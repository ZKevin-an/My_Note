# 1.先对upchirp和downchirp的大小进行设置
在已建立（初始化时）的理想chirp数组（存放理想chirp的时域采样值）中先规定好时域采样值和瞬时角频率变化梯度数组的点数。
```
代码：
d_downchirp.resize(d_samples_per_symbol);
d_upchirp.resize(d_samples_per_symbol);
d_downchirp_ifreq.resize(d_samples_per_symbol);
d_upchirp_ifreq.resize(d_samples_per_symbol);
d_upchirp_ifreq_v.resize(d_samples_per_symbol*3);
gr_complex tmp[d_samples_per_symbol*3];
```
代码说明：
1. __upchirp__ 和 __downchirp__ 数据类型都为 __std::vector<gr_complex>__ ，也就是一个chirp的一系列时域上的采样值（信号幅值）
2. __d_downchirp_ifreq__ 和 __d_upchirp_ifreq__ 的数据类型为 __std::vector float__ ，是用于存放一个 __chirp__ 的瞬时角频率变化梯度的数组    
3. __d_upchirp_ifreq_v__ 的数据类型为 __std::vector float__ ，是 __3__ 个 __chirp__ 的瞬时角频率变化梯度的数组    
4. __uint32_t d_samples_per_symbol__ ：每个 __chirp__ 的采样点数量
5. 同时还生成了一个能够包含三个 __chirp__ 采样值的数组：__tmp__

# 2.计算参数并生成理想chirp采样值
计算好生成理想chirp的参数，并通过计算公式生成理想chirp每个时域采样点的值
```
代码：
const double T       = -0.5 * d_bw * d_symbols_per_second;
const double f0      = (d_bw / 2.0);
const double pre_dir = 2.0 * M_PI;
 double t;
gr_complex cmx       = gr_complex(1.0f, 1.0f);

for (uint32_t i = 0u; i < d_samples_per_symbol; i++) {
    // Width in number of samples = samples_per_symbol
    // See https://en.wikipedia.org/wiki/Chirp#Linear
    t = d_dt * i;
    d_downchirp[i] = cmx * gr_expj(pre_dir * t * (f0 + T * t));
    d_upchirp[i]   = cmx * gr_expj(pre_dir * t * (f0 + T * t) * -1.0f);
}
```
__主要计算公式__ ：d_downchirp[i] = cmx * gr_expj(pre_dir * t * (f0 + T * t));

# 3.将产生的理想chirp时域采样值转换成该chirp的数字梯度数组并储存成文件
通过使用 __instantaneous_frequency__ 函数将输入信号的时域采样值转换成瞬时角频率变化梯度数组，__instantaneous_frequency__ 函数代码虽然好理解，但是原理不明！
```
代码：
//Store instantaneous frequency
instantaneous_frequency(&d_downchirp[0], &d_downchirp_ifreq[0], d_samples_per_symbol);
instantaneous_frequency(&d_upchirp[0], &d_upchirp_ifreq[0],d_samples_per_symbol);

samples_to_file("/tmp/downchirp", &d_downchirp[0], d_downchirp.size(), sizeof(gr_complex));
samples_to_file("/tmp/upchirp", &d_upchirp[0], d_upchirp.size(),   sizeof(gr_complex));
```
    其中instantaneous_frequency用于将信号的时域采样值转换成瞬时角频率变化梯度，函数具体实现如下：
    代码：
    (1).判断采样值至少大于1个
    代码：
    if (window < 2u) {
        std::cerr << "[LoRa Decoder] WARNING : window size < 2 !" << std::endl;
        return;
    }
    (2).计算每个采样点的瞬时角频率，然后将前后两个采样点之间的瞬时角频率控制在-2π~2π之间，最后得到相邻两个采样点瞬时角频率之差并放入数组中（原理没说通）
    /* instantaneous_phase */
    for (uint32_t i = 1u; i < window; i++) {
        const float iphase_1 = std::arg(in_samples[i - 1]);
        float iphase_2 = std::arg(in_samples[i]);

        // Unwrapped loops from liquid_unwrap_phase
        while ( (iphase_2 - iphase_1) >  M_PI ) iphase_2 -= 2.0f*M_PI;
        while ( (iphase_2 - iphase_1) < -M_PI ) iphase_2 += 2.0f*M_PI;

        out_ifreq[i - 1] = iphase_2 - iphase_1;
    }
    (3).预防错误访问导致梯度过大
    // Make sure there is no strong gradient if this value is accessed by mistake
    out_ifreq[window - 1] = out_ifreq[window - 2];

# 4.同样的方式生成了连续三个upchirp的时域采样值和瞬时角频率变化梯度，并将频域采样值放在了d_upchirp_ifreq_v中
``` 
// Upchirp sequence
memcpy(tmp, &d_upchirp[0], sizeof(gr_complex) * d_samples_per_symbol);
memcpy(tmp+d_samples_per_symbol, &d_upchirp[0], sizeof(gr_complex) * d_samples_per_symbol);
memcpy(tmp+d_samples_per_symbol*2, &d_upchirp[0], sizeof(gr_complex) * d_samples_per_symbol);
instantaneous_frequency(tmp, &d_upchirp_ifreq_v[0], d_samples_per_symbol*3);
```

# 代码原理
该实现过程是作者提出的不需要经过FFT而实现解调，或者是检测是否为upchirp或downchirp信号的方法。  
方法步骤：  
1.计算每个采样点的瞬时角频率  
2.根据抽取因子来平滑这个瞬时角频率（相当于取了个平均值，但是在检测upchirp和downchirp中没有进行这个平滑步骤）  
3.将每两个相邻点的瞬时角频率相减，获得一个角频率的变化值并存入数组中（把这个数组称为瞬时角频率变化梯度数组）  
4.检测upchirp和downchirp：将这个瞬时角频率变化梯度数组和理想信号的瞬时角频率变化梯度数组做滑动相关计算，如果相关值大于一定值，则表明该信号为upchirp或downchirp    
解调：这个瞬时角频率变化梯度数组在某一个时间点上是负值，根据这个负值的位置则就能算出对应的二进制值。

详细原理可见：“A Multi-Channel Software Decoder for the LoRa Modulation Scheme” 论文的 __3.2__ 节

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  