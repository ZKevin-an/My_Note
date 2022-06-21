# 1.检测upchirp 
detect_upchirp(const gr_complex *samples, const uint32_t window, int32_t *index)函数解析：  
>(1).定义一个浮点型数组samples_ifreq，用于存放输入信号的瞬时角频率变化梯度,从window * 2 可知存放内容为两个chirp的瞬时角频率变化梯度  
>(2).使用instantaneous_frequency(在[Locally_generated_chirps](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Locally_generated_chirps.md)中介绍过)，通过时域采样值计算出瞬时角频率变化梯度数组  
>(3).计算samples_ifreq与理想upchirp的滑动相关系数  
```
代码：
float decoder_impl::detect_upchirp(const gr_complex *samples, const uint32_t window, int32_t *index) {
    float samples_ifreq[window*2];
    instantaneous_frequency(samples, samples_ifreq, window*2);

    return sliding_norm_cross_correlate_upchirp(samples_ifreq, window, index);
}
``` 
sliding_norm_cross_correlate_upchirp(const float *samples_ifreq, const uint32_t window, int32_t *index)函数解析：  
>(1).设置相关系数为0  
>(2).每次将samples_ifreq数组滑动一个值的窗口与理想upchirp_ifreq进行点积(dot product)计算出相关因子  
>(3).将最大一项的相关因子的位置和值分别记录在index和max_correlation中。  
```
代码：
float decoder_impl::sliding_norm_cross_correlate_upchirp(const float *samples_ifreq, const uint32_t window, int32_t *index) {
    float max_correlation = 0;

    // Cross correlate
    for (uint32_t i = 0; i < window; i++) {
        const float max_corr = cross_correlate_ifreq_fast(samples_ifreq + i, &d_upchirp_ifreq[0], window - 1u);

        if (max_corr > max_correlation) {
            *index = i;
            max_correlation = max_corr;
        }
    }

    return max_correlation;
}
```
# 2.将时域采样值都放入/tmp/detect中
# 3.将d_state设置成FIND_SFD

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  