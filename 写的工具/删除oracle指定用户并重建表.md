# 删除oracle指定用户并重建用户和表空间

[百度网盘链接](https://pan.baidu.com/s/1D7wvfa5AMoyyNUnB4WiEfA )

<details>
<summary>提取码</summary>
hw8f
</details>

# 1)drop.bat文件启动对应python脚本

```cmd
@echo off

start cmd /k "cd C:\Users\hs\Desktop\pythonProject1&&python ci.py"

```

[[cmd.exe参数启动参数]]
cmd /k 执行String命令并且不关闭终端

# 2)ci.py文件修改数据准备内容
```python
con = cx.connect('scsb', 'scsb', '192.168.70.43:1521/orcl')
database = ['SBSB','SXSX','xx','xx']  
script_path = r'C:\Users\hs\Desktop\pythonProject1\123.sql'
```
1.  数据库链接准备，指定要链接的数据库

   con = cx.connect('用户名','密码','地址')

2. 指定要删除的用户名

   database = ['用户名']  

3. 重建表使用的脚本地址

   script_path = r'XXX\123.sql'

#  3）123.sql 建表脚本准备

原来的脚本脚本提取保留其中的sql语句（红框部分）：

![image-20220726131112808](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220726131112808.png)

- windows环境注意地址中`/`改为`\`
- 每一句建表语句用`/`分隔

![image-20220726131337357](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220726131337357.png)
