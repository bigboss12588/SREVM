---
tags: K8S
---

# **建置 Kubernetes 叢集**

1. kubectl **run** (命令式 Imperative) - Manage K8s object (POD, Controller, Service) using CLI  
2. kubectl **create+yaml** (聲明式 Declarative) - By defining K8s objects in yaml file  

### 佈署 Kubernetes POD

![p1](https://i.imgur.com/PqqYDVY.png)

* K8S 的 pod 並不會永久存在 , 重開機後就換新的 pod

### 佈署 Deployment Object

![p2](https://i.imgur.com/rC5KUgk.png)

編輯 yaml 檔

![p3](https://i.imgur.com/2oX37Uq.png)

$ kubectl create -f depobj.yml

$ kubectl get all

### 佈署 Kubernetes Service

![p4](https://i.imgur.com/qcfOSM4.png)

![p5](https://i.imgur.com/4gojpuB.png)

$ kubectl create -f mysrv.yml

$ kubectl get service










