# 1.通过Schmidl-Cox autocorrelation方法来检测采样值与preamble的相关度
detect_preamble_autocorr(const gr_complex *samples, const uint32_t window)函数解析:  
(1).参数定义和设置
>定义两个gr_complex数据形式的chirp时域采样值序列chirp1，chirp2(chirp2存放chirp1下一个chirp的时域采样值)  
>定义magsq_chirp1和magsq_chirp2的数组，长度都为一个chirp时域采样值  
>定义energy_chirp1,energy_chirp2，用于记录一个chirp包含的能量
autocorr：相关度
```
代码：
const gr_complex* chirp1 = samples;
const gr_complex* chirp2 = samples + d_samples_per_symbol;
float magsq_chirp1[window];
float magsq_chirp2[window];
float energy_chirp1 = 0;
float energy_chirp2 = 0;
float autocorr = 0;
gr_complex dot_product;
```

(2).数组处理
>计算chirp1和chirp2的共轭点积（conjugate dot product），并将结果放入dot_product中  
>计算chirp1的幅度平方（magnitude squared），并将结果放入magsq_chirp1中  
>计算chirp2的幅度平方（magnitude squared），并将结果放入magsq_chirp2中  
>累加magsq_chirp1的每一个值，将结果放入energy_chirp1  
>累加magsq_chirp2的每一个值，将结果放入energy_chirp2  
```
代码：
volk_32fc_x2_conjugate_dot_prod_32fc(&dot_product, chirp1, chirp2, window);
volk_32fc_magnitude_squared_32f(magsq_chirp1, chirp1, window);
volk_32fc_magnitude_squared_32f(magsq_chirp2, chirp2, window);
volk_32f_accumulator_s32f(&energy_chirp1, magsq_chirp1, window);
volk_32f_accumulator_s32f(&energy_chirp2, magsq_chirp2, window);
```

(3).计算参数值

>d_energy_threshold：能量阈值，用于判决implicit mode下stop的情况  
>d_pwr_queue：用于后面计算snr  
>autocorr：计算相关度  
```
代码：
// When using implicit mode, stop when energy is halved.
d_energy_threshold = energy_chirp2 / 2.0f;

// For calculating the SNR later on
d_pwr_queue.push_back(energy_chirp1 / d_samples_per_symbol);

// Autocorr value
autocorr = abs(dot_product / gr_complex(sqrt(energy_chirp1 * energy_chirp2), 0));

return autocorr;
```

# 2.如果相关度大于0.9：
(1).计算信噪比：（determine_snr()函数解析）
>当d_pwr_queue队列中有大于或等于2两个参数时，计算信噪比。  
>上述detect_preamble_autocorr函数中可以看到，将计算到的信号能量放入d_pwr_queue队列的末尾，则可看出应将获得的噪声放入队列的前端   
```
代码
void decoder_impl::determine_snr() {
    if(d_pwr_queue.size() >= 2) {
        float pwr_noise = d_pwr_queue[0];
        float pwr_signal = d_pwr_queue[d_pwr_queue.size()-1];
        d_snr = pwr_signal / pwr_noise;
    }
}
```

(2).d_corr_fails置0：
>这个参数用于储存计算相关度失败（后面步骤可见，当相关度小于某个值的时候）的次数，当这个次数到达一定值的时候，将decoder模块的状态重置为DETECT

(3).将d_state设置为SYNC

# 代码原理
论文中原理（Schmidl-Cox algorithm）：一个symbol下所有时域采样值的平方的总和与另一个symbol下的时域采样值的共轭与该symbol下时域采样值的乘积的平方的总和进行相除，得到的值即为相关值。当这个值最大时表明检测到了重复的upchirp。  
（该算法是利用了preamble的重复性，也就是不断连续的upchirp）  
__但是代码中的实现过程和论文中的描述好像不一致，不过都能实现效果__  
详细原理可见：“A Multi-Channel Software Decoder for the LoRa Modulation Scheme” 论文的 __3.1__ 节

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)    
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)    