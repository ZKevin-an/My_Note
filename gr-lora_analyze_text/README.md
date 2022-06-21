# 介绍
[ZKevin-an/my_Note](https://github.com/ZKevin-an/my_Note)：用于记录一些我自己写的笔记，刚刚用起来，还不熟悉  
[gr-lora](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora)：用于解读rpp0/gr-lora的代码而创建的笔记文件夹，里面包含了我对gr-lora代码的理解以及如何对它进行移植（可能我的解读会存在很多的问题与曲解）

# 阅读顺序
1. [Reciver](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Reciver.md)：作为整合的一个大模块，通过将原始信号输入该模块就能够将其包含的信息给解出来，所以从[Reciver](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Reciver.md)出发，自顶向下的来解读。
2. [channelizer](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Channelizer.md)和[Decoder](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder.md)：作为Reciver模块下的最主要的两个小模块，channelizer负责对原始信号先进行信道化处理，Decoder则是包含了整个解调与解码过程。
3. [Decoder](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder.md)和[Decoder_work](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_work.md):Decoder里面包含了建立这样一个模块需要进行怎么样的准备活动（初始化），而work里面则是包含整个解码的处理过程代码
3. Decoder_...：通过Decoder和Decoder_work的解读，可以通过他们里面的引用找到其更为底层的解析。

# 索引  
Receiver的组成：[channelizer](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Channelizer.md)+[Decoder](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder.md)   
Decoder的组成：[Decoder](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder.md)+[Decoder_work](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_work.md)  
Decoder_work的组成：[Decoder_DETECT](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_DETECT.md),[Deocder_SYNC](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Deocder_SYNC.md),[Decoder_FIND_SFD](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_FIND_SFD.md),[Decoder_demodulate](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_demodulate.md),[Decoder_decode](https://github.com/ZKevin-an/my_Note/tree/master/gr-lora/Decoder_decode.md)

# 源代码定位
源代码主要在：
[rpp0/gr-lora/python/lora_receiver.py](https://github.com/rpp0/gr-lora/blob/master/python/lora_receiver.py)  
[rpp0/gr-lora/lib/channelizer_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/channelizer_impl.cc)  
[rpp0/gr-lora/lib/channelizer_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/channelizer_impl.h)  
[rpp0/gr-lora/lib/decoder_impl.cc](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.cc)  
[rpp0/gr-lora/lib/decoder_impl.h](https://github.com/rpp0/gr-lora/blob/master/lib/decoder_impl.h)  

# 代码原理
解调，解码和同步等方法的原理可以详细见论文：A Multi-Channel Software Decoder for the LoRa Modulation Scheme