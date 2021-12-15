#### mysql重建从库
```bash
# 在从库找一个目录执行备份操作
mysqldump -uroot -p -h mysql-bigdata-prod-primary-0.mysql-bigdata-prod-primary.bigdata.svc.cluster.local --default-character-set=utf8 --master-data=2 --single-transaction --databases data_warehouse_out mysql > /tmp/bigdata.sql

# 获取备份sql中的master_log_file和master_log_pos并记录
grep 'CHANGE MASTER TO MASTER_LOG_FILE' /tmp/bigdata.sql

# 停止从库
mysql -uroot -p
mysql> stop slave

# drop 需要同步的数据库，并创建
mysql> show create database <db>  // 获取库的创建sql，并保存
mysql> drop <db>
mysql> 执行获取的<db>的创建sql,create database <db> xxxxxxx

# 导入备份
mysql> source  /tmp/bigdata.sql;

# 配置主从同步(k8s中使用全域名mysql-bigdata-prod-primary-0.mysql-bigdata-prod-primary.bigdata.svc.cluster.local会报错连接不上主库，缩短域名即可)
mysql> CHANGE MASTER TO MASTER_HOST='mysql-bigdata-prod-primary-0.mysql-bigdata-prod-primary',MASTER_USER='replicator',MASTER_PASSWORD='repl1cat0r',MASTER_LOG_FILE='mysql-bin.000115', MASTER_LOG_POS=105430550;

# 启动从库
mysql> start slave;

# 查看主从同步状态
mysql> show slave status\G;

```
