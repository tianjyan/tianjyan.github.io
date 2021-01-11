---
layout: post
title: EI Caption 下载和U盘安装
date: 2015-06-08 21:15:58
tags: [Mac]
categories: [配置]
---

# 系统下载

文件名：OS X El Capitan 10.11 (15A284) MAS.dmg  
[下载地址](/data/共享/工具/OS_X_El_Capitan_10.11_15A284_MAS.dmg)

# u盘安装

>* 准备一个8GB 的 U盘，用「应用程序 - 实用工具 - 磁盘工具」格式化成「Mac OS X 扩展（日志式）」格式，并将盘符命名为**「Untitled」**，盘符名称和下面的命令行是对应的，如果你使用其他盘符名称需要对应修改命令行中的盘符名称。
>* 打开「应用程序 - 实用工具 - 终端」，复制粘贴输入以下命令：  

    sudo /Applications/Install\ OS\ X\ El\ Capitan.app/Contents/Resources/createinstallmedia --volume /Volumes/Untitled --applicationpath /Applications/Install\ OS\ X\ El\ Capitan.app --nointeraction

执行命令前会提示你输入账户密码，密码不会显示，输入完成后直接 return即可。

耐心等待，直到「终端」程序界面中提示已经「Done」完成制作。