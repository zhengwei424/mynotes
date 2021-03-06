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
11. Create a deployment named hr-app using the image nginx:1.18 with 2 replicas.(创建一个使用nginx:1.18镜像名叫hr-app的deployment，设置副本数为2)
```shell
[root@master ~]# kubectl create deployment hr-app --image=nginx:1.18 --replicas=2 -n test
deployment.apps/hr-app created
[root@master ~]# kubectl get pod -n test
NAME                      READY   STATUS      RESTARTS   AGE
busybox-echo              0/1     Completed   0          22h
hr-app-844c498b44-5rbbp   1/1     Running     0          27s
hr-app-844c498b44-rlwkv   1/1     Running     0          28s
messaging                 1/1     Running     0          22h
nginx                     1/1     Running     0          11h
nginx-cm                  1/1     Running     0          12h
nginx-opt                 1/1     Running     0          21h
nginx-sec                 1/1     Running     0          12h
nginx-test                1/1     Running     0          21h
nginx1                    1/1     Running     0          24h
nginx2                    1/1     Running     0          24h
[root@master ~]# kubectl get deploy -n test
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
hr-app   2/2     2            2           29s
```

12. Scale hr-app deployment to 3 replicas.(滚动hr-app这个deployment到3副本)
```shell
[root@master ~]# kubectl scale --replicas=3 deployment -n test hr-app
deployment.apps/hr-app scaled
[root@master ~]# kubectl get deploy -n test hr-app
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
hr-app   3/3     3            3           5m36s
[root@master ~]# kubectl get pod -n test
NAME                      READY   STATUS      RESTARTS   AGE
busybox-echo              0/1     Completed   0          22h
hr-app-844c498b44-5rbbp   1/1     Running     0          5m44s
hr-app-844c498b44-lmmkx   1/1     Running     0          45s
hr-app-844c498b44-rlwkv   1/1     Running     0          5m45s
messaging                 1/1     Running     0          22h
nginx                     1/1     Running     0          11h
nginx-cm                  1/1     Running     0          12h
nginx-opt                 1/1     Running     0          21h
nginx-sec                 1/1     Running     0          12h
nginx-test                1/1     Running     0          21h
nginx1                    1/1     Running     0          24h
nginx2                    1/1     Running     0          24h
```

13. Update the hr-app image to nginx:1.19.(升级hr-app的镜像到nginx:1.19)
```shell
[root@master ~]# kubectl set image  deploy -n test hr-app nginx=nginx:1.19 --record
deployment.apps/hr-app image updated
```
14. Check the rollout history of hr-app and confirm that the replicas are OK.(检查rollout历史，确认副本已经滚动完成)
```shell
[root@master ~]# kubectl rollout history deployment hr-app -n test
deployment.apps/hr-app
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deploy hr-app nginx=nginx:1.19 --namespace=test --record=true
[root@master ~]# kubectl get deploy -n test hr-app
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
hr-app   3/3     3            3           13m
[root@master ~]# kubectl get pod -n test
NAME                      READY   STATUS      RESTARTS   AGE
busybox-echo              0/1     Completed   0          22h
hr-app-7996699d47-ncbjc   1/1     Running     0          3m56s
hr-app-7996699d47-nhh6z   1/1     Running     0          4m27s
hr-app-7996699d47-zgbvc   1/1     Running     0          3m59s
messaging                 1/1     Running     0          22h
nginx                     1/1     Running     0          12h
nginx-cm                  1/1     Running     0          12h
nginx-opt                 1/1     Running     0          21h
nginx-sec                 1/1     Running     0          12h
nginx-test                1/1     Running     0          22h
nginx1                    1/1     Running     0          24h
nginx2                    1/1     Running     0          24h
[root@master ~]# kubectl get pod  -n test hr-app-7996699d47-ncbjc -o jsonpath="{.spec.containers[0].image}"
nginx:1.19
```

