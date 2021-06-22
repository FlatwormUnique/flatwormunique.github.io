---
layout: post
title: "威联通安装RTL8156网卡驱动的方法"
date: 2021-06-22 
description: ""
tag: 威联通 2.5G网卡 驱动 RTL8156
---

# 前言 

威联通虽然系统比群晖更开放，可是又更小众，所以折腾资料不多，本文是网上搜集整理的资料，谢谢编译驱动的 @minlang112 和 教程作者 @吴舅妈 @19x0

本文(理论上)适用于所有X86平台的威联通机型，比如**TS453B mini**

本文是在前人的教程基础上加工而成，因为曾叔叔没有威联通的机器，所以你按照这个教程实际操作下来有些细节可能比较模糊，有触墙的可能。

# 准备材料

下载驱动：[RTL8156(include B) driver for QTS4.5.zip](https://github.com/FlatwormUnique/flatwormunique.github.io/blob/master/download/RTL8156(include%20B)%20driver%20for%20QTS4.5.zip)

下载工具软件：[Winscp](https://dl.gxnas.com:1443/黑群晖/工具/WinSCP_5.11.1（进入群晖SSH对文件进行操作）.rar)

# 安装驱动

## 一. 导入驱动 

### 1.开启威联通的SSH服务

在NAS网页管理界面，控制台-网络&文件服务-Telnet&SSH，打开SSH。

### 2.开启电脑的SHH服务

Linux、MacOS无需操作

Windows系统：Windows设置→应用→应用和功能→可选功能→添加功能→添加Openssh服务

## 3.SHH登录威联通

MacOS、Linux系统可以直接使用“终端”

Windows系统：cmd打开“命令提示符”

输入ssh 你的威联通id@威联通局域网地址(举例用户名为niuniu、局域网地址为192.168.0.53)：

```
ssh niuniu@192.168.0.53
```

回车之后输入威联通登录密码(输入的时候没有光标没有提示什么都没有不要以为是键盘坏了哦)

登录成功后输入sudo -i 获得root权限

```
sudo -i
```

回车之后再次输入威联通登录密码

### 4.解压并导入

将驱动ZIP文件解压得到usbnet.ko r8152.ko ，将这2个文件放到一个比较简单路径，方便等下使用。

依次输入以下命令，输入一行回车一次。


 ```
 /sbin/rmmod r8152
 /sbin/rmmod usbnet
  sleep 3
 /sbin/insmod /share/CACHEDEV4_DATA/（驱动所在文件夹名称）/usbnet.ko
 /sbin/insmod /share/CACHEDEV4_DATA/（驱动所在文件夹名称）/r8152.ko
 ```

  这时网卡应该已经认出来了。

### 5.设置开机自动加载驱动

开机自动加载需要手动编辑一个autorun.sh文件

#### a. 新建autorun.sh文件

用Winscp连接到NAS， 在tmp/config目录里新建一个autorun.sh文件

```
mount $(/sbin/hal_app --get_boot_pd port_id=0)6 /tmp/config
```

然后把上面的代码复制到autorun.sh里面

#### b. 修改权限

SSH连上NAS，依次输入如下命令：

```
chmod +x /tmp/config/autorun.sh
umount /tmp/config
```

#### c. 最后一步

打开NAS网页管理界面，找到 控制台→硬件→启动时运行用户定义的进程打勾→重启NAS。

重启后找到 网络&文件服务→网络与虚拟交换机→网络适配器，如果多了一个2.5G网口，就成功啦。



[扩展阅读：其他非intel&amd机型如何建立autorun.sh ](https://wiki.qnap.com/wiki/Running_Your_Own_Application_at_Startup)
