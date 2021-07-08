# practice(namespace=test)
1. Create two pods with nginx image named nginx1 and nginx2 into your namespace . All of them should have the label app=v1.（创建两个pod 一个叫nginx1 一个叫nginx2 在 test的namespace下，所有的pod应该有一个app=v1的label。）
```shell
# 创建namespace
kubectl create ns test
# 创建pod
kubectl run nginx1 --image=nginx -l app=v1 -n test
kubectl run nginx2 --image=nginx -l app=v1 -n test
# 检查创建结果
[root@master ~]# kubectl get pod -n test --show-labels
NAME     READY   STATUS    RESTARTS   AGE   LABELS
nginx1   1/1     Running   0          28s   app=v1
nginx2   1/1     Running   0          24s   app=v1
```

2. Change pod nginx2 label to app=v2.修改nginx2的label 变成app=v2
```shell
[root@master ~]# kubectl label --overwrite pod nginx2 app=v2 -n test
pod/nginx2 labeled
[root@master ~]# kubectl get pod -n test --show-labels
NAME     READY   STATUS    RESTARTS   AGE   LABELS
nginx1   1/1     Running   0          87m   app=v1
nginx2   1/1     Running   0          87m   app=v2
```

3. Create a messaging pod using redis:alpine image with label set to tier=msg. Check pod's labels.（创建一个叫messaging的pod，用redis:alpine的镜像，label设置 tier=msg， 然后查看pod的label。）
```shell
[root@master ~]# kubectl run messaging --image=redis:alpine -l tier=msg  -n test
pod/messaging created
[root@master ~]# kubectl get pod -n test --show-labels
NAME        READY   STATUS    RESTARTS   AGE   LABELS
messaging   1/1     Running   0          26s   tier=msg
nginx1      1/1     Running   0          92m   app=v1
nginx2      1/1     Running   0          92m   app=v2
```

4. Create a busybox-echo pod that echoes 'hello world' and exits. After that check the logs.（创建一个叫busybox-pod的pod，并用命令输出'hell world'，然后检查pod的logs。）
```shell
[root@master ~]# kubectl run busybox-echo --image=nginx --restart=Never -n test -- /bin/sh -c "echo 'hello world'"
pod/busybox-echo created
[root@master ~]# kubectl get pod -n test
NAME           READY   STATUS      RESTARTS   AGE
busybox-echo   0/1     Completed   0          8s
messaging      1/1     Running     0          27m
nginx1         1/1     Running     0          119m
nginx2         1/1     Running     0          119m
[root@master ~]# kubectl logs -n test busybox-echo
hello world
```

5. Create an nginx-test pod and set an env value as var1=val1. Check the env value existence within the pod.（创建一个叫nginx-test的pod，并设置参数var1=val1。然后输出pod的变量var1的值到 ~/pod_env.txt文件中。）
```shell
[root@master ~]# kubectl run nginx-test --image=nginx --env=var1=val1 -n test
pod/nginx-test created
[root@master ~]# kubectl exec -n test nginx-test -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx-test
var1=val1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
NGINX_VERSION=1.21.1
NJS_VERSION=0.6.1
PKG_RELEASE=1~buster
HOME=/root
```

6. Create a configMap called opt with value key5=val5. Create a new nginx-opt pod that loads the value from key key5 in an env variable called OPTIONS. （创建一个叫opt的cm，其中包含key5=val5，创建一个叫nginx-opt的pod并且通过刚创建的cm key5的赋予给这个pod的环境变量OPTIONS的值。）
```shell
[root@master ~]# kubectl create cm opt --from-literal=key5=val5 -n test
configmap/opt created     
[root@master ~]# kubectl get cm -n test opt -o yaml
apiVersion: v1
data:
  key5: val5
kind: ConfigMap
metadata:
  creationTimestamp: "2021-07-07T14:36:30Z"
  name: opt
  namespace: test
  resourceVersion: "132271"
  uid: d15786d6-4711-44f3-8505-cf5556eed3e7
```


- 修改配置文件nginx-opt.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-opt
  name: nginx-opt
  namespace: test
spec:
  containers:
  - image: nginx
    name: nginx-opt
    env:
    - name: OPTIONS
      valueFrom:
        configMapKeyRef:
          name: opt
          key: key5
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- 创建nginx-opt,并查看环境变量
```shell
[root@master ~]# kubectl apply -f nginx-opt.yaml  -n test
pod/nginx-opt created
[root@master ~]# kubectl exec -n test nginx-opt  -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx-opt
OPTIONS=val5
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
NGINX_VERSION=1.21.1
NJS_VERSION=0.6.1
PKG_RELEASE=1~buster
HOME=/root
```

7. Create a configmap anotherone with values var6=val6 and var7=val7. Load this configmap as an env variables into a nginx-sec pod. 创建一个叫anotherone的cm，包含var6=val6和var7=val7 并在一个叫nginx-sec的pod中以环境变量的形式挂载
```shell
[root@master ~]# kubectl create cm anotherone --from-literal=var6=val6 --from-literal=var7=val7 -n test
configmap/anotherone created
[root@master ~]# kubectl get cm -n test anotherone
NAME         DATA   AGE
anotherone   2      12s
[root@master ~]# kubectl get cm -n test anotherone  -o yaml
apiVersion: v1
data:
  var6: val6
  var7: val7
kind: ConfigMap
metadata:
  creationTimestamp: "2021-07-07T15:27:55Z"
  name: anotherone
  namespace: test
  resourceVersion: "136294"
  uid: 30b9e6a0-f5d0-4aea-af2b-c8b528264f15
  [root@master ~]# kubectl run nginx-sec --image=nginx --dry-run=client -o yaml
  apiVersion: v1
  kind: Pod
  metadata:
    creationTimestamp: null
    labels:
      run: nginx-sec
    name: nginx-sec
  spec:
    containers:
    - image: nginx
      name: nginx-sec
      resources: {}
    dnsPolicy: ClusterFirst
    restartPolicy: Always
  status: {}
```

