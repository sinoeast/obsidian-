*测试计划*：测试用例
	*线程组*：用户行为(n个人正在登录、浏览、下单)
			- 线程数：并发数量
			- ramp-up时间：用户上线间隔时间（第一个用户不算时间，后面ramp-up时间/用户数=用户上线间隔时间）
		*前置控制器等*
		*取样器*：http请求（下单涉及到的所有接口）
		*逻辑控制器*
	*配置元件*：请求信息的管理
		- 请求默认值/HTTP信息头管理器：配置ip，路径等默认值/消息头管理
	*监听器元件*：查看请求执行结果查看结果树等
		-察看结果树
		-聚合报告
			 Connect Time ： jmeter 和 被测系统 建立 TCP 连接的时间，包括3路握手时间， 如果连接复用， 值为0 
			 lantency： 从发出请求前 到 接收完第一个响应的时间 
			 loadtime（以前叫Elapsed time）： 从发出请求前 到 接收完所有响应的时间 如果是长消息， 往往时长 >= lantency，因为有多个响应


# 脚本开发
## Fiddler录制
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230103135309.png)

# 线程组
## 1.setup线程组:第一个运行的，与位置无关（初始化数据）
## 2.线程组
每秒5个线程持续10分钟（查看结果树 勾选显示错误日志）
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207151029.png)

## 3.teardown线程组:最后一个运行的，与位置无关（清除数据）

# 配置元件
## 请求默认值
## HTTP信息头管理器
## cookie管理器
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104141814.png)

# 监听器元件
## 1、察看结果树：
1、分析查看具体某一个请求的详情：
	请求头、请求体
	响应头、响应体
2、在做性能场景的时候
	分析错误请求的原因
## 2.聚合报告
汇总统计
	请求数、响应时间（平均的 90% 90% 99% min max)单位是ms
	错误率 越低越好
	吞吐量 越高越好
	发送/接收 带宽
## 3.用表格查看结果
## 4.图形结果

# 其他常用元件
## 1.前置处理器
请求发出去之前执行的控制器(元件)
比如：
	加密md5
## 2.后置处理器
请求发出去之后执行的控制器（元件）
比如
	提取数据
## 3.定时器
1.思考时间-固定定时器
2.同步定时器-集合点
3.随机定时器
4.吞吐量定时器
	
# 逻辑控制器
### 仅一次控制器：线程组多次循环中只执行一次
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104160506.png)
### 简单控制器：等价于 { } 方便禁用启动操作合集
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104162620.png)
### 循环控制器：循环执行操作
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104162827.png)
### 事务控制器：需要操作都正确
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104163408.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104163517.png)
### 交替控制器：每个操作在线程循环中按顺序执行
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104164631.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104164726.png)
### 随机控制器：每个操作在线程循环中随机执行
![](https://gitee.com/sinoeast/imgs/raw/master/20230207101457.png)
# JMeter参数化
参数化流程
1、找出需要做参数化的数据
2、准备提供给参数化需要的数据源
3、把脚本里的常量变量（使用前面的数据源数据）
参数化方式：
## 1、CSV  (需要配置元件)  使用场景：账号，密码
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207103801.png)

注意csv数据文件的作用域
填文件名，文件编码，变量名称
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104163209.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104163033.png)
变量使用${ }
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230104163116.png)

## 2、函数式:随机数,time,counteri计数器
获取函数写法
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207103149.png)
使用函数
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207103232.png)


## 3、变量
设置自定义变量后 使用${}
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207103501.png)
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207103520.png)

## 4、编程式
引入外部的jar、java、class
使用beanshell编程
## token
测试一下取token *$.token*
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207102340.png)
后置处理器提取 *$.token*
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207102512.png)
调试取样器检查 *getToken*
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207102557.png)
使用取到的token值 *${getToken}*
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207102716.png)

## 正则提取变量
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207105454.png)


## 编码问题设置utf-8
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207103006.png)
## cookie
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207141802.png)

# 并发（同时抢票，登录。。）
## 添加同步定时器
两个参数
第一个：集合内的指定个线程一起并发
第二个：超时后集合内的线程不再等待一起发送
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207112037.png)
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207112337.png)
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207112802.png)

# 断言
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230207142729.png)


# 命令行模式 运行

真正实施性能测试应该在命令行模式下运行，命令格式如下：

```
d:\tools\apache-jmeter-5.4.1\bin\jmeter -n -t loadtest-case1.jmx  -l log.jtl
```

注意JMeter的路径替换为你的安装路径

# dashboard 产生图表

```
d:\tools\apache-jmeter-5.4.1\bin\jmeter -g log.jtl -o report1
```

就会产生report1目录，里面的index.html 打开就是报告

注意 `-o` 后面的目录 一定要不存在，或者内容为空，否则会报错。

其中 `APDEX (Application Performance Index)` 里面的 `T (Toleration threshold)` 和 `F (Frustration threshold)` 可以通过 JMeter 工具 bin 目录下面的 `user.properties` 配置文件里面 这两个选项来设置

```py
jmeter.reportgenerator.apdex_satisfied_threshold
jmeter.reportgenerator.apdex_tolerated_threshold
```



















