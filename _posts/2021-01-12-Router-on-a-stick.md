---
layout: post
title: 记一次单臂路由实现的科学上网
date: 2021-01-12 19:59:03
tags: [Msic]
categories: [配置]
---

从淘宝上淘回来的星际蜗牛刷上黑群已经稳定运行了快两年了, 之前小米路由R2D上面的硬盘连带里面的电影和音乐都作为这个黑群的一部分了。说实话蜗牛这个矿机的1U电源真的很差，虽然我的这个买回来的时候没有像其他网友上面都是灰尘，但是感觉电源风扇的声音分贝太高了。看到网上也有评论说这个电源不稳定，容易烧电路，二来也带不动太多的硬盘。所以后来找了厂家定制了个ATX直插电源，稳定运行两年也离不开这个电源。唯一美中不足的是蜗牛后面的电源挡板被我拆了，所以看起来有点外露，不过还好是背面，平时也看出来。

ATX直插电源：
![ATX直插电源](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-1.png)

星际蜗牛背面：
![星际蜗牛背面](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-2.png)

---

回到正题，本身AC86U是刷了梅林改版的固件，是可以实现科学上网的，之所以又去寻找一种新的方式是因为：
* 想用官方的固件。多数情况下，其实官方的路由固件更稳定，所以如果一旦第三方的固件出问题了，会很影响家里其他人使用网络
* 想使用群晖更多的功能

这次实践上，最核心的点其实就是使用群晖的虚拟机安装一个路由器的系统。现在大家比较流行的做法其实是要么像我上面说的，在路由器上装插件，实现科学上网的功能；要么就是购买一个x86的软路由，然后路由器作为AP。这次我的实现方式是这样的，目前的路由设备恢复原厂的系统，然后在群晖的虚拟机中安装路由器系统作为旁路由。网络的拓扑结构是这样的，光猫的LAN口连接路由器的WAN口，路由器的LAN口连接群晖的网络口（我的群晖有且只有一个RJ45的接口）。

## 开始前的准备
我这边选中的路由系统的镜像是Koolshare的x86版本的LEDE（现在又改回叫做OpenWRT）了，下载地址在这里：https://firmware.koolshare.cn/。找到x86的版本下载即可，我下载的是：openwrt-koolshare-mod-v2.36-r14941-67f6fa0a30-x86-64-generic-squashfs-combined.img.gz。下载完成后解压出img文件即可。

## 安装和设置群晖中的虚拟机
如果没有安装过[Virtual Machine Manager](https://www.synology.cn/zh-cn/dsm/feature/virtual_machine_manager)，可以去套件中心下载安装。

套件中心的Virtual Machine Manager：
![套件中心的Virtual Machine Manager](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-3.png)

接下来要打开Virtual Machine Manager，在左侧的菜单栏中找到`映像`，点击`新增`，然后选择刚刚解压的img文件，并且设置存储镜像的位置，按提示设置完成即可。

上传镜像：
![上传镜像](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-4.png)

选择镜像存储路径：
![选择镜像存储路径](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-5.png)


镜像设置完成后就可以创建虚拟机了，在左侧的菜单中找到`虚拟机`，点击`新增`按钮边上的下拉按钮，点击`导入`，这边主要需要设置的是虚拟机的名字，CPU核心数以及内存大小，按实际大小调整即可。然后网络可以暂时不改，反正半双工的模式也能操作，如果要改成全双工，改成e1000即可。

选择导入镜像：
![选择导入镜像](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-6.png)

设置虚拟机名字，CPU，内存等信息：
![设置虚拟机名字，CPU，内存等信息](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-7.png)

设置其他磁盘信息：
![磁盘信息](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-8.png)

配置网络接口信息（这边可以点击小齿轮设置网络接口类型为e1000）：
![配置网络接口](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-9.png)

其他非必要信息设置：
![其他非必要信息设置](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-10.png)

设置可以操作虚拟机的用户：
![设置可以操作虚拟机的用户](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-11.png)

完成后的基本信息查看：
![完成后的基本信息查看](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-12.png)

完成以后，选中刚刚创建的虚拟机，点击`开机`即可。等待开机完成后，点击`连接`，可以在网页中打开一个Console。在Console中输入`vi /etc/config/network`修改`lan`接口的IP为局域网内可访问的IP即可。修改完成后，输入`reboot`等待重启完成，就可以在浏览器中输入刚刚设置的IP打开路由器的后台，输入密码koolshare进入系统。

打开后的Console：
![打开后的Console](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-13.png)

查看network文件和修改：
![查看network文件](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-14.png)

路由器后台登录界面：
![路由器后台登录界面](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-15.png)

路由器登录界面：
![路由器登录界面](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-16.png)

## 配置虚拟路由器

登录路由器之后，需要设置LAN口的基本信息，包括默认网关，广播，DNS，是否桥接以及DHCP信息。

设置LAN口：
![设置LAN口](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-17.png)
![设置LAN口](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-18.png)
![设置LAN口](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-19.png)
![设置LAN口](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-20.png)

之后在防火墙这边添加规则`iptables -t nat -I POSTROUTING -j MASQUERADE`

设置防火墙规则：
![设置防火墙规则](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-21.png)

然后可以安装科学上网的插件，通过离线下载安装，安装包地址：https://github.com/hq450/fancyss_history_package/tree/master/fancyss_X64

插件下载完成后：
![插件下载完成后](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-22.png)

然后进入插件配置科学上网的基本信息即可。到此的话，虚拟路由器这边的设置就完成了。

## 如何在客户端使用

在客户端这边的使用的话，方式有很多，具体如下：

### 在主路由修改默认网关

将主路由的默认网关设置为虚拟路由器的IP，即可实现局域网内所有的设备都通过虚拟路由器上网

设置主路由默认网关：
![设置主路由默认网关](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-23.png)

### 特定设备通过虚拟路由上网

如果不想设置主路由，可以将客户端的DNS和网关设置为虚拟路由的IP

设置特定设备信息：
![设置特定设备信息](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-26.png)
![设置特定设备信息](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-27.png)

### 特定设备直接通过科学上网开放端口上网

也可以直连科学上网软件开放的端口上网

直连科学上网软件：
![直连科学上网软件](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-24.png)
![直连科学上网软件](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2021-01-13-25.png)

## 结尾

上述就是群晖设置一个虚拟路由，然后局域网设备通过其上网的全部过程。
