# work函数的输入
   > noutput_items: 输出的种类数  
   > input_items: 用于处理的采样数组（时域）  
   > output_items: 处理过后的数组  
```
代码：
int decoder_impl::work(int noutput_items,
                       gr_vector_const_void_star& input_items,
                       gr_vector_void_star&       output_items) {
   (void) noutput_items;
   (void) output_items;

   const gr_complex *input     = (gr_complex *) input_items[0];
```

# 初始化：
同步标志位=0
```
代码：
d_fine_sync = 0; // Always reset fine sync
```

# 状态轮询进入(switch):
通过 __switch__ 来判断 __decoder__ 的状态，根据状态跳转到对应的代码并执行。 __decoder__ 的工作方式（代码本质）就是循环判断在哪个状态并执行。
```
代码：
enum class DecoderState {
   DETECT,
   SYNC,
   FIND_SFD,
   PAUSE,
   DECODE_HEADER,
   DECODE_PAYLOAD,
   STOP
};
```
# DETECT:
1.通过 __Schmidl-Cox autocorrelation__ 方法来检测采样值与 __preamble__ 的相关度  
2.如果相关度大于0.9：计算出信噪比，将 __d_corr_fails__ 的值置0并将状态变为 __SYNC__ 。
否则无操作  
代码详细解析：[Decoder_DETECT](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_DETECT.md) 
```
代码：
case gr::lora::DecoderState::DETECT: {
   float correlation = detect_preamble_autocorr(input, d_samples_per_symbol);

   if (correlation >= 0.90f) {
      determine_snr();
      #ifdef GRLORA_DEBUG
         d_debug << "Ca: " << correlation << std::endl;
      #endif
      d_corr_fails = 0u;
      d_state = gr::lora::DecoderState::SYNC;
      break;
   }

   consume_each(d_samples_per_symbol);

   break;
}
```
   
# SYNC：
1.检测 __upchirp__  
2.将时域采样值都放入 __/tmp/detect__ 中  
3.将 __d_state__ 设置成 __FIND_SFD__  
代码详细解析：[Decoder_SYNC](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_SYNC.md) 
```
代码：
case gr::lora::DecoderState::SYNC: {
   int i = 0;
   detect_upchirp(input, d_samples_per_symbol, &i);

   samples_to_file("/tmp/detect", &input[i], d_samples_per_symbol, sizeof(gr_complex));

   consume_each(i);
   d_state = gr::lora::DecoderState::FIND_SFD;
   break;
}
```

# FIND_SFD：
1.检测 __downchirp__  
2.如果相关因子大于0.96：将时域采样值放入 __/tmp/sync__ 并将 __d_state__ 设置为PAUSE  
3.如果相关因子小于-0.97： __fine_sync__ （用于设置 __drift correction__ ，矫正下一个 __chirp__ 的检测）  
4.如果既不大于0.96也不小于-0.97： __d_corr_fails++__ （前面提到过）  
5.当 __d_corr_fails__ 大于4，将 __d_state__ 设置为 __DETECT__ （重新启动）  
代码详细解析：[Decoder_FIND_SFD](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_FIND_SFD.md)   
```
代码：
case gr::lora::DecoderState::FIND_SFD: {
   const float c = detect_downchirp(input, d_samples_per_symbol);

   #ifdef GRLORA_DEBUG
      d_debug << "Cd: " << c << std::endl;
   #endif

   if (c > 0.96f) {
      #ifdef GRLORA_DEBUG
         d_debug << "SYNC: " << c << std::endl;
      #endif
      // Debug stuff
      samples_to_file("/tmp/sync", input, d_samples_per_symbol, sizeof(gr_complex));

      d_state = gr::lora::DecoderState::PAUSE;
   } else {
      if(c < -0.97f) {
      // TODO: Check d_upchirp_ifreq_v: bin -1 gives different result compared to bin d_number_of_bins-1, which shouldn't be the case.
      fine_sync(input, -1, d_decim_factor * 4);
      } else {
         d_corr_fails++;
      }

      if (d_corr_fails > 4u) {
         d_state = gr::lora::DecoderState::DETECT;
         #ifdef GRLORA_DEBUG
            d_debug << "Lost sync" << std::endl;
         #endif
      }
   }

   consume_each((int32_t)d_samples_per_symbol+d_fine_sync);
   break;
}
```

# PAUSE：
直接将 __d_state__ 设置为 __DECODE_HEADER__ （没有其他操作，可能是延时作用？）
```
代码：
case gr::lora::DecoderState::PAUSE: {
   d_state = gr::lora::DecoderState::DECODE_HEADER;
   consume_each(d_samples_per_symbol + d_delay_after_sync);
   break;
}
```

