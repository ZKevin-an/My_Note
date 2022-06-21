# 1.检测downchirp
```
代码：
float decoder_impl::detect_downchirp(const gr_complex *samples, const uint32_t window) {
    float samples_ifreq[window];
    instantaneous_frequency(samples, samples_ifreq, window);

    return cross_correlate_ifreq(samples_ifreq, d_downchirp_ifreq, window - 1u);
}
```
与detect_upchirp相似，只不过窗口设置成了一个chirp大小
***  
cross_correlate_ifreq()代码解析:   //用于计算实信号的相关因子    
(1).计算采样信号瞬时角频率变化梯度数组的平均值  
(2).计算理想downchirp的瞬时角频率变化梯度数组的平均值  
(3).将采样信号的瞬时角频率变化梯度标准方差和理想downchirp瞬时角频率变化梯度标准方差相乘存入sd中  
(4).计算相关因子
```
代码：
float decoder_impl::cross_correlate_ifreq(const float *samples_ifreq, const std::vector<float>& ideal_chirp, const uint32_t to_idx) {
    float result = 0.0f;

    const float average   = std::accumulate(samples_ifreq  , samples_ifreq + to_idx, 0.0f) / (float)(to_idx);
    const float chirp_avg = std::accumulate(&ideal_chirp[0], &ideal_chirp[to_idx]  , 0.0f) / (float)(to_idx);
    const float sd        =   stddev(samples_ifreq   , to_idx, average)
    * stddev(&ideal_chirp[0] , to_idx, chirp_avg);

    for (uint32_t i = 0u; i < to_idx; i++) {
        result += (samples_ifreq[i] - average) * (ideal_chirp[i] - chirp_avg) / sd;
    }

    result /= (float)(to_idx);

    return result;
}
```

# 2.如果相关因子大于0.96：将时域采样值放入/tmp/sync并将d_state设置为PAUSE

# 3.如果相关因子小于-0.97：fine_sync（用于设置drift correction，矫正下一个chirp的检测）
fine_sync()代码解析:    //用于设置drift correction，矫正下一个chirp的检测  
(1).初始化shift_ref（用于一开始的偏移量，代码上看是0）  
>samples_ifreq: 用于存放信号的瞬时角频率变化梯度  
max_correlation: 用于存放相关因子  
lag：用于记录偏移值（目的）  

(2).将信号时域采样值转换成瞬时角频率变化梯度数组instantaneous_frequency()  
(3).通过点积(dot_product)来找到最大相关度对应的偏移值(drift correction)  
```
 void decoder_impl::fine_sync(const gr_complex* in_samples, int32_t bin_idx, int32_t search_space) {
    int32_t shift_ref = (bin_idx+1) * d_decim_factor;
    float samples_ifreq[d_samples_per_symbol];
    float max_correlation = 0.0f;
    int32_t lag = 0;

    instantaneous_frequency(in_samples, samples_ifreq, d_samples_per_symbol);

    for(int32_t i = -search_space+1; i < search_space; i++) {
        float c = cross_correlate_ifreq_fast(samples_ifreq, &d_upchirp_ifreq_v[shift_ref+i+d_samples_per_symbol], d_samples_per_symbol);
        if(c > max_correlation) {
            max_correlation = c;
            lag = i;
        }
    }

    #ifdef GRLORA_DEBUG
        d_debug << "LAG : " << lag << std::endl;
    #endif

    d_fine_sync = -lag;
    //d_fine_sync = 0;
    #ifdef GRLORA_DEBUG
        d_debug << "FINE: " << d_fine_sync << std::endl;
    #endif
}

float decoder_impl::cross_correlate_ifreq_fast(const float *samples_ifreq, const float *ideal_chirp, const uint32_t window) {
    float result = 0;
    volk_32f_x2_dot_prod_32f(&result, samples_ifreq, ideal_chirp, window);//用于计算两者的点积
    return result;
}
```

# 4.如果既不大于0.96也不小于-0.97：d_corr_fails++，（前面提到过）
用于计算错误次数，当多次检测不到downchirp时，会将状态重置成DETECT模式
# 5.当d_corr_fails大于4，将d_state设置为DETECT（重新开始）

# 代码原理
SDR和LoRa收发器中的晶体振荡器固有地具有相对和未知的时钟漂移，这会导致同步时间损失。由于这些配置中的符号长度较长，因此对于长负载负载以及较高的SF而言，这尤其成问题。所以需要fine_sync来找到偏移值进行设置。  
详细原理可见：“A Multi-Channel Software Decoder for the LoRa Modulation Scheme” 论文的 __3.4__ 节


# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  