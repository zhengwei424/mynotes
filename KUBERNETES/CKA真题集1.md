#### 1、RBAC

------

##### Context
为部署管道创建一个新的ClusterRole并将其绑定到范围为特定namespaed的特定ServiceAccount

##### Task

创建一个名为deployment-clusterrole且允许创建以下资源类型的新ClusterRole:

Deployment

StatefulSet

DeamonSet

在现有的namespace app-team1中创建一个名为cicd-token的新的ServiceAccount

限于namespace app-team1，将新的ClusterRole deployment-clusterrole绑定到新的ServiceAccount cicd-token

```shell
source < (kubectl completion bash)
kubectl create clusterrole deployment-clusterrole --verb=create --resource=deployments,statefulsets,deamonsets
kubectl -n app-team1 create serviceaccount cicd-token
#题目中没有指定namespace，所以创建rolebinding
kubectl -n app-team1 create rolebinding cicd-token-binding --clusterrole=deployment-clusterrole --serviceaccount=app-team1/cicd-token

kubectl get rolebinding -n app-team1  查看是否正常运行
```



#### 2、驱逐节点

------

##### Task

将名为ek8s-node-1的节点设置为不可用，并重启调度在其上允许的所有pod

```
drain:首先，驱逐node上的pod，其他节点重新创建,接着，将节点调为SchedulingDisabled
cordon: 只是将节点设置为SchedulingDisabled

kubectl cordon ek8s-node-1
kubectl drain ek8s-node-1 --ignore-daemonsets --delete-local-data --force

kubectl get nodes    查看是否成功
```



#### 3、集群升级

------

##### Task

升级 master 节点为1.20.1
升级前确保drain master 节点
不要升级worker node 、容器 manager、 etcd、 CNI插件、DNS 等内容

```shell
# 第一步需要ssh到master节点上去
kubectl get nodes
ssh mk8s-master-0
kubectl cordon mk8s-master-0
kubectl drain mk8s-master-0 --ignore-daemonsets
apt-mark unhold kubeadm kubectl kubelet
apt-get update && apt-get install -y kubeadm=1.20.1-00 kubelet=1.20.1-00 kubectl=1.20.1-00
apt-mark hold kubeadm kubectl kubelet
kubeadm upgrade plan
kubeadm upgrade apply v1.20.1 --etcd-upgrade=false
kubectl uncordon mk8s-master-0
kubectl get nodes   查看主节点版本是否生效
```



#### 4、ETCD备份恢复

------

##### Task

首先，为运行在https://127.0.0.1:2379上的现有的ETCD实例创建快照并将快照保存到/var/lib/backup/etcd-snapshot.db

然后还原位于/var/lib/backup/etcd-snapshot-previons.db

提供了以下的TLS证书与密钥，以通过etcdctl连接到服务器

CA证书：/opt/KUIN00601/ca.crt

客户端证书：/opt/KUIN00601/etcd-client.crt

客户端密钥：/opt/KUIN00601/etcd-client.key

```shell
ETCDCTL_API=3 etcdctl --endpoints https://172.0.0.1:2379 --cacert=/opt/KUIN00601/ca.crt --cert=/opt/KUIN00601/etcd-client.crt --key=/opt/KUIN00601/etcd-client.key snapshot save /var/lib/backup/etcd-snapshot.db
还原：
ETCDCTL_API=3 etcdctl --endpoints https://172.0.0.1:2379 --cacert=/opt/KUIN00601/ca.crt --cert=/opt/KUIN00601/etcd-client.crt --key=/opt/KUIN00601/etcd-client.key snapshot restore /var/lib/backup/etcd-snapshot-previous.db


kubectl get nodes 查看集群状态是否正常
```



#### 5、网络策略

------

##### Task 1

在已有的namespace foobar中创建一个名为allow-port-from-namespace的新的NetWorkPolicy，以允许namesapce corp-bar访问其pods的端口9200

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: foobar
spec:
  podSelector:
    matchLabels: {}  #所有pod
  policyTypes:
  - Ingress
  ingress:
  - from: #指来源
    - namespaceSelector:
        matchLabels:
          project: corp-bar
      PodSelector:
        matchLabels: {}
    ports:  #指本namespace下
    - protocol: TCP
      port: 9200

       为命名空间创建标签 kubectl label  ns corp-bar  project=corp-bar
       查看命名空间标签  kubectl get ns --show-labels
```

##### Task 2

允许本namespace下的pod访问本namespace下pod的9200

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace:  foobar
spec:
  PodSelector:
    matchLabels: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    PodSelector:
      matchLabels: {}
    ports:
    - protocol: TCP
      port: 9200
```



#### 6、SVC

------

##### Task

重建配置现有的deployment front-end，并添加名为http的端口规范，以暴漏现有容器nginx的端口80/tcp。

创建一个名为front-end-svc的新服务，以暴漏容器端口http

配置新服务以通过调度他们的节点上的NodePort暴漏各个Pod

```
kubectl edit deployment front-end
ports:
- name: http
  protocol: tcp
  containerPort: 80
```
```
kubectl expose  deployment front-end  --type=NodePort  --port=80  --target-port=80 --name=front-end-svc

查看服务是否正常
```


#### 7、创建一个ingress

------

##### Task

创建一个名为nginx的Ingress并遵守以下规则
Name：pong
Namespace：ing-internal
Exposing service hello on path /hello 使用端口5678
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: pong
  namespace: ing-internal
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /hello
        pathType: Prefix
        backend:
          service:
            name: hello
            port:
              number: 5678
