# 解决Windows下django.db.utils.OperationalError: (2026, ‘SSL connection error: unknown error number‘)问题

![image-20220728110521891](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220728110521891.png)

原因：较高版本的mysql的ssl默认是开启的
解决方案：关闭ssl

第一步：先查看ssl开启情况
登录mysql之后，输入该命令：

```sql
mysql> SHOW VARIABLES LIKE '%ssl%';
```

![image-20220728110617148](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220728110617148.png)


第二步：修改配置文件my.ini

路径：C:\ProgramData\MySQL\MySQL Server 8.0

```ini
[mysqld]
skip_ssl  # 忽略ssl
```

第三步：重启mysql服务
ctrl+shift+esc按键调出任务管理器—>服务—>找到mysql服务—>右键重新运行

![image-20220728110736574](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220728110736574.png)

第四步：重新执行命令：
mysql> SHOW VARIABLES LIKE '%ssl%';

![image-20220728110758012](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220728110758012.png)
第五步：产生[[django.db.utils.OperationalError (2059, ＜NULL＞)]]问题