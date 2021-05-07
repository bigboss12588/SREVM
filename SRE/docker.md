# **Docker**

![p1](https://i.imgur.com/mni84c7.png)

很多人都覺得 Docker daemon(高階命令) 是個新技術，其實不然，Docker 除了其開發語言用 go 比較新外，其實它還真不是個新東西，也就是個新瓶裝舊酒的東西

Docker 使用的 Linux 核心模組功能有下列各項：
1. Namespace - 用來隔離不同 Container 的執行空間
2. Cgroup - 用來分配硬體資源
3. chroot - 針對正在運作的軟體行程和它的子行程，改變它的根目錄
4. Bridge / Tun (Network) - 建立虛擬網路, 連接不同 Container 
5. Overlay2(chroot) - 用來建立不同 Container 的檔案系統
6. slirp4netns (Network)

![p2](https://i.imgur.com/jrkQPMt.png)

![p3](https://i.imgur.com/CZM3XC9.png)

* Docker現況 : 因為他的Docker Daemon太多,會成為安全破口,再加上Docker Engine權力大,入侵就等於完蛋

![p4](https://i.imgur.com/dCOnxoC.png)

Podman : 在沒有Daemon的情況下也能產生Container

## **Runc**

* OCI(Container的設定檔,rootfs的檔案系統目錄)官方基於Runtime規範,實現了一個解決方案,稱為RunC,這意味者藉由這套工具並且搭配適宜的設定就,可以輕鬆的創建出一個符合OCI標準的Container運行。

ps:RunC並不是只包含Linux Namespace 及chroot,它還包含CGroup

`mkdir rootfs; cd rootfs; curl -o alpine.tar.gz http://dl-cdn.alpinelinux.org/alpine/v3.13/releases/x86_64/alpine-minirootfs-3.13.5-x86_64.tar.gz; tar xvf alpine.tar.gz ;rm alpine.tar.gz; cd ..`

![p5](https://i.imgur.com/IMmdCu5.png)

`sudo apk update &>/dev/null ; sudo apk add runc`
下載runc

`sudo chown root:root -R rootfs/`
因為runc命令內定執行帳號為root,而rootfs目錄的Owner是bigred,所以將 rootfs目錄的owner改為root

`nano config.json` 
(container 設定檔)

![p6](https://i.imgur.com/Y8J7Ndi.png)

![p7](https://i.imgur.com/sZSv8Zz.png)

![p8](https://i.imgur.com/4nu70Ji.png)

* /#代表你為root

![p9](https://i.imgur.com/HITlMoE.png)

`rm /proc/meminfo`(/proc是Kernel的) (seccomp X process)

![p10](https://i.imgur.com/F0NyY6x.png)

## **Containerd**

![p11](https://i.imgur.com/pau38xX.png)

`sudo apk add containerd` 下載containerd

`containerd -v` 看containerd版本

`sudo containerd &` 背景執行


![p12](https://i.imgur.com/vVZ5dCH.png)

## **Docker(easy)**

Docker現狀 : Docker Daemon太多,會成為安全破口,Docker Engine權力大,入侵就完蛋


`sudo apk add docker` 下載docker

`sudo rc-update add docker boot` 設定Docker Daemon自動啟動

`sudo addgroup bigred docker` 將bigred帳號加入docker群組後,就不需使用sudo命令執行docker

`sudo  reboot` 重新開機 (一定要執行)

![p13](https://i.imgur.com/nnRlni3.png)


![p14](https://i.imgur.com/UzF1RQF.png)

## **Docker(Application Container)**

![p15](https://i.imgur.com/hqHVob1.png)

`docker search busybox` 搜尋busybox

`docker pull busybox` 從docker網站下載

![p16](https://i.imgur.com/Iy2lgyi.png)

虛擬終端機只run貝殼程式,當你沒給主機名稱時,他會隨機配發名稱

![p17](https://i.imgur.com/gn8CP7Y.png)

## **建立Nginx網站**

`$ docker run -d --restart=always --name n1 nginx` (-d=背景執行)

`$ docker ps -a`

![p18](https://i.imgur.com/tDPbFPG.png)

`$ sudo reboot`

`$ docker ps -a`

![p20](https://i.imgur.com/N2jip3D.png)


[註]Docker Host重啟內定不會自動啟動Container,除非指定 --restart=always **(開機後跟著啟動)** 這參數,Container一但設定自動重啟,並不會重新建立一個新的Container,由以下 Container ID可以得知

`$ docker rm -f n1`

## **Docker 運算資源管理**

限制Memory

![p21](https://i.imgur.com/zKeuDCm.png)

![p22](https://i.imgur.com/lB7IClB.png)

限制CPU

![p23](https://i.imgur.com/hxaj47w.png)

限制Hard Disk

![p24](https://i.imgur.com/OcWbR89.png)

