# **chroot**

* curl (網站: 砍網頁,砍檔案)

* alpine-minirootfs-3.13.4-x86_64.tar.gz
(Minirootfs檔案)(tar把很多檔案打包的打包檔)(Tar.gz(先打包再壓縮))

* 下載minirootfs檔案

## **chroot**

`sudo chroot rootfs/ /bin/sh`

root@ALP:/$ `whoami`
root

root@ALP:/$ `ls -al /proc`
total 8
dr-xr-sr-x    2 1000     1000          4096 Mar 31 16:51 .
drwxr-sr-x   19 1000     1000          4096 Apr 27 22:41 ..

root@ALP:/$ `mkdir /proc/hi`
 
root@ALP:/$ `exit`

bigred@ALP:~$ `ls -al rootfs/proc`
total 12
dr-xr-sr-x  3 bigred bigred 4096 Apr 28 07:01 .
drwxr-sr-x 19 bigred bigred 4096 Apr 28 06:41 ..
drwxr-sr-x  2 root   bigred 4096 Apr 28 07:01 hi

## **Web server**

root@ALP:/$ `httpd`
/bin/sh: httpd: not found

root@ALP:/$ `which httpd`
沒有顯示(代表我現在rootfs裡的busybox沒有httpd)

去busybox官網下載(Download binaries)有httpd(通常busybox的大小為800k以上)

![p1](https://i.imgur.com/G8eFVjW.png)

下載後的busybox原本是沒有執行權限

bigred@ALP:~$ `cp /home/bigred/bin/httpd rootfs/bin/`

root@ALP:/$ `mkdir /opt/www`
root@ALP:/$ `echo "<h1>hello my friend</h1>" | tee /opt/www/index.html`
root@ALP:/$ `httpd &`

![p2](https://i.imgur.com/2tf3wQ5.png)

## **在rootfs裡創帳號**

bigred@ALP:~$ `sudo chroot --userspec=bigred:bigred  rootfs/ /bin/sh`

![p3](https://i.imgur.com/hDXmePL.png)

會發現根本沒有bigred這個帳號,所以需要先在rootfs裡先建立一個bigred的帳號

![p4](https://i.imgur.com/0v0EpDi.png)

----------------------------------

# **Cgroup**

透過使用cgroup，系統管理員能取得分配、處理優先順序、拒絕、管理，以及監控系統資源的細部控制。硬體資源可機敏地分配於工作與使用者之間，並提昇整體效率

由於cgroup的controller滿多，功能也很廣，這邊只針對幾個做說明 : 

1. cpuset： 設定使用那幾個 cpu core
2. cpu 和 cpuacct 是同一個：設定 cpu shares，share 值越大，能用 cpu 的時間越多
3. memory：限制記憶體使用量

簡單來說就是cgroup會拷貝上一層內容繼承下去,也可以限制process的運算資源,來保護主機不被攻擊

## **限制memory**

`sudo mkdir /sys/fs/cgroup/memory/demo`

* 創建一個叫demo的目錄

`ls -al /sys/fs/cgroup/memory/demo/`

![p5](https://i.imgur.com/QTMYNsF.png)

* 會繼承上一層memory的內容

`nano memlimit.sh` 

```
#!/bin/bash

[ ! -d /sys/fs/cgroup/memory/demo ] && sudo mkdir /sys/fs/cgroup/memory/demo

echo "150000000" | sudo tee /sys/fs/cgroup/memory/demo/memory.limit_in_bytes &>/dev/null

echo $$ | sudo tee /sys/fs/cgroup/memory/demo/tasks &>/dev/null

cat /dev/zero | head -c $1 | tail
```

`./memlimit.sh`

![p6](https://i.imgur.com/XQJOEBj.png)

* 當成功時並不會有error的出現,但如果超出所限定的範圍,就會無法執行

## **自定cpu控制群組**

`cat /sys/fs/cgroup/cpu/cpu.shares` (預設是1024)

`sudo mkdir /sys/fs/cgroup/cpu/low`

`sudo ls -al /sys/fs/cgroup/cpu/low`

![p7](https://i.imgur.com/KI48caC.png)

* 以root來建立low控制子群組，low繼承了cpu controller的所有內容

`sudo chown bigred:root -R /sys/fs/cgroup/cpu/low`

* 設定low控制子群組權限, 將權限開給bigred使用者

`echo 512 | sudo tee /sys/fs/cgroup/cpu/low/cpu.shares`

* 將low控制子群組分配比率設成512

`sudo mkdir /sys/fs/cgroup/cpu/high`

* 建立cpu的high控制子群組

`sudo chown bigred:root -R /sys/fs/cgroup/cpu/high`

`echo 2048 | sudo tee /sys/fs/cgroup/cpu/high/cpu.shares`

* 將high控制子群組分配比率設成2048

計算的比率為:

![p8](https://i.imgur.com/zxHFQvG.png)

* 建立cpuset控制子群組first

`sudo mkdir /sys/fs/cgroup/cpuset/first`

* 設定first控制子群組權限

`sudo chown bigred:root -R /sys/fs/cgroup/cpuset/first`

* 檢視原本cpuset 的設定：

`cat /sys/fs/cgroup/cpuset/cpuset.cpus`

![p9](https://i.imgur.com/ijG2wVR.png)

* 有二個cpu core,分別是0,1

`echo 0 | sudo tee /sys/fs/cgroup/cpuset/first/cpuset.cpus`

* 設定first控制子群組,將first控制子群組設成第一個cpu core

`yes &>/dev/null &`

`yes &>/dev/null &`

![p10](https://i.imgur.com/ke61ud4.png)




`echo 0 | sudo tee /sys/fs/cgroup/cpuset/first/cpuset.mems`

`echo 7545 | sudo tee /sys/fs/cgroup/cpuset/first/tasks`

`echo 7546 | sudo tee  /sys/fs/cgroup/cpuset/first/tasks`

`cat /sys/fs/cgroup/cpuset/first/tasks`

`$top`

![p11](https://i.imgur.com/V07CFVK.png)

可以看到cpu的使用量為平均分配,基本上是50%,50%

`echo 7545 | sudo tee /sys/fs/cgroup/cpu/low/tasks`


`echo 7546 | sudo tee /sys/fs/cgroup/cpu/high/tasks`

![p12](https://i.imgur.com/wQ9qGeA.jpg)

可以看到比率變成80%,20%