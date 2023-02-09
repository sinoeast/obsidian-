# django.db.utils.OperationalError: (2059, ＜NULL＞)

问题所在：主要就是mysql8.0的问题。
目前最新的mysql8.0对用户密码的加密方式为caching_sha2_password, django暂时还不支持这种新增的加密方式。只需要将用户加密方式改为老的加密方式即可。
解决方案：

1.执行命令查看加密方式

```sql
select user,plugin from user where user=‘root’;
```

2.执行命令修改加密方式

```sql
alter user 'root'@'localhost' identified with mysql_native_password by '密码'
```

