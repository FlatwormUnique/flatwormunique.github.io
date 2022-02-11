---
layout: post
title: "群暉系統橋接教程-省去一個2.5G交換機的錢錢"
date: 2021-08-26 
tag: 群暉，橋接，SSH
---

# 前言：橋接的意義

是窮嗎？是極客精神！

如果要說有2.5G使用場景的普通人家，莫不過就是NAS和主力電腦之間。其他的如機頂盒、孩子用的電腦、AP之類其實千兆用用就可以了，那麼，如果為了2.5G網絡而添置一台2.5G交換機的話，稍顯有點浪費，一來多了幾個用不上的接口，再者2.5G的功耗開銷也相對千兆更大。

或者理由更加簡單，裝修時您的工作室只拉了一條網線過來，NAS和工作電腦都要有線連接才舒服，如此而已。

# 一.橋接的網絡拓撲圖

![微信圖片_20210927193141 (2)](https://user-images.githubusercontent.com/85718974/134901303-fccb7f65-f94f-4c8d-9462-088e01215f50.jpg)
靈魂作畫應付一下

# 二. 打開Open vSwitch

<img width="650" alt="1" src="https://user-images.githubusercontent.com/85718974/130980250-bccede07-2eb3-4d20-b64d-670c7ed48cdc.png">

打開了vSwitch之後，相當於給每一個端口都做了個虛擬交換機，那這樣肯定是不行的，所以繼續往下看。

# 三. SSH下的一系列操作

首先，要搞清楚，哪條網線接哪個設備，如圖：
<img width="747" alt="2" src="https://user-images.githubusercontent.com/85718974/130980391-87f6aae3-de2b-4cfb-89b6-8b0c071f9abe.png">

其次，要搞清楚，哪個端口對應的是哪個虛擬交換機。

還是看上圖：

局域網1=eth0對應綁定了ovs-eth0

局域網2=eth1對應綁定了ovs-eth1

局域網3=eth2對應綁定了ovs-eth2

現在，假設，請看清楚，假設我們將局域網1連接到主路由器，PC連接到局域網2！

## 1.開啟群暉的SSH服務

登錄群暉→控制面板→高級模式→終端機和SNMP→啟動SSH功能

## 2.開啟電腦的SHH服務

MacOS、Linux系統無需操作

Windows系統：Windows設置→應用→應用和功能→可選功能→添加功能→添加Openssh服務

## 3.SSH登錄群暉操作

MacOS、Linux系統可以直接使用「終端」

Windows系統：cmd打開「命令提示符」

輸入ssh 你的群暉用戶名@群暉局域網地址(舉例用戶名為huahua、局域網地址為192.168.1.129)：

```
ssh huahua@192.168.1.129
```

**注意：這裡輸入的是群暉的用戶名！不是QuickConnect ID!**

回車之後會出現如下字符：

```
The authenticity of host '192.168.1.129 (192.168.1.129)' can't be established.
ECDSA key fingerprint is SHA256:Y7aQyGomZWRyGjHEmv2tf16HKRt9/yjOiT0rQg7NV3U.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

問你yes不yes，當然要yes了！ yes之後回車輸入群暉登錄密碼(輸入的時候沒有光標沒有提示什麼都沒有不要以為是鍵盤壞了哦)

登錄成功後輸入sudo -i 獲得root權限

```
sudo -i
```

回車之後再次輸入群暉登錄密碼，回車。 光標的前面如果變成了 root@XXXX:~# （XXXX為你的機器名）則可以進入下一步。

## 4.刪除橋接中的需要連接PC端口的虛擬交換機

如上的假設，我們先刪除局域網2的虛擬交換機，以下命令可以複製：
```
ovs-vsctl del-br ovs_eth1
```
## 5.將需要連接到PC的端口添加到「連接路由器」的虛擬交換機上

還是按照如上假設，我們將局域網2對應的eth1添加到連接路由器的局域網1對應的eth0上。有點拗口，但是邏輯清楚。
```
ovs-vsctl add-port ovs_eth0 eth1
```
## 6.如果有多個網口都要橋接呢？

相信仔細看了前面教程的極客們馬上可以舉一反三的得出，如果我們現在要把局域網3也橋接了只需要執行以下命令就可以了：
```
ovs-vsctl del-br ovs_eth2
```
```
ovs-vsctl add-port ovs_eth0 eth2
```
# 重要說明

再次說明一下，我這裡的命令，是假設局域網1連接路由器，局域網2和局域網3連接2台PC的情況，如果您家的情況不同，請根據上述命令來修改，我相信您如果看懂了改起來是簡單的事情。
