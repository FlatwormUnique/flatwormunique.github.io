---
layout: post
title: "威联通安装RTL8156网卡驱动的方法"
date: 2021-06-22 
description: ""
tag: 威联通 2.5G网卡 驱动 RTL8156
---

# 前言 

威联通虽然系统比群晖更开放，可是又更小众，所以折腾资料不多，本文是多位爱好者协作的结果。

本文(理论上)适用于所有X86平台的威联通机型，比如**TS453B mini**

本文多图，受制于中美网络的特殊性，请耐心等待加载，遇到打不开的情况多刷新几次。

# 准备材料

下载驱动：[RTL8156(include B) driver for QTS4.5.zip](https://pan.chiphello.com:40272/威联通/RTL8156(include B) driver for QTS4.5.zip)

下载工具软件：[Winscp](https://pan.chiphello.com:40272/黑群晖/WinSCP-5.17.10-Setup.exe)

# 安装驱动

## 一. 导入驱动 

### 1.开启威联通的SSH服务

在NAS网页管理界面，控制台-网络&文件服务-Telnet&SSH，打开SSH。

### 2.开启电脑的SHH服务

Linux、MacOS无需操作

Windows系统：Windows设置→应用→应用和功能→可选功能→添加功能→添加Openssh服务

### 3.SHH登录威联通

MacOS、Linux系统可以直接使用“终端”

Windows系统：cmd打开“命令提示符”

输入ssh 你的威联通id@威联通局域网地址(举例用户名为hhjfm、局域网地址为192.168.1.107)：

```
ssh hhjfm@192.168.1.107
```

回车之后输入威联通登录密码(输入的时候没有光标没有提示什么都没有不要以为是键盘坏了哦)

登录成功后输入sudo -i 获得root权限

```
sudo -i
```

回车之后再次输入威联通登录密码，之后就获得下面的界面：

![wlt1](https://user-images.githubusercontent.com/85718974/131508142-d03bd67e-9e3a-44ad-89cf-518107824379.jpg)

按“Q”退出这个框框。

### 4.解压并导入

将之前下载的驱动ZIP文件解压得到 usbnet.ko 和 r8152.ko ，将这2个文件放到一个比较简单路径，方便等下使用。

用Winscp连接到NAS，如下图可以看到，左侧是本地路径，右侧是威联通的路径，威联通的根目录下有个“share”文件夹，里面可以看到CACHEDEV1_DATA和CACHEDEV2_DATA这2个存储池，这里因机而异。

![wlt2](https://user-images.githubusercontent.com/85718974/131508540-24a8907e-39b1-4255-b844-e11f82d320e4.jpg)

我们现在要做的，就是把 usbnet.ko 和 r8152.ko两个文件放入任意一个存储池中，我这里是放到了CACHEDEV1_DATA的根目录下。

![wlt3](https://user-images.githubusercontent.com/85718974/131509722-4882b294-76e4-416b-ae4f-1f5ddbd3e656.jpg)

接下来在SSH中依次输入以下命令，输入一行回车一次。

 ```
 /sbin/rmmod r8152
 /sbin/rmmod usbnet
  sleep 3
 /sbin/insmod /share/CACHEDEV4_DATA/（驱动所在文件夹名称）/usbnet.ko
 /sbin/insmod /share/CACHEDEV4_DATA/（驱动所在文件夹名称）/r8152.ko
 ```
![wlt4](https://user-images.githubusercontent.com/85718974/131510197-0bc21c33-8d2f-49df-ba17-0622b51b5c87.jpg)

这时网卡应该已经认出来了。

![wlt5](https://user-images.githubusercontent.com/85718974/131510264-9f5d8be5-a345-4d88-809b-ff911b1f6f04.jpg)


## 二.设置开机自动加载驱动

前面我们已经成功让机器识别了RTL8156B网卡，但是重启后就没了，我们需要需要手动编辑一个开机自动加载驱动的autorun.sh文件。

### a. 新建autorun.sh文件

用Winscp连接到NAS，这里注意，**一定要用“admin”账户登录**。

![wlt6](https://user-images.githubusercontent.com/85718974/131513259-a53c6402-8635-46e7-9342-169243b1cee2.jpg)

在tmp/config目录里新建一个autorun.sh文件

![wlt7](https://user-images.githubusercontent.com/85718974/131513800-9c815833-1224-470c-9cd2-1872f3d79b22.jpg)


```
mount $(/sbin/hal_app --get_boot_pd port_id=0)6 /tmp/config
```

然后把上面的代码复制到autorun.sh里面

![wlt8](https://user-images.githubusercontent.com/85718974/131513863-53857ffb-2df9-4fd0-880e-f53d61a2fd24.jpg)


### b. 修改权限

SSH连上NAS，依次输入如下命令：

```
chmod +x /tmp/config/autorun.sh
umount /tmp/config
```

### c. 最后一步

打开NAS网页管理界面，找到 控制台→硬件→启动时运行用户定义的进程打勾→重启NAS。

重启后找到 网络&文件服务→网络与虚拟交换机→网络适配器，如果多了一个2.5G网口，就成功啦。

# 后记
感谢@minlang112编译的驱动

感谢@hhjfm的测试

# 扩展阅读
[其他非intel&amd机型如何建立autorun.sh ](https://wiki.qnap.com/wiki/Running_Your_Own_Application_at_Startup)
