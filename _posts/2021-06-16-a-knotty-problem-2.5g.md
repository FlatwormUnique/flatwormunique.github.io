---
layout: post
title: "升級2.5G網卡的一些疑難雜症"
date: 2021-06-16 
description: "持續更新"
tag: 群暉 網卡 2.5G 8156B 驅動
---

疑難雜症集合（希望玩家朋友貢獻經驗，本篇持續更新）

本條目專門收集網友們反饋來的各種問題，發問前請先看看有沒有和你類似的解決方案。

本條目有些資料來源其他博客，感謝您的付出，文末附上來源信息。
# 操作系統原因（軟故障）
[^.^]: ## 一. 驅動安裝成功，重啟後不識別、不啟動等問題
### A：Windows
#### 1. Windows系統下，裝好最新的驅動之後並沒有發現新硬件？
網卡在Windows系統下會識別出一個盤符，裡面有個exe文件，點它即可。
#### 2. 正確安裝了盤符內的驅動，但是出現藍屏、斷流、速度跑不滿、開不了巨幀等不限於這些問題！
那是因為盤符內的驅動比較老，Win10、Win11用這個驅動有諸多問題，解決方法：去官網下載最新的驅動即可。

官方鏈接：[https://www.realtek.com/zh-tw/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-usb-3-0-software](https://www.realtek.com/zh-tw/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-usb-3-0-software)

備用下載：[https://pan.chiphello.com:40272/?dir=/%E9%A9%B1%E5%8A%A8/RTL8156](https://pan.chiphello.com:40272/?dir=/%E9%A9%B1%E5%8A%A8/RTL8156)
### B：MacOS
#### 1.MAC系統下，重啟後不識別網卡？
目前由於驅動完善，有這個問題，在iMAC和新款M1的機器上復現了。解決辦法暫時只能是插拔一次就ok。
### C：群暉
#### 1.DS918+和DS920+的後置USB 3.0插上沒反應？

具網友反映，這倆機型的後置接口可能供電不足，插前置USB就好了。
#### 2.安裝了正確的套件，可是套件啟動失敗
據客戶反映，套件版本2.14.0-3有一定的兼容性問題，當然問題可能不止這一個版本，如果您的套件啟動不正常，可以嘗試換個版本安裝。

#### 3.驅動安裝成功，可是重啟就沒了？
##### 方法1：
這種情況，試着關了再開一次套件，如果這樣就識別了，那麼問題出在有個腳本的等待時間不夠，我們要SSH登錄群暉去修改一個參數，請參考「[群暉DSM系統安裝8156B芯片網卡驅動](https://flatworm-unique.chiphello.com/2021/06/DSM-8156B-drivers/)」的第五大點。
##### 方法2：
如果上述正確執行依然無效，可能是遇到了玄學問題，可能與機器配置或者各種小參數有關，這裡提供用戶「@年年」的解決方案。
1. 登錄群暉WEB界面，控制面板中找到「任務計劃」；
 
 ![步驟1](https://user-images.githubusercontent.com/85718974/132372880-1568afb5-e0ab-46df-85b2-1923ec1694e5.png)

2. 新建一個任務計劃，任務名稱隨意取，用戶賬號選root，事件選「開機」；

![步驟2](https://user-images.githubusercontent.com/85718974/132372956-d50ab48c-1b52-4847-b9ed-05cd71a88a0a.png)

3. 任務設置里添加一個「自定義腳本」，確定保存即可，重啟一下試試吧。

![步驟3](https://user-images.githubusercontent.com/85718974/132372985-0c73cd28-9f2a-4bb1-8e6a-83bd8481d117.png)

代碼可複製

```
/usr/syno/bin/synopkg restart r8152
```

#### 4.裝好驅動後，2.5G訪問不了，原有千兆網登錄上去發現驅動啟動了卻沒有分配IPV4地址？
這種情況是一位客戶反饋，其機型為DS218+，上級路由為AC86U，如圖所示，驅動已經啟動，分配到了IPV6地址，沒有分配IPV4地址（DHCP為否）。
<img width="1058" alt="none-ipv4-2" src="https://user-images.githubusercontent.com/85718974/122928467-7b2f7a00-d39c-11eb-8f9d-ee49797fff4d.png">
這種情況手動配置IPV4地址或者自動取得網絡設置（DHCP）即可解決。
<img width="233" alt="open-dhcp" src="https://user-images.githubusercontent.com/85718974/122929320-630c2a80-d39d-11eb-8a5c-0a433b9badc6.png">

[^.^]:  ### D：其他系統

# 設備原因（硬故障）
## 蝸牛星際的系列問題
#### 雙千兆主板只識別為千兆網卡
此問題復現在A雙和B雙網卡板載芯片為intel 82583的主板上，經過研究得知，此主板沒有引出USB3.0接口，是的，後置藍色接口是個假的3.0，所以USB設備只能工作在2.0標準下，故只識別出千兆網卡。建議準備上2.5G的玩家儘量選擇單網卡版本，這樣在安裝驅動時也更加簡單。

#### 不識別USB設備
有些蝸牛的主板，因為主板電池沒電或者其他原因斷電後會復位BIOS設置，具體原理不得而知，但是修改一個選項即可。
開機後不停按Del鍵即可進入BIOS（藍牙無線鍵盤無法實現），然後進入「Advanced」條目，再進入「Miscellaneous Configuration」選項，
![111](https://user-images.githubusercontent.com/85718974/136494917-d1231129-3fda-45e7-814c-6999e076b74d.jpg)
接下來修改「OS Selection」的值為「Windows 8.X」。
![222](https://user-images.githubusercontent.com/85718974/136494935-e6a11fd6-8f8d-4ab8-b899-0726a1077d7d.jpg)

感謝@jroky0貢獻經驗，更新於2021.10.07
