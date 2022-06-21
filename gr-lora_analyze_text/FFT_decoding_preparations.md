# 1.对fft参数大小进行设置
道理都在这里，但是这个是用来干嘛的，还没看懂~~
```
d_fft.resize(d_samples_per_symbol);
d_mult_hf.resize(d_samples_per_symbol);
d_tmp.resize(d_number_of_bins);
```
d_fft：储存fft结果的数组  
d_mult_hf：储存一维fft结果的数组  
d_tmp：储存一维逆fft结果的数组  

# 2.设置fft_plan？？？
同上
```
d_q  = fft_create_plan(d_samples_per_symbol, &d_mult_hf[0], &d_fft[0], LIQUID_FFT_FORWARD, 0);
d_qr = fft_create_plan(d_number_of_bins, &d_tmp[0], &d_mult_hf[0], LIQUID_FFT_BACKWARD, 0);
```
LIQUID_FFT_FORWARD  =  +1,  // complex one-dimensional FFT  
LIQUID_FFT_BACKWARD =  -1,  // complex one-dimensional inverse FFT

# 注意
虽然设置了FFT，但是后面再阅读代码和论文的过程中发现，该decoder并未用到FFT，所以这段可以不要。

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  