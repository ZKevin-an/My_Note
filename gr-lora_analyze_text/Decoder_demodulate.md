# 1.先确定是否是header部分的内容，如果是则reduced_rate = True

# 2.输入信号时域采样值来解调出内容的二进制索引值（重要解调代码）
```
uint32_t bin_idx = max_frequency_gradient_idx(samples);
```
max_frequency_gradient_idx()函数解析:  
>(1).定义变量  
samples_ifreq用于储存信号的瞬时角频率变化梯度  
samples_ifreq_avg用于存放将瞬时角频率变化梯度进行平滑处理过后的值，相当于是一个元素包含一个连续部分的平均角频率  

>(2).将信号的时域采样值转换成瞬时角频率变化梯度储存在samples_ifreq中  


>(3).将频域采样值以一位一段的方式分隔开并取每一段的平均值放入samples_ifreq_avg中  

>(4).从这些平均段中找到变化率最大的地方，并记录下来，根据这个位置信息算出对应的二进制索引值并返回  
```
uint32_t decoder_impl::max_frequency_gradient_idx(const gr_complex *samples) {
    float samples_ifreq[d_samples_per_symbol];
    float samples_ifreq_avg[d_number_of_bins];

    samples_to_file("/tmp/data", &samples[0], d_samples_per_symbol, sizeof(gr_complex));

    instantaneous_frequency(samples, samples_ifreq, d_samples_per_symbol);

    for(uint32_t i = 0; i < d_number_of_bins; i++) {
        volk_32f_accumulator_s32f(&samples_ifreq_avg[i], &samples_ifreq[i*d_decim_factor], d_decim_factor);
        samples_ifreq_avg[i] /= d_decim_factor;
    }

    float max_gradient = 0.1f;
    float gradient = 0.0f;
    uint32_t max_index = 0;
    for (uint32_t i = 1u; i < d_number_of_bins; i++) {
        gradient = samples_ifreq_avg[i - 1] - samples_ifreq_avg[i];
        if (gradient > max_gradient) {
            max_gradient = gradient;
            max_index = i+1;
        }
    }

    return (d_number_of_bins - max_index) % d_number_of_bins;
}
```

# 3.如果打开了同步模式，则进行一次fine_sync（之前提到过这个函数），相当于是在一个比较小的跳动范围内（std::max(d_decim_factor / 4u, 2u)）找到矫正值
```
if(d_enable_fine_sync)
    fine_sync(samples, bin_idx, std::max(d_decim_factor / 4u, 2u));
```

# 4.如果是header信息（及存在冗余），将冗余去除
```
// Header has additional redundancy
if (reduced_rate) {
    bin_idx = std::lround(bin_idx / 4.0f) % d_number_of_bins_hdr;
}
```

# 5.将二进制索引值解码（decode，实际上只解码了一步，还需要经过很多处理），并将这个解码值放入words队列中
```
// Decode (actually gray encode) the bin to get the symbol value
const uint32_t word = bin_idx ^ (bin_idx >> 1u);
```

# 6.判断解码（gray decode）后的信息是否满足cr，如果满足则进行解交织的操作并返回True表示解调完成，不满足说明words队列中的内容不够还需要继续收集返回false
```
if (d_words.size() == (4u + (is_first ? 4u : d_phdr.cr))) {
    // Deinterleave
    deinterleave(reduced_rate ? d_sf - 2u : d_sf);

    return true; // Signal that a block is ready for decoding
}
return false;
```
void decoder_impl::deinterleave(const uint32_t ppm):  
>解交织函数的内容感觉可以直接移植，相当于是将操作完后的值放入到demodulated队列中（一个新的队列），并将words(操作前的队列)队列清除
```
void decoder_impl::deinterleave(const uint32_t ppm) {
    const uint32_t bits_per_word = d_words.size();
    const uint32_t offset_start  = ppm - 1u;

    std::vector<uint8_t> words_deinterleaved(ppm, 0u);

    if (bits_per_word > 8u) {
        // Not sure if this can ever occur. It would imply coding rate high than 4/8 e.g. 4/9.
        exit(1);
    }

    for (uint32_t i = 0u; i < bits_per_word; i++) {
        const uint32_t word = gr::lora::rotl(d_words[i], i, ppm);
        for (uint32_t j = (1u << offset_start), x = offset_start; j; j >>= 1u, x--) {
            words_deinterleaved[x] |= !!(word & j) << i;
        }
    }

    #ifdef GRLORA_DEBUG
        print_interleave_matrix(d_debug, d_words, ppm);
        print_vector_bin(d_debug, words_deinterleaved, "D", sizeof(uint8_t) * 8u);
    #endif

    // Add to demodulated data
    d_demodulated.insert(d_demodulated.end(), words_deinterleaved.begin(), words_deinterleaved.end());

    // Cleanup
    d_words.clear();
}
```

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  


