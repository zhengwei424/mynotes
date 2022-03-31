#### etcd backup and restore
https://rancher.com/docs/rke/latest/en/etcd-snapshots/one-time-snapshots/

##### backup
```bash
# rke version >= 0.2.0+
#备份etcd
rke etcd snapshot-save --config cluster.yml --name <snapshot_name>
# 备份集群状态文件,默认在cluster.yml所在目录下
cp cp cluster.rkestate  cluster.rkestate.backup.20220329

```
##### recurring
```bash
# 清理集群
rke remove
# 恢复集群状态文件到rke up所在目录
cp cluster.rkestate.backup.20220329  cluster.rkestate
# rke version >= 0.2.0+
rke etcd snapshot-restore --config cluster.yml --name <snapshot_name>
```
#### add/delete node
```bash
(正常情况执行rke up不会修改集群认证信息-即使再不加--update-only的情况下)
添加删除节点，通过修改cluster.yaml添加删除worker节点信息，然后执行:
rke up --update-only

如果通过kubectl delete node 删除节点后，直接执行rke up --update-only 会报错（所加的nodename not found）
原因应该是cluster.rkestate文件内的node信息没有更新（直接delete node 不会更新该文件)
需要：
1. 在cluster.yaml中注释到被删除的节点，执行rke up --update-only
2. 如果要重新添加该节点，在cluster.yml中恢复该节点信息，执行rke up --update-only即可
```

#### modify component configuration
```bash
# rke version v0.3.0-rc12
修改cluster.yml中组件的配置（services），需要执行rke up才会生效（不会改变集群认证信息），而不是update-only
```