```
```
验证
官网负载均衡->ingress-> 第一个yaml  添加命名空间
kubectl get ingress
curl -kl <INTERNAL_IP>/hello
```

#### 8、扩容

------

##### Task

将deployment web-server扩容到6个pods

```
kubectl scale deployment web-server --replicas=6
```

```
验证
kubectl get deployment  看是否已伸缩
```



#### 9、通过标签选择器调度pod

------

##### Task

按以下规则调度pod
name: nginx-kusc00401
image: nginx
Node selector: disk=ssd

```
apiVersion: v1
kind: Pod
matedata:
  name: nginx-kusc00401
spec:
  containers:
  - image: nginx
    name: nginx=kusc00401
  nodeSelector
    disk: ssd
```


```
验证
做题之前检查是否有此标签 没有的话就创建
完了 检查调度结果  kubectl get pod -o wide
```



#### 10、节点数量

------

##### Task

检查并查看有多少个节点准备就绪（不包括获得Noschedule的节点）并将其写入/opt/KUSC00402/kusc00402.txt

```
kubectl get nodes   正常节点数
kubectl describe node | grep -i taint|grep NoSchedule #不包含的节点
echo number > /opt/KUSC00402/kusc00402.txt
```



#### 11、创建多容器pod

------

##### Task

创建名称为 kucc1 的 pod
pod 中运行 nginx 和 redis 两个示例

```
apiVersion: v1
kind: Pod
matedata:
  name: kuccl
spec:
  containers:
  - image: nginx
    name: nginx
  - image: redis
    name: redis
```

```
验证
kubectl get pods  查看pod是否running状态
```


#### 12、pv

------

##### Task

创建一个名为app-config的pv，1G大小，权限为ReadOnlyMany使用hostPath类型挂载本地位置为/srv/app-config

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config
spec:
  capacity:
    storage: 1Gi
  accessModes:
  - ReadOnlyMany
  hostPath:
    path: "/srv/app-config"
```

```
验证
kubectl get pv  查看pv是否available状态
```


#### 13、pvc

------

##### Task

创建一个pvc满足以下要求

Name: pv-volume

Class: csi-hostpath-sc

Capcity: 10Mi

创建一个pod并且挂载pvc：

name: test

image: nginx

Mount path: /usr/share/nginx/html

编辑pod volume权限为ReadWriteOnce

最后，使用kubectl edit或者kubectl patch 将pvc大小改成70Mi


```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-volume
spec:
  storageClassName: csi-hostpath-sc
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
```

```
apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  volumes:
    - name: pv-volume
      persistentVolumeClaim:
        claimName: pv-volume
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: pv-volume
```

```
edit修改大小，--recard 需要写上
```

```
验证
kubectl get storageclass  查看存储类是否running状态
kubectl get pvc  查看pvc是否running状态
kubectl get pod 查看pod是否running状态
```


####  14、输出日志

------

##### Task

监控bar的日志

将有error字段内容输出到/opt/KUTR0010/bar

```
kubectl logs bar | grep error > /opt/KUTR0010/bar
```



#### 15、sidecar

------

##### Task

添加一个 sidecar 容器(使用busybox 镜像)到已有的 pod 11-factor-app 中
确保 sidecar 容器能够输出 /var/log/11-factor-app.log 的信息
使用 volume 挂载 /var/log 目录，确保 sidecar 能访问 11-factor-app.log 文件

新的sidecar容器使用以下命令：
/bin/sh -c tail -n+1 -f /var/log/big-corp-app.log

不要修改已经存在的容器
不要修改日志路径与文件

```
https://kubernetes.io/zh/docs/concepts/cluster-administration/logging/
```

```
apiVersion: v1
kind: Pod
metadata:
  name: 11-factor-app
spec:
  containers:
  - name: 11-factor-app
    image: busybox
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$i: $(date)" >> /var/log/11-factor-app.log;
        i=$((i+1));
        sleep 1;
      done
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  - name: sidecar
    image: busybox
    args: [/bin/sh, -c, 'tail -n+1 -f /var/log/11-factor-app.log']
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  volumes:
  - name: varlog
    emptyDir: {}
```

```
官方文档  集群管理->日志架构->倒数第三个yaml  修改    删除一行及一个容器

kubectl get pod big-corp-app -o yaml >sidecar.yaml
修改yaml文件
kubectl delete pod -f sidecar.yaml
kubectl create pod -f sidecar.yaml


kubectl get pod  big-corp-app 是否running状态   查看日志是否输入
kubectl  logs  big-corp-app  sidecar  查看sidecar日志是否写入文件
```



#### 16、top

------

#### Task

从pod标签name=cpu-loader中找到cpu负载最大的pod名称，并输出到/opt/KUTR00401/KUTR401.txt(该文件已经存在)

中文题中会有KUTR000401，多一个0

英文是对的 KUTR000401

```
kubectl top pod -A -l name=cpu-loader --sort-by="cpu"
#得到最大的cpu使用率的pod,文件已经存在，使用追加
echo pod-name >> /opt/KUTR00401/KUTR401.txt
```



#### 17、kubelet

------

##### Task

名为wk8s-node-0的工作节点为NotReady，找到并解决次问题

```
#原因为kubelet没有启动，start 就好,注意要ssh过去
kubectl get nodes
ssh wk8s-node-0
sudo -i
systemctl status kubelet
systemctl enable kubelet
systemctl restart kubelet
systemctl status kubelet
```