# DECODE_HEADER：  
1.对输入进行解调（ __demodulate,header_flag = True__ ）  
2.如果解调成功（返回 __True__ ），则先判断是否为 __implicit__ 模式，如果是则直接将 __d_state__ 置为 __DECODE_PAYLOAD__ ，否则进入下一步  
3.对解调好的信息队列（ __d_words__ ）进行解码（ __decoder（header_flag = True）__ ），把信息输出，并收集后面 __payload__ 的相关参数，将 __d_state__ 置为 __DECODE_PAYLOAD__    

   > _DECODE_HEADER和DECODE_PAYLOAD实际上都是对信号进行解调和解码的过程，其原理都是对输入信号的时域采样值数组进行demodulate和decode操作，然后将解出的信息发送出去。所以后面我们就单独对解调和解码两部分进行详细的分析，其他部分代码不复杂，可直接移植。_   
   >代码详细解析：[Decoder_demodulate](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_demodulate.md)   
   >代码详细解析：[Decoder_decode](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_decode.md) 
```
代码：
case gr::lora::DecoderState::DECODE_HEADER: {
   if (demodulate(input, true)) {
      if (d_implicit) {
         d_payload_symbols = 1;
      } else {
         decode(true);
         gr::lora::print_vector_hex(std::cout, &d_decoded[0], d_decoded.size(), false, false);
         memcpy(&d_phdr, &d_decoded[0], sizeof(loraphy_header_t));
         if (d_phdr.cr > 4)
            d_phdr.cr = 4;
         d_decoded.clear();

         d_payload_length = d_phdr.length + MAC_CRC_SIZE * d_phdr.has_mac_crc;
         // Calculate number of payload symbols needed
         uint8_t redundancy = (d_reduced_rate ? 2 : 0);
         const int symbols_per_block = d_phdr.cr + 4u;
         const float bits_needed     = float(d_payload_length) * 8.0f;
         const float symbols_needed  = bits_needed * (symbols_per_block / 4.0f) / float(d_sf - redundancy);
         const int blocks_needed  = (int)std::ceil(symbols_needed / symbols_per_block);
         d_payload_symbols = blocks_needed * symbols_per_block;

         #ifdef GRLORA_DEBUG
            d_debug << "LEN: " << d_payload_length << " (" << d_payload_symbols << " symbols)" << std::endl;
         #endif
      }
      d_state = gr::lora::DecoderState::DECODE_PAYLOAD;
   }

   consume_each((int32_t)d_samples_per_symbol+d_fine_sync);
   break;
}
```

# DECODE_PAYLOAD：
1.首先判断是否为 __implicit__ 模式且输入信号的能量是否小于 __energy_threshold__ ，如果是则直接将 __d_payload_symbols__ 置0，并计算 __payload__ 的长度  
2.如果不是 __implicit__ 模式或者能量值大于 __energy_threshold__ ，对信号进行解调（ __demodulate（header_flag = False）__ ）,并且如果不是 __implicit__ 模式，重新计算 __payload_symbols__  
3.当 __payload_symbols__ 小于0（为什么不是大于0？？？），解码并输出解码内容，并将 __d_state__ 置位 __DETECT__ （重新开始），将之前建立的所有队列清空  
```
代码：
case gr::lora::DecoderState::DECODE_PAYLOAD: {
   if (d_implicit && determine_energy(input) < d_energy_threshold) {
      d_payload_symbols = 0;
      // Test for SF 8 with header
      d_payload_length = (int32_t)(d_demodulated.size() / 2);
   } else if (demodulate(input, false)) {
      if(!d_implicit)
         d_payload_symbols -= (4u + d_phdr.cr);
   }

   if (d_payload_symbols <= 0) {
      decode(false);
      gr::lora::print_vector_hex(std::cout, &d_decoded[0], d_payload_length, true, true);
      msg_lora_frame();

      d_state = gr::lora::DecoderState::DETECT;
      d_decoded.clear();
      d_words.clear();
      d_words_dewhitened.clear();
      d_words_deshuffled.clear();
      d_demodulated.clear();
   }

   consume_each((int32_t)d_samples_per_symbol+d_fine_sync);

   break;
}
```

# STOP:
无实意，进入该状态后不进行任何操作，等同于模块被暂停了
```
代码
case gr::lora::DecoderState::STOP: {
   consume_each(d_samples_per_symbol);
   break;
}
```

# 源代码定位
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  