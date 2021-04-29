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
