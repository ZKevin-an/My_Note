# 模块名：channelizer
	输入：一个端口的gr_complex  
	输出：channel_list.size()个端口的gr_complex  
```
代码：
channelizer_impl::channelizer_impl(float samp_rate, float center_freq, std::vector<float> channel_list, uint32_t bandwidth, uint32_t decimation)
      : gr::hier_block2("channelizer",
              gr::io_signature::make(1, 1, sizeof(gr_complex)),
              gr::io_signature::make(channel_list.size(), channel_list.size(), sizeof(gr_complex))),
        d_cfo(0.0)
```


# 模块定义
1. 设置一个FIR低通滤波器taps：d_lpf  
	> 函数名：gr::filter::firdes::low_pass  
	> gain：1.0  
	> 采样频率： 待设置（常用1MHz）  
	> 过渡带中心频率：(bandwidth/2)+15000  
	> 过渡带带宽：10000  
	> 窗口：gr::filter::firdes::WIN_HAMMING  
	> Kaiser：6.67  
```
代码：
d_lpf = gr::filter::firdes::low_pass(1.0, samp_rate, (bandwidth/2)+15000, 10000, gr::filter::firdes::WIN_HAMMING, 6.67);
```	
***
2. 设置fre_offset：channel_list[0] - center_freq（频率偏移设置） 
``` 
代码：
d_freq_offset = channel_list[0] - center_freq;
```
***
3. 创建一个FIR滤波器（使用我们上面已经定义好的d_lpf和fre_offset），相当于将起点频率（我们目前使用的是433MHz）从0Hz搬移到433MHz。  
	> 函数名：gr::filter::freq_xlating_fir_filter_ccf  
	> 抽取速率：1  
	> 滤波器taps：d_lpf  
	> 中心频率：fre_offset  
	> 采样频率：samp_rate  
```
代码：
d_xlating_fir_filter = gr::filter::freq_xlating_fir_filter_ccf::make(decimation, d_lpf, d_freq_offset, samp_rate);
```
***
4. 模块连接：  
信道化操作实质上就是使用上面这个FIR滤波器  
```
代码：
connect(self(), 0, d_xlating_fir_filter, 0);
connect(d_xlating_fir_filter, 0, self(), 0);
```
# 源代码定位
[rpp0/gr-lora/lib/channelizer_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/channelizer_impl.cc)  
[rpp0/gr-lora/lib/channelizer_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/channelizer_impl.h)  