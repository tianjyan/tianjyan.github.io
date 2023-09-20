---
layout: post
title: 从微信SDK看ProtoBuffer文件的生成
date: 2016-07-22 21:15:58
tags: [ProtoBuffer]
categories: [csharp]
---

##  前言  

Protocol Buffers (下面简称PB)是一种轻便高效的结构化数据存储格式，可以用于结构化数据串行化，很适合做数据存储或 RPC 数据交换格式。它可用于通讯协议、数据存储等领域的语言无关、平台无关、可扩展的序列化结构数据格式。它支持多种语言，比如C++，Java，C## ，Python，JavaScript等等。目前它的最新版本是[3.0.0](https://github.com/google/protobuf/releases)。与PB经常相提并论的也是Google推出的FlatBuffers(下面简称FB)。有关PB和FB性能和语义等方面的区别，这里就不展开描述了。如果有兴趣，可以参阅下面的信息：

* [Google序列化库FlatBuffers 1.1发布，及与protobuf的比较](http://mp.weixin.qq.com/s?__biz=MzA3MDY5NjAzMw==&mid=205406410&idx=7&sn=dbacd18a854886022be6351410885b39&)
* [FlatBuffers Documentation](http://google.github.io/flatbuffers/)  
* [FlatBuffers与protobuf性能比较](http://blog.csdn.net/menggucaoyuan/article/details/34409433)

目前很多公司在一些高性能的通信场景下，会越来越多的选择用PB或者FB来替代我们常用的Json。比如说Windows Phone的微信的SDK就用到了。

##  反编译微信SDK
PB对C## 官方的支持是从3.0开始的，之前的1.0和2.0的版本都能找到一些非官方的版本。我们先反编译一下微信的SDK，看下它具体是什么版本的。

首先，我们从微信的官网下载SDK：

![Image](/images/2016-07-22-ProtoBuffer-01.png)

登陆微信开发平台，进入资源中心，选择WP8资源下载，点击下载。

然后下载我们的反编译工具[ILSpy](http://ilspy.net/)。

解压下载完成的ILSpy和SDK包，用ILSpy.exe打开MicroMsgSDK.dll。

![Image](/images/2016-07-22-ProtoBuffer-02.png)

我们暂时先不管这个结构到底是怎么来的，我们可以看到反编译出来的文件带了ProtoGen的版本号，我们尝试从Github上找到这个版本号的代码。

##  编译ProtoBuffer源码
我们先打开官方的C## 版本的PB的源码页面：[地址](https://github.com/google/protobuf/tree/master/csharp)。

可以看到官方地址只保留了3.0的版本，对于旧的2.0版本的代码在[jskeet](https://github.com/jskeet/protobuf-csharp-port)的账号下，  

![Image](/images/2016-07-22-ProtoBuffer-03.png)

我们点开这个仓库，然后找到它的[Release](https://github.com/jskeet/protobuf-csharp-port/releases)页面：  

![Image](/images/2016-07-22-ProtoBuffer-04.png)

我们找到2.3.0.277的源码并下载到本地。

解压文件，我们看到Build文件夹下有一堆编译用的脚本：  

![Image](/images/2016-07-22-ProtoBuffer-05.png)

双击运行buildAll.bat(此处应确保本机已经安装了Visual Studio 2008及以上版本)，然后等待编译完成。

##  尝试使用源码中的Proto文件生成cs代码
我们找到ProtoGen项目中生成的exe文件，尝试将它放到命令行中运行：  
![Image](/images/2016-07-22-ProtoBuffer-07.png)

它提示我们找不到protoc.exe程序。我们回到源码的根目录会发现有一个lib的文件夹，里面有一个protoc.exe的程序。所以我们尝试吧ProtoGen项目的所有生成文件拷贝到lib下。
继续尝试运行我们的ProtoGen程序。

![Image](/images/2016-07-22-ProtoBuffer-08.png)

这回对了，我们尝试把源码下的protos文件夹下的三个子文件夹拷贝到我们的lib目录下。

我们尝试输入如下内容：

    protogen --proto_path==protos protos/tutorial/addressbook.proto

又得到一个错误信息：  

![Image](/images/2016-07-22-ProtoBuffer-09.png)

提示我们找不到依赖，我们尝试打开proto文件：(有关PB的语法请参阅：http://www.cnblogs.com/stephen-liu74/archive/2013/01/02/2841485.html)

```protobuf
package tutorial;

import "google/protobuf/csharp_options.proto";

option (google.protobuf.csharp_file_options).namespace = "Google.ProtocolBuffers.Examples.AddressBook";
option (google.protobuf.csharp_file_options).umbrella_classname = "AddressBookProtos";

option optimize_for = SPEED;

message Person {
required string name = 1;
required int32 id = 2;        // Unique ID number for this person.
optional string email = 3;

enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
}

message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
}

repeated PhoneNumber phone = 4;
}

// Our address book file is just one of these.
message AddressBook {
repeated Person person = 1;
}
```

我们可以看到导入了google/protobuf/csharp_options.proto文件，我们回头看protogen的命令参数中有一个import的标记，我们尝试添加：

    protogen --proto_path==protos protos/tutorial/addressbook.proto --include_imports=google/protobuf/csharp_options.proto

没有任何错误，并且我们在lib的目录下发现了生成的cs文件。
![Image](/images/2016-07-22-ProtoBuffer-11.png)


##  从cs文件反推proto文件
我们打开AddressBookProtos文件，阅读源码发现：
* 只有两个非静态类，与我们Proto文件中的Person和AddressBook对应：
![Image](/images/2016-07-22-ProtoBuffer-12.png)

* Person类中又有一个嵌套的枚举和类，与PhoneType和PhoneNumber对应：
![Image](/images/2016-07-22-ProtoBuffer-13.png)

* 我们有发现，在类的IsInitialized中，Name和Id等required的有是否有值得判断，所以我们能区分去required和optional
![Image](/images/2016-07-22-ProtoBuffer-14.png)

其他依赖信息，我们可以通过引用来查找。

##  从反编译的微信文件中反推proto文件
我们以BaseReqP为例。首先，没有using，所以我们确定没有其他的Proto文件的依赖。我们只发现一个类，所以说明它只有一条message，名称就是BaseReqP，然后包名是MicroMsg.sdk.protobuf。
我们知道message的所有字段是需要标记数字的： 
![Image](/images/2016-07-22-ProtoBuffer-15.png)

从这里我们又反推出，message有两个字段：Transaction和Type，它们类型分别是string和uint。
接下来我们推是否是必须的。找到我们的IsInitialized:  
![Image](/images/2016-07-22-ProtoBuffer-17.png)
从这里我们就知道了两个字段都是必须的。所以综合上述信息，我们可以写出的proto文件如下：

```protobuf
package MicroMsg.sdk.protobuf;

message BaseReqP {
    required uint32 Type = 1;
    required string Transaction = 2;
}
```

##  小结
本篇内容简要介绍了ProtoBuffer的文件如何生成C## 文件，并简单的举例如何从C## 文件反推Proto文件。

##  参考信息  
* [FlatBuffers Documentation](https://google.github.io/flatbuffers/)  
* [Google Protocol Buffer 的使用和原理](http://www.ibm.com/developerworks/cn/linux/l-cn-gpb/index.html)  
* [Protobuf Source](https://github.com/google/protobuf)  
* [protocol-buffers documentation](https://developers.google.com/protocol-buffers/)  
* [Google序列化库FlatBuffers 1.1发布，及与protobuf的比较](http://mp.weixin.qq.com/s?__biz=MzA3MDY5NjAzMw==&mid=205406410&idx=7&sn=dbacd18a854886022be6351410885b39&)  
* [FlatBuffers与protobuf性能比较](http://blog.csdn.net/menggucaoyuan/article/details/34409433)
* [Protocol Buffer使用简介](http://www.jianshu.com/p/b1f18240f0c7)  
* [Protocol Buffer技术详解(语言规范)](http://www.cnblogs.com/stephen-liu74/archive/2013/01/02/2841485.html)  