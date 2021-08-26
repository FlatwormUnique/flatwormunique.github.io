<img width="650" alt="1" src="https://user-images.githubusercontent.com/85718974/130980182-06b5684f-5d9a-47e0-8cf9-e230368db411.png">
---
layout: post
title: "群晖系统桥接教程-省去一个2.5G交换机的钱钱"
date: 2021-08-26 
tag: 群晖，桥接，SSH
---

# 前言：桥接的意义

是穷吗？是极客精神！

如果要说有2.5G使用场景的普通人家，莫不过就是NAS和主力电脑之间。其他的如机顶盒、孩子用的电脑、AP之类其实千兆用用就可以了，那么，如果为了2.5G网络而添置一台2.5G交换机的话，稍显有点浪费，一来多了几个用不上的接口，再者2.5G的功耗开销也相对千兆更大。

# 一.桥接的网络拓扑图

图0

# 二. 打开Open vSwitch

<img width="650" alt="1" src="https://user-images.githubusercontent.com/85718974/130980250-bccede07-2eb3-4d20-b64d-670c7ed48cdc.png">

打开了vSwitch之后，相当于给每一个端口都做了个虚拟交换机，那这样肯定是不行的，所以继续往下看。

# 三. SSH下的一系列操作

首先，要搞清楚，哪条网线接哪个设备，如图：
<img width="747" alt="2" src="https://user-images.githubusercontent.com/85718974/130980391-87f6aae3-de2b-4cfb-89b6-8b0c071f9abe.png">

其次，要搞清楚，哪个端口对应的是哪个虚拟交换机。

还是看上图：

局域网1=eth0对应绑定了ovs-eth0

局域网2=eth1对应绑定了ovs-eth1

局域网3=eth2对应绑定了ovs-eth2

现在，假设，请看清楚，假设我们将局域网1连接到主路由器，PC连接到局域网2！

## 1.开启群晖的SSH服务

登录群晖→控制面板→高级模式→终端机和SNMP→启动SSH功能

## 2.开启电脑的SHH服务

MacOS、Linux系统无需操作

Windows系统：Windows设置→应用→应用和功能→可选功能→添加功能→添加Openssh服务

## 3.SSH登录群晖操作

MacOS、Linux系统可以直接使用“终端”

Windows系统：cmd打开“命令提示符”

输入ssh 你的群晖用户名@群晖局域网地址(举例用户名为huahua、局域网地址为192.168.1.129)：

```
ssh huahua@192.168.1.129
```

**注意：这里输入的是群晖的用户名！不是QuickConnect ID!**

回车之后会出现如下字符：

```
The authenticity of host '192.168.1.129 (192.168.1.129)' can't be established.
ECDSA key fingerprint is SHA256:Y7aQyGomZWRyGjHEmv2tf16HKRt9/yjOiT0rQg7NV3U.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

问你yes不yes，当然要yes了！ yes之后回车输入群晖登录密码(输入的时候没有光标没有提示什么都没有不要以为是键盘坏了哦)

登录成功后输入sudo -i 获得root权限

```
sudo -i
```

回车之后再次输入群晖登录密码，回车。 光标的前面如果变成了 root@XXXX:~# （XXXX为你的机器名）则可以进入下一步。

## 4.删除桥接中的需要连接PC端口的虚拟交换机

如上的假设，我们先删除局域网2的虚拟交换机，以下命令可以复制：
```
ovs-vsctl del-br ovs_eth1
```
## 5.将需要连接到PC的端口添加到“连接路由器”的虚拟交换机上

还是按照如上假设，我们将局域网2对应的eth1添加到连接路由器的局域网1对应的eth0上。有点拗口，但是逻辑清楚。
```
ovs-vsctl add-port ovs_eth0 eth1
```
## 6.如果有多个网口都要桥接呢？

相信仔细看了前面教程的极客们马上可以举一反三的得出，如果我们现在要把局域网3也桥接了只需要执行以下命令就可以了：
```
ovs-vsctl del-br ovs_eth2
```
```
ovs-vsctl add-port ovs_eth0 eth2
```
# 重要说明

再次说明一下，我这里的命令，是假设局域网1连接路由器，局域网2和局域网3连接2台PC的情况，如果您家的情况不同，请根据上述命令来修改，我相信您如果看懂了改起来是简单的事情。
