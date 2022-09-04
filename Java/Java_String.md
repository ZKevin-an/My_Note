# String 
```
1.String声明为final，不可继承
2.实现了Serializable表示字符串是支持序列化的
  实现了Comparable接口表示可以比较String大小
3.内部定义了final char[] value用于储存字符串数据
4.String代表不可变的字符序列，简称不可变性
当字符串重新赋值时/对现有字符串进行连接操作/调用String的replace()修改指定字符或字符串时，需要重写指定内存区域赋值，不能使用原有的value进行赋值
5.通过字面量的方式给一个字符串赋值，此时字符串值声明在字符串常量池中
6.字符串常量池中是不会储存相同内容的字符串
7.String s = new String("abc");创建对象，在内存中创建了几个对象？
  两个：一个是堆空间中new结构，另一个是char[]对应的常量池中的数据"abc"
8.对String调用intern()方法，返回值就在常量池中
```

# String常用方法
```
......
```

# StringBuffer和StringBuilder
```
StringBuffer：可变的字符序列，线程安全，效率低（比String高）底层使用char[]储存
StringBuilder：可变的字符序列，jdk5.0新增，线程不安全，效率高，底层使用char[]储存
源码分析：
StringBuffer sb = new StringBuffer(); // char[] value = new char[16];
StringBuffer sb2 = new StringBuffer("abc"); // char[] value = new char["abc".length + 16];
其中调用sb.length()的时候，返回内部的count成员
当append()时发现空间不够的情况下，对数组进行扩容，扩容为原来容量的2倍+2，同时将数组中的元素复制到新的数组中
```