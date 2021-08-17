---
layout: post
title: "升级2.5G网卡的一些疑难杂症"
date: 2021-06-16 
description: "持续更新"
tag: 群晖 网卡 2.5G 8156B 驱动
---

疑难杂症集合（更新中）

本条目专门收集网友们反馈来的各种问题，发问前请先看看有没有和你类似的解决方案。

本条目有些资料来源其他博客，感谢您的付出，文末附上来源信息。
## 一. 驱动安装成功，重启后不识别、不启动等问题
### A：Windows
#### 1. Windows系统下，装好最新的驱动之后并没有发现新硬件？
网卡在Windows系统下会识别出一个盘符，里面有个exe文件，点它即可。
#### 2. 正确安装了盘符内的驱动，但是出现蓝屏、断流、速度跑不满等不限于这些问题！
那是因为盘符内的驱动比较老，Win10、Win11用这个驱动有诸多问题，解决方法：去官网下载最新的驱动即可。
官方链接：[https://www.realtek.com/zh-tw/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-usb-3-0-software](https://www.realtek.com/zh-tw/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-usb-3-0-software)
备用下载：[https://pan.chiphello.com:40272/?dir=/%E9%A9%B1%E5%8A%A8/RTL8156](https://pan.chiphello.com:40272/?dir=/%E9%A9%B1%E5%8A%A8/RTL8156)
### B：MacOS
#### 1.MAC系统下，重启后不识别网卡？
目前由于驱动完善，有这个问题，在iMAC和新款M1的机器上复现了。解决办法暂时只能是插拔一次就ok。
### C：群晖
#### 1.DS918+和DS920+的后置USB 3.0插上没反应？

具网友反映，这俩机型的后置接口可能供电不足，插前置USB就好了。

#### 2.驱动安装成功使用正常可是重启后又不识别了？
a.重启后开关一次驱动套件即可识别
b.彻底解决这个问题还得配置一下“任务计划”，让群晖每次开机都执行挂载网卡的任务。

#### 3.装好驱动后，2.5G访问不了，原有千兆网登录上去发现驱动启动了却没有分配IPV4地址？
这种情况是一位客户反馈，其机型为DS218+，上级路由为AC86U，如图所示，驱动已经启动，分配到了IPV6地址，没有分配IPV4地址（DHCP为否）。
<img width="1058" alt="none-ipv4-2" src="https://user-images.githubusercontent.com/85718974/122928467-7b2f7a00-d39c-11eb-8f9d-ee49797fff4d.png">
这种情况手动配置IPV4地址或者自动取得网络设置（DHCP）即可解决。
<img width="233" alt="open-dhcp" src="https://user-images.githubusercontent.com/85718974/122929320-630c2a80-d39d-11eb-8a5c-0a433b9badc6.png">

### D：其他系统