15. Undo the latest rollout and verify that new pods have the old image (nginx:1.18)(重新回滚至老版本的nginx:1.18)
```shell
[root@master ~]# kubectl rollout history deployment -n test hr-app
deployment.apps/hr-app
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deploy hr-app nginx=nginx:1.19 --namespace=test --record=true
[root@master ~]# kubectl rollout undo deployment -n test hr-app --to-revision 1
deployment.apps/hr-app rolled back
[root@master ~]# kubectl rollout history deployment -n test hr-app
deployment.apps/hr-app
REVISION  CHANGE-CAUSE
2         kubectl set image deploy hr-app nginx=nginx:1.19 --namespace=test --record=true
3         <none>
[root@master ~]# kubectl get deployments.apps -n test hr-app
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
hr-app   3/3     3            3           21m
[root@master ~]# kubectl get pod -n test
NAME                      READY   STATUS      RESTARTS   AGE
busybox-echo              0/1     Completed   0          22h
hr-app-844c498b44-mlvzs   1/1     Running     0          119s
hr-app-844c498b44-rpr4w   1/1     Running     0          2m1s
hr-app-844c498b44-wb4j5   1/1     Running     0          2m3s
messaging                 1/1     Running     0          22h
nginx                     1/1     Running     0          12h
nginx-cm                  1/1     Running     0          12h
nginx-opt                 1/1     Running     0          21h
nginx-sec                 1/1     Running     0          12h
nginx-test                1/1     Running     0          22h
nginx1                    1/1     Running     0          24h
nginx2                    1/1     Running     0          24h
[root@master ~]# kubectl get pod -n test hr-app-844c498b44-mlvzs -ojsonpath="{.spec.containers[0].image}"
nginx:1.18
```

16. Do an update of the deployment with a wrong image nginx:1.91 and check the status.(滚动升级至image版本至nginx:1.91，检查副本状态)
```shell
[root@master ~]# kubectl set image deploy -n test hr-app nginx=nginx:1.91 --record
deployment.apps/hr-app image updated
[root@master ~]# kubectl rollout history
error: required resource not specified
[root@master ~]# kubectl rollout history deployment -n test hr-app
deployment.apps/hr-app
REVISION  CHANGE-CAUSE
2         kubectl set image deploy hr-app nginx=nginx:1.19 --namespace=test --record=true
3         <none>
4         kubectl set image deploy hr-app nginx=nginx:1.91 --namespace=test --record=true
[root@master ~]# kubectl get deployments.apps -n test hr-app
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
hr-app   3/3     1            3           25m
[root@master ~]#
[root@master ~]# kubectl get pod -n test
NAME                      READY   STATUS         RESTARTS   AGE
busybox-echo              0/1     Completed      0          22h
hr-app-65d75d95b7-wzr72   0/1     ErrImagePull   0          46s
hr-app-844c498b44-mlvzs   1/1     Running        0          6m20s
hr-app-844c498b44-rpr4w   1/1     Running        0          6m22s
hr-app-844c498b44-wb4j5   1/1     Running        0          6m24s
messaging                 1/1     Running        0          23h
nginx                     1/1     Running        0          12h
nginx-cm                  1/1     Running        0          12h
nginx-opt                 1/1     Running        0          22h
nginx-sec                 1/1     Running        0          12h
nginx-test                1/1     Running        0          22h
nginx1                    1/1     Running        0          24h
nginx2                    1/1     Running        0          24h
[root@master ~]# kubectl get pod -n test hr-app-65d75d95b7-wzr72 -ojsonpath="{.spec.containers[0].image}"
nginx:1.91
```

