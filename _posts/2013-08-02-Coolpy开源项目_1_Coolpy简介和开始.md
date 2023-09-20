---
layout: post
title: Coolpy开源项目:(1)Coolpy简介和开始
date: 2013-08-02 21:15:58
tags: [Arduino,Windows Phone]
categories: [Open Source]
---
# 开始之前
Coolpy开源项目主页:https://coolpy.codeplex.com

## 本次教程所用:
* 硬件：Arduino Uno R3，W5100，路由器，网线一根，B-usb线一根
* 烧写软件：Arduino—0023(http://files.arduino.cc/downloads/arduino-0023.zip)
* 源码：Coolpy硬件ROM源代码文件v1.1(https://coolpy.codeplex.com/releases/view/109870)
* 连接软件: Coolpy_Windows客户端(v1.0)(https://coolpy.codeplex.com/releases/view/110232)
<!-- more -->
# 简介
Coolpy是一项基于Arduino开发的开源项目，其主要功能就是通过手持设备来进行对硬件电路的控制和信息获取。这是一个与物联网和智能家居有关联的开源项目。

想要了解Coolpy就必须先了解单片机。对于传统的计算机而言，主要的构成部分分为中央处理单元CPU（进行运算、控制），随机存储器RAM（数据存储），存储器ROM（程序存储），输入/输出设备I/O（串行口、并行输出口等）,然后将其各部分印刷到主板之上,加之一些商业和工业的设计,就成为我们手中所用的计算机.而单片机则是上述部分都集成到一块单独的芯片里面，所以我们也成单片机为单芯片机。而Arduino的基础就是AVR单片机(AVR指令集的单片机是单片机的一种)。单片机因为其廉价易用而常用于工业生产的控制、生活中与程序和控制有关的场合。

有关Arduino的内容不再多余累赘.更多资料请参考下面的链接:
* Arduino资料库: http://wiki.geek-workshop.com/doku.php
* Arduino开源硬件资料库: http://kb.open.eefocus.com/index.php?title=Arduino_IDE

# 开始
好了，开始我们的Arduino应用。
## Step 1：
> 把下载好的Cool硬件ROM源代码文件解压到硬盘的某处。

## Step 2：
> 把下载好的Arduino—0023也解压到硬盘的某处。

## Step 3：
> 将Cool硬件ROM源代码目录下的IRremote文件夹复制到arduino-0023下的libraries文件夹内，完成复制之后，打开arduino-0023下的arduino.exe；(如果提示升级,请取消或者选择否)

## Step 4：
> 打开arduino.exe后，单击File→Open，找到Cool硬件ROM源代码下的WPadkArduino0023.pde文件打开。  
![image](/images/2013-08-02-Coolpy-01.jpg)  

## Step 5：
> 打开后我们会看到下图的内容:  
![image](/images/2013-08-02-Coolpy-02.jpg)  

## Step 6：
> 现在的话我们还不能直接将程序烧写到Arduino里，我们还有在程序的开头添加一句代码：
<pre><code>#include&lt;SPI.h&gt;</code></pre>
然后效果应该是这样的:  
![image](/images/2013-08-02-Coolpy-03.jpg)  

## Step 7：
> 接下来我们就可以连接上我们的主板Arduino。(有关Arduino的驱动安装和端口设置请参照购买时店家提供的说明)。  
点击工具栏的Upload按钮，如下图所示:  
![image](/images/2013-08-02-Coolpy-04.jpg)  
等待片刻，当出现Done Uploading的时候，即为完成，如图所示:   
![image](/images/2013-08-02-Coolpy-05.jpg)  

## Step 8：
> 已经完成我们的程序烧写，现在我们要完成硬件电路的连接。将Arduino Uno R3 板子和W5100引脚和端口对应插入，如图所示:  
![image](/images/2013-08-02-Coolpy-06.jpg)  
然后将Arduino接电源，W5100的网络口插路由器的Lan口，最终效果如图所示:  
![image](/images/2013-08-02-Coolpy-07.jpg)   

## Step 9：
> 好了，硬件我们已经连接完成了，离成功只差一小步了。我们先稍等一会，等Arduino和路由器完全连接上。
Ok。打开我们的Coolpy_Windows客户端(其他客户端也可以进行操作，请参考此过程执行)。将电脑连接到Arduino所连接的路由。我们的客户端如图所示：  
![image](/images/2013-08-02-Coolpy-08.jpg)   

因为现在只是做简单的介绍和演示，所以不介绍更多内容。我们先不改动任何其他的内容，先只是简单的建立一个Arduino和我们电脑的连接。

## Step 10：
> 点击”连接硬件”，当提示已连接的时候，我们再点击”重置硬件”，这时候界面会出现如下内容：    
![image](/images/2013-08-02-Coolpy-09.jpg)    
这就说明我们的客户端硬件和Arduino建立了连接，可以直接其他的操作了。

因为本次教程只是对这个开源项目做一个简单的介绍，所以上面的内容只是建立了一个简单的连接，但是只是阅读上面的内容，很难感受到具体的功能。所以我做了一个很简单的功能演示。通过一个温度湿度传感器来获得室内的温湿度。

## Step 11：
> 将温湿度传感器连接到Arduino板子上，点击”截取温湿度”按钮，就会看到如下内容：  
![image](/images/2013-08-02-Coolpy-10.jpg)   

具体的硬件连接和实现原理，我们在下次教程中再做介绍。

 

# 后记：
* 如果出现烧录程序出错的情况，请拔出板子上的所有连接路线后再烧录；
* 如果路由器的ip地址出现了冲突，请将代码中的相应内容改为你能用的ip；
* 如果你想更改密码，请将代码中的相应内容改为你需要的密码：然后烧写完成之后，将客户端的密码改为相应的密码，连接硬件→重置硬件，即可。