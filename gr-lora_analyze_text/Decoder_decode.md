```
代码：
void decoder_impl::decode(const bool is_header) {
    static const uint8_t shuffle_pattern[] = {5, 0, 1, 2, 4, 3, 6, 7};

    // For determining shuffle pattern
    deshuffle(shuffle_pattern, is_header);

    // For determining whitening sequence
    dewhiten(is_header ? gr::lora::prng_header :
            (d_phdr.cr <=2) ? gr::lora::prng_payload_cr56 : gr::lora::prng_payload_cr78);

    hamming_decode(is_header);
}
```

# 1.设置shuffle_pattern数组（这个相当于已经是已知项了，密钥），用于解shuffle（deshuffle）

以下部分都是直接对已经从信号采样值变为二进制信息的数据进行处理，这一部分就是单纯的数组转换问题了，按道理来说应该可以直接移植代码，所以没有做过多的理解（需要去查查原理）

# 2.解shuffle：
void decoder_impl::deshuffle(const uint8_t * shuffle_pattern, const bool is_header):   
解shuffle方式因为没有涉及其他的操作，都是对数组进行直接的操作，所以可以直接移植，最主要的操作就是处理完d_demodulated队列之后，存入d_words_deshuffled中
```
void decoder_impl::deshuffle(const uint8_t *shuffle_pattern, const bool is_header) {
    const uint32_t to_decode = is_header ? 5u : d_demodulated.size();
    const uint32_t len       = sizeof(shuffle_pattern) / sizeof(uint8_t);
    uint8_t result;

    for (uint32_t i = 0u; i < to_decode; i++) {
        result = 0u;

        for (uint32_t j = 0u; j < len; j++) {
            result |= !!(d_demodulated[i] & (1u << shuffle_pattern[j])) << j;
        }

        d_words_deshuffled.push_back(result);
    }

    #ifdef GRLORA_DEBUG
        print_vector_bin(d_debug, d_words_deshuffled, "S", sizeof(uint8_t)*8);
    #endif

    // We're done with these words
    if (is_header){
        d_demodulated.erase(d_demodulated.begin(), d_demodulated.begin() + 5u);
        d_words_deshuffled.push_back(0);
    } else {
        d_demodulated.clear();
    }
}
```

# 3.解dewhiten：
void decoder_impl::dewhiten(const uint8_t * prng)：  
和shuffle相似，也是对数组进行操作，然后将d_words_deshuffled操作完的结果放入d_words_dewhitened的队列中  
```
void decoder_impl::dewhiten(const uint8_t *prng) {
    const uint32_t len = d_words_deshuffled.size();
    for (uint32_t i = 0u; i < len; i++) {
        uint8_t xor_b = d_words_deshuffled[i] ^ prng[i];
        d_words_dewhitened.push_back(xor_b);
    }
    #ifdef GRLORA_DEBUG
        print_vector_bin(d_debug, d_words_dewhitened, "W", sizeof(uint8_t) * 8);
    #endif
    d_words_deshuffled.clear();
}
```

# 4.解汉明码：
同上，其中ceil()：返回大于或等于一个给定数字的最小整数，fec_decode是gnuradio上已有的函数，可以直接使用。
```
void decoder_impl::hamming_decode(bool is_header) {
    switch(d_phdr.cr) {
        case 4: case 3: { // Hamming(8,4) or Hamming(7,4)
            //hamming_decode_soft(is_header);
            uint32_t n = ceil(d_words_dewhitened.size() * 4.0f / (4.0f + d_phdr.cr));
            uint8_t decoded[n];

            fec_decode(d_h48_fec, n, &d_words_dewhitened[0], decoded);
            if(!is_header)
                swap_nibbles(decoded, n);
            d_decoded.assign(decoded, decoded+n);
            break;
        }
        case 2: case 1: { // Hamming(6,4) or Hamming(5,4)
            // TODO: Report parity error to the user
            extract_data_only(is_header);
            break;
        }
    }

    d_words_dewhitened.clear();
}
```
ceil：返回大于或等于一个给定数字的最小整数  
swap_nibbles：将数组内每个字节的前四位和后四位交换  
```
inline void swap_nibbles(uint8_t* array, uint32_t length) {
    for(uint32_t i = 0; i < length; i++) {
        array[i] = ((array[i] & 0x0f) << 4) | ((array[i] & 0xf0) >> 4);
    }
}
```
extract_data_only：仅提取给定字节中的数据  
```
void decoder_impl::extract_data_only(bool is_header) {
    static const uint8_t data_indices[4] = {1, 2, 3, 5};
    uint32_t len = d_words_dewhitened.size();

    for (uint32_t i = 0u; i < len; i += 2u) {
        const uint8_t d2 = (i + 1u < len) ? select_bits(d_words_dewhitened[i + 1u], data_indices, 4u) & 0xFF : 0u;
        const uint8_t d1 = (select_bits(d_words_dewhitened[i], data_indices, 4u) & 0xFF);

        if(is_header)
            d_decoded.push_back((d1 << 4u) | d2);
        else
            d_decoded.push_back((d2 << 4u) | d1);
    }
}
```
select_bits：提取出由indices中的索引值给出的数据中的位  
```
/**
*  \brief  Select the bits in data given by the indices in `*indices`.
*
*  \param  data
*          The data to select bits from.
*  \param  *indices
*          Array with the indices to select.
*  \param  n
*          The amount of indices.
*/
inline uint32_t select_bits(const uint32_t data, const uint8_t *indices, const uint8_t n) {
    uint32_t r = 0u;
    for(uint8_t i = 0u; i < n; ++i)
        r |= (data & (1u << indices[i])) ? (1u << i) : 0u;
    return r;
}
```

# 代码原理
交织（nterleaving）：为了避免突发噪声对信号的干扰，需要通过交织（nterleaving）来将每个bit的顺序进行打乱，不过这种打乱是可逆的。    
白化（whitened）：keepthe data Direct Current (DC)-free
汉明编码（Hamming）：用于对数据检错和纠错

详细原理可见：“A Multi-Channel Software Decoder for the LoRa Modulation Scheme” 论文的 __2.3__ 节和 __3.4__ 节

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  