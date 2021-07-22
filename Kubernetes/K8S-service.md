---
tags: K8S
---

# **Kubernetes Service**

![p1](https://i.imgur.com/SSO3mmx.png)

Pod 如果要連出去需要透過 K8S service

```
$ echo 'kind: Deployment
apiVersion: apps/v1
Metadata:
  name: s1.dep
Spec:
  replicas: 2
  selector:
    matchLabels:
      app: s1.pod
  template:
    metadata:
      labels:
        app: s1.pod
    spec:
      containers:
      - name: derbyapp
        image: quay.io/cloudwalker/alpine.derby
        imagePullPolicy: Always
        ports:
        - name: http
          containerPort: 8888  '> s1.dep.yml

```
Selector : 用來選出標籤叫做 s1.pod
template : 用來宣告 pod

![p2](https://i.imgur.com/oi7K8eX.png)

DepB : 管理三個 pod 分別在不同主機,且擁有不同的 IP
A,B : 都是Deployment

### **Service NodePort**

![p3](https://i.imgur.com/16E7sjm.png)

`$ kubectl create -f s1.dep.yml `

```
$ nano s1.nodetype.yml 
apiVersion: v1
kind: Service
metadata:  
  name: my-nodeport-service
spec:
  selector:
    app: s1.pod
  type: NodePort
  ports:
  - port: 9999
    targetPort: 8888
    nodePort: 3036
    protocol: TCP 
```

`$ kubectl apply -f s1.nodetype.yml `
The Service "my-nodeport-service" is invalid: spec.ports[0].nodePort: Invalid value: 3036: provided port is not in the valid range. The range of valid ports is 30000-32767 (port 只能在 30000-32767 之間)

修改 NodePort 為 30036
```
$ nano s1.nodetype.yml 
apiVersion: v1
kind: Service
metadata:  
  name: my-nodeport-service
spec:
  selector:
    app: s1.pod
  type: NodePort
  ports:
  - port: 9999
    targetPort: 8888
    nodePort: 30036
    protocol: TCP 
```

`$ kubectl apply -f s1.nodetype.yml`

`$ kubectl get service my-nodeport-service`
NAME                  TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
my-nodeport-service   NodePort   10.109.215.18   <none>  9999:30036/TCP   76s


`$ curl http://localhost:30036/hostname`
Hostname : s1.dep-66bf498c7b-fbwn9
`$ curl http://localhost:30036/hostname`
Hostname : s1.dep-66bf498c7b-6pbkq

`$ curl http://10.109.215.18:9999/hostname`
Hostname : s1.dep-66bf498c7b-fbwn9
`$ curl http://10.109.215.18:9999/hostname`
Hostname : s1.dep-66bf498c7b-6pbkq

在 m1 及 w1 執行以下命令
`$ netstat -an | grep 30036`
tcp        0      0 0.0.0.0:30036           0.0.0.0:*               LISTEN

### **Service External IP**

Iptables 不需要全開,只要開要連的機器

```
$ nano s1.externalip.yml 
kind: Service
apiVersion: v1
metadata:
  name: myextip
spec:
  externalIPs:
  - 120.96.143.168
  - 120.96.143.169
  selector:
    app: s1.pod
  ports:
  - port: 8080
    targetPort: 8888
```

修改此 IP 位址, 可以是 Kubernetes 叢集中任何一部實體主機對外的 IP 位址, 包括 Master 主機

### **DNS for Service**

```
$ nano service-dns.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: k8s-nginx
spec:
  selector:
    matchLabels:
      run: k8s-nginx
  replicas: 3
  template:
    metadata:
      labels:
        run: k8s-nginx
    spec:
      containers:
      - name: k8s-nginx
        image: quay.io/cloudwalker/nginx
        imagePullPolicy: Never
        ports:
        - containerPort: 80
---

apiVersion: v1
kind: Service
metadata:
  name: svc-cluster
spec:
  selector:
    run: k8s-nginx
  ports:
  - name: http
    port: 80
    protocol: TCP
    
---

kind: Service
apiVersion: v1
metadata:
  name: svc-headless
spec:
  selector:
    run: k8s-nginx
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 80
  clusterIP: None
  
---
```

**template: 宣告 pod**

`---` : 作為分段使用,需要使用的元件可以全部一起上

(clusterIP: None): 是指不需要 clusterIP














