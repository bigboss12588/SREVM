# **安裝Alpine**

![p1](https://i.imgur.com/lC9EMk0.png)
* 選擇creat程式
---
![p2](https://i.imgur.com/6gUdfTT.png)
* 選擇next
---
![p2](https://i.imgur.com/cDFjI1z.png)
* 5.x是對應到Linux的版本
---
![p3](https://i.imgur.com/rH5zAeA.png)
* 命名檔案名稱(A為Alpine R為router 55為網段 3為第三台Alpine)
---
![p4](https://i.imgur.com/rZzfMKT.png)
* 設定硬碟大小
* 統合所有資料為一個資料夾
---
![p5](https://i.imgur.com/wRMaaqz.png)
* 設定memory
* 設定CPU(要看所跑程式而定) 
* 設定Alpine光碟
* 設定NAT網路服務
* remove多餘程式(USB,Sound Card,Printer)
---
![p6](https://i.imgur.com/uiy1vb6.png)
* 點選啟動程式
---
![p7](https://i.imgur.com/JVzz5jL.png)
```
輸入:root
```
![p8](https://i.imgur.com/hr5kS1C.png)

```
輸入:setup-alpine
```
---
![p9](https://i.imgur.com/uqGYd4K.png)
* 輸入1:tw"taiwan"(鍵盤輸入法)
* 輸入2:tw(備用鍵盤輸入法)
* 輸入hostname:ar5503(不能使用大寫,只能使用[a-z],[1-9],[-])

![p10](https://i.imgur.com/BpLR4YK.png)
* 網卡名稱:eth0
* 賦予網卡IP address:DHCP(自動分配IP)
* 沒有額外設定選[n]
![p11](https://i.imgur.com/98Ewluc.png)
* 輸入新密碼:root
* 確認新密碼:root
![p12](https://i.imgur.com/sTgPVgW.png)
* 輸入時區:Asia
* 再次輸入:Taipei
* (首位字母必須大寫)
* proxy-(詢問的是是否需要偽裝公司IP上網)
---
![p13](https://i.imgur.com/hT1xARX.png)
* 選擇載點:37
![p14](https://i.imgur.com/gdvaMMq.png)
* 啟用ssh服務
---
![15](https://i.imgur.com/Bo7y2WN.png)
* 使用硬碟sda讓system使用
* 輸入1:sda
* 輸入2:sys
* 輸入3:y(此問確認是否安裝？)
![16](https://i.imgur.com/POp9w67.png)
* 安裝至硬碟後須重新開機，並退出iso
* 輸入:reboot

第一步
安裝所需套件

`$ apk update`

`$ apk upgrade`

`$ apk add tree nano sudo unzip curl wget zip grep bash procps`
(以及一切你想加入的配件)

`$ tree /home/`
(確認是否安裝成功)

第二步

建立與設定 管理者 帳號

`$ adduser -s /bin/bash -h /home/bigred -D bigred`  
(建立 bigred 管理者帳號，並讓他操作時使用bash，並建立 bigred 群組)

`$ addgroup bigred wheel`  
(將管理者號加入 wheel 群組) (wheel 群組為擁有 sudo 權限的群組)

`$ echo -e "bigred\nbigred\n"|passwd bigred`  
(設定管理者密碼)

`$ nano /etc/sudoers`  
(編輯 sudoers 文件並將下面這行加入進去，可達成 wheel 群組使用 sudo 時不用輸入密碼)

% wheel ALL=(ALL) NOPASSWD: ALL  
(這行打錯就要全部重來了)

`$ sudo passwd -dl root`  
(取消 root 帳號)

`$ nano /etc/hosts`  
(修改電腦名稱)

完成設定之後，記得重新開機