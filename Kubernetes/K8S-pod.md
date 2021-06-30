---
tags: K8S
---
# **K8S pods**

![p1](https://i.imgur.com/fJ174c0.png)

![p2](https://i.imgur.com/M4utjJ4.png)

![p3](https://i.imgur.com/N0nM33i.png)

因為兩個 container 共用一個 pid , 不能 kill pause 因為 **capabilities**

![p4](https://i.imgur.com/EwsYZZg.png)

### Multi-Container Pod Design Patterns

![p5](https://i.imgur.com/08O3CdH.png)

Sidecar: 公司 app 一定會產生 log 紀錄 , sidecar 責整理,他們都在同一個 pods 裡的 container  
Adapter: 很多個公司 app , adapter 會給一個下拉選單,方便客戶使用  
Ambassador: 到外網蒐集資料  

![p6](https://i.imgur.com/jEfQWu3.png)

emptyDir: 會在 linux 系統產生資料夾(目錄)
```
volumeMounts:
   - name: shared-logs
   mountPath: /var/log/nginx
```
volumeMounts: 是指 linux 系統產生目錄 mount 到指定的 /var/log/nginx 目錄

### Assigning Pods to Nodes

![p7](https://i.imgur.com/EEGDMHN.png)

```
  nodeSelector:
    kubernetes.io/hostname : 120-96-143-167-m1 
```
指定在哪一個 node 上執行 pod

### Pod Namespace

![p8](https://i.imgur.com/zznvA5x.png)

```
$ kubectl create -f pod-n1.yaml
Error from server (NotFound): error when creating "pod-n1.yaml": namespaces "myring" not found
```

```
$ kubectl create namespace myring
namespace/myring created
```

```
$ kubectl create -f pod-n1.yaml
pod/n1 created
```

```
$ kubectl get pod
No resources found in default namespace.
```

```
$ kubectl get pod -n myring
NAME   READY   STATUS    RESTARTS   AGE
n1     1/1     Running   0          33s
```