- 修改配置文件nginx-sec.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-sec
  name: nginx-sec
spec:
  containers:
  - image: nginx
    name: nginx-sec
    envFrom:
    - configMapRef:
        name: anotherone
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- 新建nginx-sec,并查看env是否设置成功
```shell
[root@master ~]# kubectl apply -f nginx-sec.yaml  -n test
pod/nginx-sec created
[root@master ~]# kubectl get pod -n test
NAME           READY   STATUS      RESTARTS   AGE
busybox-echo   0/1     Completed   0          9h
messaging      1/1     Running     0          10h
nginx-opt      1/1     Running     0          9h
nginx-sec      1/1     Running     0          12s
nginx-test     1/1     Running     0          9h
nginx1         1/1     Running     0          11h
nginx2         1/1     Running     0          11h
[root@master ~]# kubectl exec  nginx-sec -n test  -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx-sec
var6=val6
var7=val7
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
NGINX_VERSION=1.21.1
NJS_VERSION=0.6.1
PKG_RELEASE=1~buster
HOME=/root
```

8. Create a configMap cmvolume with values var8=val8 and var9=val9. Load this as a volume inside an nginx-cm pod on path /etc/spartaa. Create the pod and 'ls' into the /etc/spartaa directory. 创建一个叫cmvolume的cm包含var8=val8 和var9=val9。将这个cm挂载至一个叫nginx-cm的pod中去。
```shell
[root@master ~]# kubectl create cm cmvolume --from-literal=var8=val8 --from-literal=var9=val9 -n test
configmap/cmvolume created
[root@master ~]# kubectl get cm -n test cmvolume -o yaml
apiVersion: v1
data:
  var8: val8
  var9: val9
kind: ConfigMap
metadata:
  creationTimestamp: "2021-07-08T00:12:29Z"
  name: cmvolume
  namespace: test
  resourceVersion: "177189"
  uid: a562e3ce-134d-4a31-9d66-2f24a3938118
```

- 修改nginx-cm.yaml配置文件
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-cm
  name: nginx-cm
  namespace: test
spec:
  volumes:
  - name: config
    configMap:
      name: cmvolume
  containers:
  - image: nginx
    name: nginx-cm
    volumeMounts:
    - name: config
      mountPath: /etc/spartaa
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- 创建nginx-cm，并查看configmap是否挂载成功
```shell
[root@master ~]# kubectl apply -f nginx-cm.yaml  -n test
pod/nginx-cm created
[root@master ~]# kubectl get pod -n test
NAME           READY   STATUS      RESTARTS   AGE
busybox-echo   0/1     Completed   0          10h
messaging      1/1     Running     0          10h
nginx-cm       1/1     Running     0          8s
nginx-opt      1/1     Running     0          9h
nginx-sec      1/1     Running     0          17m
nginx-test     1/1     Running     0          9h
nginx1         1/1     Running     0          12h
nginx2         1/1     Running     0          12h
[root@master ~]# kubectl exec -n test nginx-cm  -- ls /etc/spartaa
var8
var9
[root@master ~]# kubectl exec -n test nginx-cm  -- cat /etc/spartaa/var8
val8
[root@master ~]# kubectl exec -n test nginx-cm  -- cat /etc/spartaa/var9
val9
```
9. Create a secret called mysecret with values password=mypass and check its yaml. 创建一个叫mysecret的secret 包含password=mypass的值。检查他的yaml描述文件。
```shell
[root@master ~]# kubectl create secret generic mysecret --from-literal=password=mypass -n test
secret/mysecret created
[root@master ~]#
[root@master ~]# kubectl get secret -n test mysecret -o yaml
apiVersion: v1
data:
  password: bXlwYXNz
kind: Secret
metadata:
  creationTimestamp: "2021-07-08T00:24:49Z"
  name: mysecret
  namespace: test
  resourceVersion: "178165"
  uid: 4028dd62-0627-4b2b-8bba-5dd808c0e034
type: Opaque
[root@master ~]# kubectl get secret -n test mysecret -ojsonpath="{.data.password}" | base64 -d
mypass
```

10. Create an nginx pod that mounts the secret mysecret in a volume on path /etc/foo. 创建一个nginx的pod，并把mysecret挂载进/etc/foo下面。
```shell
[root@master ~]# kubectl run nginx --image=nginx -n test --dry-run=client -o yaml > nginx.yaml
```

- 修改nginx.yaml配置文件
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
  namespace: test
spec:
  volumes:
  - name: secret
    secret:
      secretName: mysecret
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - name: secret
      mountPath: /etc/foo
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- 创建nginx，并查看secret是否挂载成功
```shell
[root@master ~]# kubectl apply -f nginx.yaml
pod/nginx created
[root@master ~]# kubectl get pod -n test
NAME           READY   STATUS      RESTARTS   AGE
busybox-echo   0/1     Completed   0          10h
messaging      1/1     Running     0          10h
nginx          1/1     Running     0          7s
nginx-cm       1/1     Running     0          12m
nginx-opt      1/1     Running     0          9h
nginx-sec      1/1     Running     0          30m
nginx-test     1/1     Running     0          9h
nginx1         1/1     Running     0          12h
nginx2         1/1     Running     0          12h
[root@master ~]# kubectl exec nginx -n test -- ls /etc/foo
password
[root@master ~]# kubectl exec nginx -n test -- cat /etc/foo/password
mypass
```