---
layout: post
title: "威聯通安裝RTL8156網卡驅動的方法"
date: 2021-06-22 
description: ""
tag: 威聯通 2.5G網卡 驅動 RTL8156
---

# 前言 

威聯通雖然系統比群暉更開放，可是又更小眾，所以折騰資料不多，本文是多位愛好者協作的結果。

本文(理論上)適用於所有X86平台的威聯通機型，比如**TS453B mini**

本文多圖，受制於中美網絡的特殊性，請耐心等待加載，遇到打不開的情況多刷新幾次。

# 準備材料

下載驅動：[RTL8156(include B) driver for QTS4.5.zip](https://pan.chiphello.com:40272/威聯通/RTL8156(include B) driver for QTS4.5.zip)

下載工具軟件：[Winscp](https://pan.chiphello.com:40272/黑群暉/WinSCP-5.17.10-Setup.exe)

# 安裝驅動

## 一. 導入驅動 

### 1.開啟威聯通的SSH服務

在NAS網頁管理界面，控制台-網絡&文件服務-Telnet&SSH，打開SSH。

### 2.開啟電腦的SSH服務

Linux、MacOS無需操作

Windows系統：Windows設置→應用→應用和功能→可選功能→添加功能→添加Openssh服務

### 3.SHH登錄威聯通

MacOS、Linux系統可以直接使用「終端」

Windows系統：cmd打開「命令提示符」

輸入ssh 你的威聯通id@威聯通局域網地址(舉例用戶名為admin、局域網地址為192.168.1.107)：

```
ssh admin@192.168.1.107
```

回車之後輸入威聯通登錄密碼(輸入的時候沒有光標沒有提示什麼都沒有不要以為是鍵盤壞了哦)

回車之後再次輸入威聯通登錄密碼，之後就獲得下面的界面：

![wlt1](https://user-images.githubusercontent.com/85718974/131508142-d03bd67e-9e3a-44ad-89cf-518107824379.jpg)

按「Q」退出這個框框，再次輸入」Y「確認退出。

### 4.解壓並導入

將之前下載的驅動ZIP文件解壓得到 usbnet.ko 和 r8152.ko ，將這2個文件放到一個比較簡單路徑，方便等下使用。

用Winscp連接到NAS，如下圖可以看到，左側是本地路徑，右側是威聯通的路徑，威聯通的根目錄下有個「share」文件夾，裡面可以看到CACHEDEV1_DATA和CACHEDEV2_DATA這2個存儲池，這裡因機而異。

![wlt2](https://user-images.githubusercontent.com/85718974/131508540-24a8907e-39b1-4255-b844-e11f82d320e4.jpg)

我們現在要做的，就是把 usbnet.ko 和 r8152.ko兩個文件放入任意一個存儲池中，我這裡是放到了CACHEDEV1_DATA的根目錄下。

![wlt3](https://user-images.githubusercontent.com/85718974/131509722-4882b294-76e4-416b-ae4f-1f5ddbd3e656.jpg)

接下來在SSH中依次輸入以下命令，輸入一行回車一次。

 ```
/sbin/rmmod r8152
/sbin/rmmod usbnet
sleep 3
/sbin/insmod /share/CACHEDEV1_DATA/data/usbnet.ko
/sbin/insmod /share/CACHEDEV1_DATA/data/r8152.ko
 ```
![wlt4](https://user-images.githubusercontent.com/85718974/131510197-0bc21c33-8d2f-49df-ba17-0622b51b5c87.jpg)

這時網卡應該已經認出來了。

![wlt5](https://user-images.githubusercontent.com/85718974/131510264-9f5d8be5-a345-4d88-809b-ff911b1f6f04.jpg)


## 二.設置開機自動加載驅動

前面我們已經成功讓機器識別了RTL8156B網卡，但是重啟後就沒了，我們需要需要手動編輯一個開機自動加載驅動的autorun.sh文件。

### a. 新建autorun.sh文件

用Winscp連接到NAS，這裡注意，**一定要用「admin」賬戶登錄**。

![wlt6](https://user-images.githubusercontent.com/85718974/131513259-a53c6402-8635-46e7-9342-169243b1cee2.jpg)

在tmp/config目錄里新建一個autorun.sh文件

![wlt7](https://user-images.githubusercontent.com/85718974/131513800-9c815833-1224-470c-9cd2-1872f3d79b22.jpg)


```
mount $(/sbin/hal_app --get_boot_pd port_id=0)6 /tmp/config
/sbin/rmmod r8152
/sbin/rmmod usbnet
sleep 3
/sbin/insmod /share/CACHEDEV1_DATA/data/usbnet.ko
/sbin/insmod /share/CACHEDEV1_DATA/data/r8152.ko
```

然後把上面的代碼複製到autorun.sh裡面

### b. 修改權限

SSH連上NAS，依次輸入如下命令：

```
chmod +x /tmp/config/autorun.sh
umount /tmp/config
```

### c. 啟動時運行用戶定義的進程

打開NAS網頁管理界面，找到 控制台→硬件→啟動時運行用戶定義的進程打勾。

![wlt9](https://user-images.githubusercontent.com/85718974/131521364-d2cafb97-508f-4bdd-9a1d-ddd851a815a7.jpg)

重啟即成功啦！

### d. 最後一步
至此，設定全部完成，重啟後大概率就成功了，可是我要提醒您記得關閉SSH，NAS的安全和穩定大於一切！

**控制台-網絡&文件服務-Telnet&SSH，關閉SSH**

# 後記
感謝@minlang112編譯的驅動

感謝@hhjfm的測試

# 擴展閱讀
[其他非intel&amd機型如何建立autorun.sh ](https://wiki.qnap.com/wiki/Running_Your_Own_Application_at_Startup)
