# **Proxy**

![p1](https://i.imgur.com/bVJZBdV.png)

當現在要從king連上網站,不經由AR23301的NAT進行轉址服務,將AP23301作為proxy server

1. 首先我們需要更新套件清單
`sudo apk update`

2. 下載squid
`sudo apk add squid`

3. 啟動squid
`sudo rc-service squid start`

4. 讓squid開機自動執行
`sudo rc-update add squid`


![p1](https://i.imgur.com/V7e5omK.png)

將條件打在acl底下

acl (條件名稱) src (IP位址)


* 注意有可能因為電腦名稱抓不到而出現error

1. 輸入: `sudo nano /etc/hosts`

![p2](https://i.imgur.com/Di5okHD.png)

* 在127.0.0.1 後面加上目前主機名稱

* 在沒有限制的規則下,每一台都能連上網站,但卻ping不到網路




