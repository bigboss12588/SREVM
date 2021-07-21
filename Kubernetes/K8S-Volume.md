---
tags: K8S
---
# **Kubernetes Volume**

![p1](https://i.imgur.com/CuHQfvm.png)

Overlay2 : container 存在檔案系統才存在
emptyDir : pod 存在時存在
PV(Persistent volume) : K8S 叢集存在他就在

hostPath 就是 PV ,它會自動產生 /opt/hostpath 目錄, pod-hp 這個 POD 如被刪除, /opt/hostpath 會被保留

## **Persistent Volumes 運作架構**

![p2](https://i.imgur.com/IMOfhjh.png)

Access Modes 說明

PV 可以透過 ReadWriteOnce(一個 node 可 read-write，可縮寫為 RWO), ReadOnlyMany(一個 node 可 write，多個 node 可 read-only，可縮寫為 ROX), ReadWriteMany(多個 node 可 read-write，可縮寫為 RWX) 三種存取模式提供掛載，但存取控制機制本身並非由 k8s 支援，而是由 PV resource provider 支援（例如上面的例子為 NFS，就可以支援 ReadWriteMany，但 Ceph RBD 則不行）

此外，雖然 NFS 可以提供 ReadWriteMany access mode，但在 NFS share 本身的設定就有可能只是 RO，因此在 PV 上還是會有 Access Mode 的設定，主要的用意就是要用來告訴使用者目前這個 PV 所支援的 access mode 為何


### Local PersistentVolume

Local PV 只能使用 yaml 檔建立

```
$ echo 'kind: PersistentVolume
apiVersion: v1
metadata:
  name: pv-local
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  local:
    path: "/opt/local"
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
           - k8sm1 ' > pv-local.yaml 

```

在 k8sm1 主機, 必需自行先建立 /opt/local 目錄

