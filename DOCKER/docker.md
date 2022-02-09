# 修改容器的binds（rke的kubernetes组件)
```shell
以kubelet为例：
1. 停止docker或者停止kubelet容器
systemctl stop docker
# docker stop kubelet
2. 修改配置
[root@master rancher]# docker ps
CONTAINER ID        IMAGE                                COMMAND                  CREATED             STATUS                  PORTS               NAMES
13308ec16bb8        rancher/pause:3.1                    "/pause"                 1 second ago        Up Less than a second                       k8s_POD_calico-kube-controllers-69d87587cc-zttmh_kube-system_10e6c0b0-cb70-4938-9060-2d5932a60fa1_2193
b1b54f930ab6        rancher/pause:3.1                    "/pause"                 38 minutes ago      Up 38 minutes                               k8s_POD_calico-node-nw4n2_kube-system_bb88a1a9-6c9e-4067-a50c-fc8042084035_2
b0311f0821a8        rancher/hyperkube:v1.15.3-rancher1   "/opt/rke-tools/entr…"   25 hours ago        Up 38 minutes                               kube-proxy
1705d904a944        rancher/hyperkube:v1.15.3-rancher1   "/opt/rke-tools/entr…"   25 hours ago        Up 38 minutes                               kubelet
9ac7f64f90e3        rancher/rke-tools:v0.1.50            "/opt/rke-tools/rke-…"   25 hours ago        Up 38 minutes                               etcd-rolling-snapshots
f8535caa7bf7        rancher/hyperkube:v1.15.3-rancher1   "/opt/rke-tools/entr…"   26 hours ago        Up 38 minutes                               kube-scheduler
33a290bb6987        rancher/hyperkube:v1.15.3-rancher1   "/opt/rke-tools/entr…"   26 hours ago        Up 38 minutes                               kube-controller-manager
48eb56b88f1b        rancher/hyperkube:v1.15.3-rancher1   "/opt/rke-tools/entr…"   26 hours ago        Up 38 minutes                               kube-apiserver
a5e5a72466d2        quay.io/coreos/etcd:v3.3.10          "/usr/local/bin/etcd…"   26 hours ago        Up 38 minutes                               etcd

[root@master rancher]# cd /var/lib/docker/containers/1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4/
[root@master 1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4]# ls
1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4-json.log    checkpoints         hosts
1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4-json.log.1  config.v2.json      mounts
1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4-json.log.2  config.v2.json.bak  resolv.conf
1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4-json.log.3  hostconfig.json
1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4-json.log.4  hostname
[root@master 1705d904a94416dce0213a6d6d5ffa3e8fb08d8bc72aea37f73a9ebd13762dd4]#

修改config.v2.json内MountPoints中需要修改的挂载点的所有Source字段
"MountPoints": {
  "/var/lib/kubelet": {
    "Source": "/data01/kubelet",
    "Destination": "/var/lib/kubelet",
    "RW": true,
    "Name": "",
    "Driver": "",
    "Type": "bind",
    "Relabel": "shared,z",
    "Propagation": "shared",
    "Spec": {
      "Type": "bind",
      "Source": "/data01/kubelet",
      "Target": "/var/lib/kubelet",
      "BindOptions": {
        "Propagation": "shared"
      }
    },
    "SkipMountpointCreation": false
  },

修改hostconfig.json内的Binds字段
"Binds": [
  "/etc/kubernetes:/etc/kubernetes:z",
  "/etc/cni:/etc/cni:rw,z",
  "/opt/cni:/opt/cni:rw,z",
  "/var/lib/cni:/var/lib/cni:z",
  "/var/lib/calico:/var/lib/calico:z",
  "/etc/resolv.conf:/etc/resolv.conf",
  "/sys:/sys:rprivate",
  "/data01/docker:/var/lib/docker:rw,rslave,z",
  "/data01/kubelet:/var/lib/kubelet:shared,z",
  "/data01/rancher:/var/lib/rancher:shared,z",
  "/var/run:/var/run:rw,rprivate",
  "/run:/run:rprivate",
  "/etc/ceph:/etc/ceph",
  "/dev:/host/dev:rprivate",
  "/data01/log/containers:/var/log/containers:z",
  "/data01/log/pods:/var/log/pods:z",
  "/usr:/host/usr:ro",
  "/etc:/host/etc:ro"
],
启动docker或启动kubelet
systemctl start 启动docker
# docker start kubelet
```
