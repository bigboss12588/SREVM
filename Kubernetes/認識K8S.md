# **認識 Kubernetes**

- Replication of components
- Auto-scaling
- Load balancing
- Rolling updates
- Logging across components
- Monitoring and health checking
- Service discovery
- Authentication

![p1](https://i.imgur.com/U5XL4n2.png)

![p2](https://i.imgur.com/5MTPgDP.png)

CRI-O : 就是為了 k8s 的 CRI

CRI (Container Runtime Interface) : k8s 要透過 CRI 標準產生 pod  
CNI (Container Network Interface) : 透過 static route ,制定網路標準  
CSI (Container Storage Interface ) : 各式各樣的儲存系統,專屬的檔案系統  

## kubernetes CRI 的架構演進圖

![p3](https://i.imgur.com/Nf5UCYl.png)

1. kubelet 透過 Dockershim 與 docker engine 連接，最後一路串接到 containerd 來創建 container  
2. 繞過 Docker 直接與後端的 Containerd 溝通，為了滿足這個需求也需要一個額外的應用程式 CRI-Containerd 來作為中間溝通的橋樑  
3. 隨者 containerd 1.1 版本的發行， CRI-Containerd 本身的功能已經可以透過 plugin 的方式實現於 containerd 中，可以再少掉一層溝通的耗損  
4. CRI-O 一個完全針對 kubernetes 需求的解決方案，讓整體的溝通變得更快速與簡單  


### Kubernetes Pods (CRI) 

![p4](https://i.imgur.com/B7I9nGt.png)

### Kubernetes Pods (CNI) 

![p5](https://i.imgur.com/h3kInKY.png)

### Kubernetes Pods (CSI)

![p6](https://i.imgur.com/D6BDyMX.png)

### Kubernetes Pods (Scheduling) 

![p7](https://i.imgur.com/Kcxhsyg.png)

### Kubernetes Pods (High Availability) 

![p8](https://i.imgur.com/fK2cjdJ.png)

### Kubernetes Master 容錯

![p9](https://i.imgur.com/QdFrKNf.png)














