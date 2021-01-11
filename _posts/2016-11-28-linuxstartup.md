---
layout: post
title: Ubuntu 14.04 LTS Server设置开机启动
date: 2016-11-28 21:15:58
tags: [Linux]
categories: [配置]
---
Linux设置开机启动项：

* 运行`runlevel`确定运行等级
* 切换到`vi /etc/rc.local`编辑添加运行项：
    ```bash
    #!/bin/sh -e
    #
    # rc.local
    #
    # This script is executed at the end of each multiuser runlevel.
    # Make sure that the script will "exit 0" on success or any other
    # value on error.
    #
    # In order to enable or disable this script just change the execution
    # bits.
    #
    # By default this script does nothing.

    /usr/share/denyhosts/daemon-control start
    /usr/bin/python /root/scripts/cloudservices.py start
    exit 0
    ```
* 根据运行等级切换到rc{level}.d目录下
* 如果S99rc.local存在并且为空文件就删除
* 设置rc.local的软连接为rS99rc.local  
   ```
   ln -s /etc/rc.local S99rc.local
   ```
