# 用户

```shell
# 创建用户
create user "tmp"@"%" identified by '123';
# 授权
grant select,insert,update,delete on `test`.* to `tmp`@"%";
flush privileges;
# 取消权限
revoke select on `test`.* from `tmp`@"%";
flush privileges;
# 查看用户权限
show grants for tmp;
```