17. Return the deployment to working state and verify the image is nginx:1.19.(回滚至运行正常的版本：nginx:1.19)
```shell
[root@master ~]# kubectl rollout history  deployment  -n test hr-app
deployment.apps/hr-app
REVISION  CHANGE-CAUSE
2         kubectl set image deploy hr-app nginx=nginx:1.19 --namespace=test --record=true
3         <none>
4         kubectl set image deploy hr-app nginx=nginx:1.91 --namespace=test --record=true
[root@master ~]#
[root@master ~]# kubectl rollout undo deployment -n test hr-app --to-revision 2
deployment.apps/hr-app rolled back
[root@master ~]# kubectl get pod -n test
NAME                      READY   STATUS      RESTARTS   AGE
busybox-echo              0/1     Completed   0          22h
hr-app-7996699d47-dftg8   1/1     Running     0          23s
hr-app-7996699d47-k2m96   1/1     Running     0          25s
hr-app-7996699d47-z7f9x   1/1     Running     0          20s
messaging                 1/1     Running     0          23h
nginx                     1/1     Running     0          12h
nginx-cm                  1/1     Running     0          12h
nginx-opt                 1/1     Running     0          22h
nginx-sec                 1/1     Running     0          12h
nginx-test                1/1     Running     0          22h
nginx1                    1/1     Running     0          24h
nginx2                    1/1     Running     0          24h
[root@master ~]# kubectl get pod -n test -o jsonpath="{.spec.containers[0].image}"
[root@master ~]# kubectl get pod -n test hr-app-7996699d47-dftg8  -o jsonpath="{.spec.containers[0].image}"
nginx:1.19
```
18. Create a PersistentVolume of 1Gi, called 'myvolume-practice'. Make it have accessMode of 'ReadWriteOnce' and 'ReadWriteMany', storageClassName 'normal', mounted on hostPath '/etc/foo'. List all PersistentVolume。(创建一个叫myvolume-practice的pv，1Gi容量，有两个访问模式 'ReadWriteOnce' 和 'ReadWriteMany'， sc名字为“normal”，并通过/etc/foo中。查看所有pv。)
- 创建storageClass的yaml
```yaml
[root@master ~]# cat sc-normal.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: normal
  namespace: test
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsume
```
- 创建local类型的storageClass
```shell
[root@master ~]# kubectl apply -f sc-normal.yaml
storageclass.storage.k8s.io/normal created
[root@master ~]# kubectl get sc -n test
NAME     PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
normal   kubernetes.io/no-provisioner   Delete          WaitForFirstConsumer   false                  7s
```
- 创建myvolume-practice.yaml
```yaml
[root@master ~]# cat myvolume-practice.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: myvolume-practice
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Delete
  storageClassName: normal
  local:
    path: /etc/foo
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: pv
          operator: In
          values:
          - test
```
- 创建pv
```shell
[root@master ~]# kubectl apply -f  myvolume-practice.yaml
persistentvolume/myvolume-practice created
[root@master ~]# kubectl get pv
NAME                CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
myvolume-practice   1Gi        RWO,RWX        Delete           Available           normal                  27s
```
18. Create a PersistentVolumeClaim called 'mypvc-practice' requesting 400Mi with accessMode of 'ReadWriteOnce' and storageClassName of normal. Check the status of the PersistenVolume.(创建一个PVC叫 'mypvc-practice'，400MB，accessMode：'ReadWriteOnce'，sc：normal，检查pvc状态)
- 创建mypvc-practice.yaml
```yaml
[root@master ~]# cat mypvc-practice.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc-practice
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 400Mi
  storageClassName: normal
#  selector:
#    matchLabels:
#      release: "stable"
```
- 创建pvc
```shell
[root@master ~]# kubectl apply -f mypvc-practice.yaml  -n test
persistentvolumeclaim/mypvc-practice created
[root@master ~]# kubectl get pvc -n test
NAME             STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mypvc-practice   Pending                                      normal         9s
```
19. Create a busybox pod with command 'sleep 3600'. Mount the PersistentVolumeClaim mypvc-practice to '/etc/foo'. Connect to the 'busybox' pod, and copy the '/etc/passwd' file to '/etc/foo/passwd'.(创建一个busybox的pod，使用 'sleep 3600' 命令，把这个pvc mount到'/etc/foo'中，进入这个pod，copy '/etc/passwd' file to '/etc/foo/passwd')
- 创建busybox.yaml
```yaml
[root@master ~]# cat busybox.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
  namespace: test
spec:
  volumes:
  - name: test
    persistentVolumeClaim:
      claimName: mypvc-practice
  containers:
  - args:
    - /bin/sh
    - -c
    - sleep 3600
    image: busybox
    name: busybox
    volumeMounts:
    - name: test
      mountPath: /etc/foo/
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
- 创建pod
```shell
[root@master ~]# kubectl apply -f busybox.yaml -n test
pod/busybox created
[root@master ~]# kubectl get pod -n test
NAME                      READY   STATUS      RESTARTS   AGE
busybox                   1/1     Running     0          10s
busybox-echo              0/1     Completed   0          5d2h
hr-app-7996699d47-dftg8   1/1     Running     1          4d4h
hr-app-7996699d47-k2m96   1/1     Running     1          4d4h
hr-app-7996699d47-z7f9x   1/1     Running     1          4d4h
messaging                 1/1     Running     1          5d3h
nginx                     1/1     Running     1          4d16h
nginx-cm                  1/1     Running     1          4d16h
nginx-opt                 1/1     Running     1          5d2h
nginx-sec                 1/1     Running     1          4d16h
nginx-test                1/1     Running     1          5d2h
nginx1                    1/1     Running     1          5d4h
nginx2                    1/1     Running     1          5d4h
[root@master ~]# kubectl exec busybox -n test -- /bin/sh -c "cp /etc/passwd  /etc/foo/"
# 去pod所在的节点
[root@slave ~]# cd /etc/foo/
[root@slave foo]# ls
passwd
[root@slave foo]# cat passwd
root:x:0:0:root:/root:/bin/sh
daemon:x:1:1:daemon:/usr/sbin:/bin/false
bin:x:2:2:bin:/bin:/bin/false
sys:x:3:3:sys:/dev:/bin/false
sync:x:4:100:sync:/bin:/bin/sync
mail:x:8:8:mail:/var/spool/mail:/bin/false
www-data:x:33:33:www-data:/var/www:/bin/false
operator:x:37:37:Operator:/var:/bin/false
nobody:x:65534:65534:nobody:/home:/bin/false
```

20. Create a pod with image nginx called nginx-1 and expose its port 80.(创建一个叫nginx-1的 nginx pod，并暴露80端口。)
```bash
[root@master ~]# kubectl run nginx-1 --image=nginx -n test --port=80 --expose
service/nginx-1 created
pod/nginx-1 created
[root@master ~]# kubectl get all -n test
NAME          READY   STATUS    RESTARTS   AGE
pod/nginx-1   1/1     Running   0          8s
NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/nginx-1   ClusterIP   10.98.225.136   <none>        80/TCP    8s
```

21. Get service's ClusterIP, create a temp busybox-1 pod and 'hit' that IP with wget.(拿到那个svc的clusterip，创建一个临时的busybox-1的pod，并wget那个ip)
```bash
[root@master ~]# kubectl run busybox-1 --image=busybox -i --restart=Never --rm  -n test  -- /bin/sh -c "wget 10.98.225.136"
Connecting to 10.98.225.136 (10.98.225.136:80)
saving to 'index.html'
index.html           100% |********************************|   612  0:00:00 ETA
'index.html' saved
pod "busybox-1" deleted
```
22. Convert the ClusterIP to NodePort for the same service and find the NodePort. Hit the service(create temp busybox pod) using Node's IP and Port.(转换clusterip到nodeport并找到那个nodeport。)
- 修改svc的配置
```yaml
[root@master ~]# kubectl edit svc -n test nginx-1
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2021-07-13T09:38:06Z"
  name: nginx-1
  namespace: test
  resourceVersion: "694711"
  uid: cd999581-e2ce-45c3-bea4-d4ce95958c9b
spec:
  clusterIP: 10.98.225.136
  clusterIPs:
  - 10.98.225.136
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx-1
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
```
- 通过nodeIP和nodePort访问nginx
```bash
[root@master ~]# kubectl run busybox --image=busybox --restart=Never -i --rm -n test  -- /bin/sh -c "wget 192.168.10.81:30678"
Connecting to 192.168.10.81:30678 (192.168.10.81:30678)
saving to 'index.html'
index.html           100% |********************************|   612  0:00:00 ETA
'index.html' saved
pod "busybox" deleted
```
