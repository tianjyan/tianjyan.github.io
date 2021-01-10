---
title: 树莓派配置Nas
date: 2016-02-28 21:15:58
tags: [树莓派]
categories: [配置]
---

不同版本操作方式可能不同。

# 安装文件系统
树莓派作为一个Linux系统，本身不支持exFat和ntfs格式，所以需要安装扩展：
    
    sudo apt-get install exfat-fuse
    sudo apt-get install ntfs-3g
    
# 安装samba

    sudo apt-get install samba samba-common-bin
    sudo apt-get install netatalk （可选，用于支持AFP）
    sudo apt-get install avahi-daemon（可选，用于支持网内的计算机自动发现）
    
# 编辑配置文件

    sudo nano /etc/samba/smb.conf
    
    #最后添加
    [share] 
        comment = Public Storage 
        path = /media/pi 
        valid users = pi
        browseable = yes
        public = yes
        writable = yes
        create mask = 0660
        directory mask = 0771
        read only = no
        
# 添加用户

    sudo smbpasswd -a pi

两次输入密码后成功建立账号。

# 重启samba

    sudo /etc/init.d/samba restart
    
树莓派的IP即是Nas的IP