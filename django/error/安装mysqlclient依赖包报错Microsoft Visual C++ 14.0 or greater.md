# [安装mysqlclient依赖包报错Microsoft Visual C++ 14.0 or greater](https://www.cnblogs.com/wavezhou/p/cplus_need.html)

# 一、报错详情：

 error: Microsoft Visual C++ 14.0 or greater is required. Get it with "Microsoft C++ Build Tools": https://visualstudio.microsoft.com/visual-cpp-build-tools/

# 二、报错原因：

像我这里，是在安装mysqlclient依赖包的时候报错

![img](https://gitee.com/sinoeast/imgs/raw/master/img/2644932-20211121234110768-1980338107.png)

网上很多的解决办法都是让你去下载一个Microsoft  C++ Build - tools 的exe可执行工具包，反正我自己试了几个，没有一个成功解决问题的。

我这里是安装mysqlclient时候报的缺少 Microsoft Visual C++ 14.0 or greater错，所以，你可以在这个网站https://www.lfd.uci.edu/~gohlke/pythonlibs/里找这个mysqlclient对应需要的python工具包

# 三、[下载你需要的whl文件](![](https://gitee.com/sinoeast/imgs/raw/master/img/2644932-20211121234122987-91789563.png))

 ![img](https://gitee.com/sinoeast/imgs/raw/master/img/2644932-20211121234122987-91789563.png)

点击一下你缺少的工具包，然后跳到以下你可以点击下载你需要的pyd库的地方

 ![img](https://gitee.com/sinoeast/imgs/raw/master/img/2644932-20211121234135898-2140031191.png)
①cp后面跟的是python的版本号，请与你的python版本对应 
②win32 - 只有默认32位 
③带amd是指电脑处理器为amd, 不带可以理解为intel处理器的(所以intel的64位的操作系统请直接下载win32.whl这款。但是，也可能网站放错了位置，如果win32.whl的不行，再试下win_amd64.whl的)
# 四、安装依赖

我是把下载号的whl文件放在了D：\Temp\git_pull目录下，所以如下，直接安装它就OK

 ![img](https://gitee.com/sinoeast/imgs/raw/master/img/2644932-20211121234149706-1502192136.png)
# 五、再次执行 pip install ***，像我这里的***就是mysqlclient，问题解决
# 六 产生[[解决Windows下django.db.utils.OperationalError (2026, ‘SSL connection error unknown error number‘)问题]]问题