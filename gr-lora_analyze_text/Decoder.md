# Decoder模块：
	> 输入：1或-1个gr_complex端口  
	> 输出：无输出端口（不包含msg输出）  
```
代码：
decoder_impl::decoder_impl(float samp_rate, uint32_t bandwidth, uint8_t sf, bool implicit, uint8_t cr, bool crc, bool reduced_rate, bool disable_drift_correction)
            : gr::sync_block("decoder",
                             gr::io_signature::make(1, -1, sizeof(gr_complex)),
                             gr::io_signature::make(0, 0, 0)),
            d_pwr_queue(MAX_PWR_QUEUE_SIZE)
```
# 初始化：
1. 设置DecoderState：_DETECT_ （检测阶段）  
```
代码：
d_state = gr::lora::DecoderState::DETECT;
```
> 说明：__decoder__ 一共有 __7__ 个状态 
>``` 
>代码：
>enum class DecoderState {
>    DETECT,
>    SYNC,
>    FIND_SFD,
>    PAUSE,
>    DECODE_HEADER,
>    DECODE_PAYLOAD,
>    STOP
>};
>```
>__decoder__ 的工作方式是不断通过 __switch__ 来判断自己处在什么状态下，然>后执行对应状态下的代码并且满足条件后更改状态，例如：  
>```
>代码：
>switch(d_state){
>	case DETECT:
>	case SYNC:
>	case FIND_SFD:
>	....
>}
>```
***
2. 判断sf是否在范围内6~12。
```
代码：
if (sf < 6 || sf > 13) {
    std::cerr << "[LoRa Decoder] ERROR : Spreading factor should be between 6 and 12 (inclusive)!" << std::endl
    << "Other values are currently not supported." << std::endl;
    exit(1);
}
```
***
3. 参数赋值
由于参数过多，后面使用到再一一分析
```
d_bw= bandwidth;
d_implicit           = implicit;
d_reduced_rate       = reduced_rate;
d_phdr.cr            = cr;
d_phdr.has_mac_crc   = crc;
d_samples_per_second = samp_rate;
d_payload_symbols    = 0;
d_cfo_estimation     = 0.0f;
d_dt                 = 1.0f / d_samples_per_second;
d_sf                 = sf;
d_bits_per_second    = (double)d_sf * (double)(4.0 / (4.0 + d_phdr.cr)) / (1u << d_sf) * d_bw;
d_symbols_per_second = (double)d_bw / (1u << d_sf);
d_period             = 1.0f / (double)d_symbols_per_second;
d_bits_per_symbol    = (double)(d_bits_per_second    / d_symbols_per_second);
d_samples_per_symbol = (uint32_t)(d_samples_per_second / d_symbols_per_second);
d_delay_after_sync   = d_samples_per_symbol / 4u;
d_number_of_bins     = (uint32_t)(1u << d_sf);
d_number_of_bins_hdr = (uint32_t)(1u << (d_sf-2));
d_decim_factor       = d_samples_per_symbol / d_number_of_bins;
d_energy_threshold   = 0.0f;
d_fine_sync = 0;
d_enable_fine_sync = !disable_drift_correction;
set_output_multiple(2 * d_samples_per_symbol);
```


# 生成本地理想chirp
生成本地理想的upchirp和downchirp用于后面的解调
```
代码：
// Locally generated chirps
build_ideal_chirps();
```
函数解析索引：[Locally generated chirps](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Locally_generated_chirps.md)

# FFT准备：
设置好FFT的窗口类型和窗口大小，并设置fft_create_plan
```
代码：
// FFT decoding preparations
d_fft.resize(d_samples_per_symbol);
d_mult_hf.resize(d_samples_per_symbol);
d_tmp.resize(d_number_of_bins);
d_q  = fft_create_plan(d_samples_per_symbol, &d_mult_hf[0], &d_fft[0],LIQUID_FFT_FORWARD, 0);
d_qr = fft_create_plan(d_number_of_bins,&d_tmp[0], &d_mult_hf[0], LIQUID_FFT_BACKWARD, 0);
```
函数解析索引：[FFT decoding preparations](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/FFT_decoding_preparations.md)

# 汉明解码：
设置前向纠错编码方式——4/8汉明码
```
代码：
// Hamming coding
fec_scheme fs = LIQUID_FEC_HAMMING84;
d_h48_fec = fec_create(fs, NULL);
```
# Work函数
在模块定义完之后，信号流就会通过 __input__ 端口输入，然后 __decoder__ 模块则根据其 __work__ 函数去处理信号流，并将处理好的结果从 __output__ 端口输出（当然可能没有 __output__ 端口，该decoder就是通过msg端口来输出解调解码后的信息）   
work函数索引：[Decoder_work](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_work.md)

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  