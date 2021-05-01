# **Container(貨櫃) 運作架構**

![p1](https://i.imgur.com/1bNWVLb.png)

Opercation(軟體) Container(貨櫃)就是把program 隔離變成一台電腦
(Oprecation Container沒有開機程序,因為共用kernel)

isolated = 隔離
APP = program
/bins/libs :
相依檔
.so (system object)
.ko (kernel object)
Host OS (Kernel)

## **1. Namespace**

![p1](https://i.imgur.com/NODn0qB.png)

ipc : Containere溝通管道
mnt(mount) : 掛載到某一個目標區
uts : 電腦名稱

`sudo unshare --pid --fork --uts sh`
(sh可以有自己的子程序)

`$ps`

![p2](https://i.imgur.com/5urRQ3Y.png)

* ps命令還是會看到bigred主機的所有的Process資訊,這是因爲ps命令會讀取bigred主機的/proc目錄資訊
(ps命令一定讀取/proc目錄資訊,因為此時/proc還在bigred裡)

`exit` (退出container)

`sudo unshare --pid --fork --mount-proc sh`

* --mount-proc這參數,會將sh的PID Namespace,掛載到主機的/proc目錄,由以下命令,得知/proc目錄被掛載二次

這時輸入ps指令,可以看到/proc已經被新的蓋掉

![p3](https://i.imgur.com/onZI5m5.png)

* 當我們輸入kill -9 sh可以發現我們無法成功,所以得知root會被限制




`mount | grep -e "^proc"`
* 由此命令可知/proc目錄被掛載二次

![p4](https://i.imgur.com/UqvNvfp.png)

## **2. slirp4netns**

`sudo unshare --pid --fork --mount-proc --net --uts sh`

--net (給這個專屬空間一個網路空間,而沒有網路設備)

![p5](https://i.imgur.com/2qqZBle.png)

`ps aux | grep ' sh$'` (回到主機輸入)

![p6](https://i.imgur.com/zuVf09s.png)

`sudo slirp4netns --configure  --disable-host-loopback   --mtu=65520 5067 tap0`
(給container一張網卡叫tap0)

![p7](https://i.imgur.com/moSdh4F.png)

當我們回到container端時,可以看到我們多了一張網卡

![p7](https://i.imgur.com/ySvozVL.png)

雖然ping不到8.8.8.8但可以發現當我們去curl網站時是可以成功的

## **3. chroot**

![p8](https://i.imgur.com/knEaGrn.png)

![p9](https://i.imgur.com/pjsroRx.png)

`sudo unshare --pid --fork --mount-proc --uts -R r2d2/rootfs sh` (-R = chroot)

![p10](https://i.imgur.com/LOWpchC.png)

![p11](https://i.imgur.com/FYWTRYk.png)

## **Overlay2**

`mkdir upper lower merged work`

`echo "I'm from lower!" > lower/in_lower.txt`

`echo "I'm from upper!" > upper/in_upper.txt`

`echo "I'm from lower!" > lower/in_both.txt`

`echo "I'm from upper!" > upper/in_both.txt` 

`sudo mount -t overlay overlay -o lowerdir=/home/bigred/lower,upperdir=/home/bigred/upper,workdir=/home/bigred/work  /home/bigred/merged`

當你對merged裡做事時,事實上是在upper進行,當upper和lower都由一個共同檔案時,看到的是在upper的檔案

![p12](https://i.imgur.com/Z53BJXN.jpg)

