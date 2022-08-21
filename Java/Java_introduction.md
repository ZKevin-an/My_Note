# java历史
```
由SUN（Stanford University Network）公司开发出来的，1995年推出
面向internet编程，一开始是可以在web浏览器中运行，称为java小程序（applet）
随着web不断发展，成为了web应用程序的首选开发语言
1991：最初命名Oak（橡树）
1996：发布JDK1.0
1999：Java分成J2SE,J2EE,J2ME
2004：里程碑版本JDK1.5，改名为JDK5.0
2005：J2SE->JavaSE，J2EE->JavaEE，J2ME->JavaME
2009：Oracle收购SUN，交易74亿美元
2014：JDK8.0，是5.0以后变化最大的版本
2017：JDK9.0
2018.3：JDK10.0版本号为18.3
2018.9：JDK11.0版本号为18.9
JavaSE：Standard Edition标准版，支持桌面级应用，提供完整的Java核心API
JavaEE：Enterprise Edition企业版，为开发企业环境提供的一套解决方案，包含的技术如Servlet，Jsp等，针对web应用程序开发
JavaME：支持运行在移动终端，对API有所精简，并加上对移动端的支持
Java的诞生：
james Gosling团队开发Green项目时，发现C缺少垃圾回收系统，还有可移植的安全性，分布程序设计和多线程，所以...
Java继承了C和C++很多成分，但是抛弃了指针，运算符重载，多重继承等特性，增加了垃圾回收，JDK5.0又增加了泛型编程，类型安全的枚举，不定长参数和自动装，拆箱
```

# Java虚拟机
```
JVM是一个虚拟的计算机，具有指令集并使用不同的存储区域，负责执行指令，管理数据，内存和寄存器，不同的平台有不同的JVM
```

# Java垃圾回收
```
不再使用的内存回收，Java提供一种系统级线程跟踪存储空间的分配情况，在JVM空闲时，检查并释放可被释放的空间，自动运行，程序员无法控制和干涉，但是Java还是会出现内存泄露和溢出的问题
```

# JDK(Java Development Kit)
```
Java开发工具包，提供给开发人员使用，包含了java的开发工具，包括了JRE，所以安装完JDK就不需要安装JRE了
其中的开发工具：编译工具（javac.exe）打包工具（jar.exe）
JDK=JRE+开发工具集（如Javac编译工具）
JRE=JVM+Java SE标准类库
```

# JRE(Java Runtime Environment)
```
Java运行环境，包括JVM和java程序所需的核心类库等，如果要运行一个开发好的Java程序，只需要安装JRE即可
```

# Java编写与编译
```
编写java源文件：.java
通过javac编译成字节码文件：.class
通过java.exe运行
```