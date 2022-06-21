# 1.参数配置
**将建立decoder，channelizer和resampler模块所需要的参数定义好**
```
代码：
# Parameters
self.samp_rate     = samp_rate
self.center_freq   = center_freq
self.channel_list  = channel_list
self.bandwidth     = bandwidth
self.sf            = sf
self.implicit      = implicit
self.cr            = cr
self.crc           = crc
self.decimation    = decimation
self.conj          = conj
self.disable_channelization = disable_channelization
self.disable_drift_correction = disable_drift_correction
```

* 参数注明：  
    > decimation：设置重采样模块（gnuradio.filter.fractional_resampler_cc）的重要参数————压缩率  
    > decimation（The resampling ratio） = input_rate / output_rate.  
    > conj：是否要对模块的输入/输出进行共轭的标志，主要用于自定义函数   


# 2.模块定义
**将所需的三种模块定义好**
```
代码：
# Define blocks
        self.block_conj = gnuradio.blocks.conjugate_cc()
        self.channelizer = lora.channelizer(samp_rate, center_freq, channel_list, bandwidth, decimation)
        self.decoder = lora.decoder(samp_rate / decimation, bandwidth, sf, implicit, cr, crc, reduced_rate, disable_drift_correction)
```
* 模块介绍：
    > block_conj：共轭转换器，用于将输入的信号进行共轭转换输出  
    > channelizer：信道化模块，channelizer文档可见，实际上是一个低通滤波器  
    > resampler：重采样模块（与信道化模块之间二选一与decoder相连，默认未用到），用于重新改变采样率大小

# 3.模块连接
**选择是将重采样器与解码器连接，或信道化模块与解码器连接，以及中间是否要加共轭转换器**
```
代码：
# Connect blocks
        if self.disable_channelization:
            self.resampler = gnuradio.filter.fractional_resampler_cc(0, float(decimation))
            self.connect((self, 0), (self.resampler, 0))
            self._connect_conj_block_if_enabled(self.resampler, self.decoder)
        else:
            self.connect((self, 0), (self.channelizer, 0))
            self._connect_conj_block_if_enabled(self.channelizer, self.decoder)
            self.msg_connect((self.decoder, 'control'), (self.channelizer, 'control'))
```
* 函数介绍：
    > _connect_conj_block_if_enabled（），如果conj=1，则在两模块间插入一个共轭转换器，否则不插入  
    
    > disable_channelization=False(默认，表示在解调器前放置一个信道化模块):  
    > channelizer ->  (block_conj) -> decoder  
    
    > disable_channelization=True（表示在解调器前面放置一个重采样模块）:  
    > resampler -> (block_conj) -> decoder  

    > 参数：channel_list------不明确  
    > 在C++中的数据类型为：std::vector<float>

# 4.模块解析跳转
信道化模块：[Channelizer](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Channelizer.md)  
解码器：[Decoder](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder.md)

# 源代码定位
[rpp0/gr-lora/python/lora_receiver.py](https://github.com/rpp0/gr-lora/blob/master/python/lora_receiver.py)   