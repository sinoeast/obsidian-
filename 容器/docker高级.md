# [[Mysql 主从复制安装](https://www.yuque.com/tmfl/cloud/nr5kfx)] 
  
安装主服务器容器实例（端口号3307）：  
  
1 启动容器实例  

```
docker run -p 3307:3306 \
           --name mysql-master \
           --privileged=true \
           -v /app/mysql-master/log:/var/log/mysql \
           -v /app/mysql-master/data:/var/lib/mysql \
           -v /app/mysql-master/conf:/etc/mysql \
           -e MYSQL_ROOT_PASSWORD=root \
           -d mysql:5.7
```

2 进入/app/mysql-master/conf，新建my.cnf配置文件：  

```
[mysqld]
## 设置server_id, 同一个局域网中需要唯一
server_id=101
## 指定不需要同步的数据库名称
binlog-ignore-db=mysql
## 开启二进制日志功能
log-bin=mall-mysql-bin
## 设置二进制日志使用内存大小（事务）
binlog_cache_size=1M
## 设置使用的二进制日志格式（mixed,statement,row）
binlog_format=mixed
## 二进制日志过期清理时间。默认值为0，表示不自动清理
expire_logs_days=7
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断
## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
```

3 重启容器实例  

```
docker restart mysql-master
```

4 进入容器实例内  

```
docker exec -it mysql-master /bin/bash
```

5 登录mysql，创建数据同步用户  

```
-- 首先使用 mysql -uroot -p 登录mysql
-- 创建数据同步用户
create user 'slave'@'%' identified by '123456';
-- 授权
grant replication slave, replication client on *.* to 'slave'@'%';
flush privileges;
```

安装从服务器容器实例（端口号3308）：  
  
1 启动容器服务：  

```
docker run -p 3308:3306 \
           --name mysql-slave \
           --privileged=true \
           -v /app/mysql-slave/log:/var/log/mysql \
           -v /app/mysql-slave/data:/var/lib/mysql \
           -v /app/mysql-slave/conf:/etc/mysql \
           -e MYSQL_ROOT_PASSWORD=root \
           -d mysql:5.7
```

2 进入/app/mysql-slave/conf目录，创建my.cnf配置文件：  

```
[mysqld]
## 设置server_id, 同一个局域网内需要唯一
server_id=102
## 指定不需要同步的数据库名称
binlog-ignore-db=mysql
## 开启二进制日志功能，以备slave作为其它数据库实例的Master时使用
log-bin=mall-mysql-slave1-bin
## 设置二进制日志使用内存大小（事务）
binlog_cache_size=1M
## 设置使用的二进制日志格式（mixed,statement,row）
binlog_format=mixed
## 二进制日志过期清理时间。默认值为0，表示不自动清理
expire_logs_days=7
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断
## 如：1062错误是指一些主键重复，1032是因为主从数据库数据不一致
slave_skip_errors=1062
## relay_log配置中继日志
relay_log=mall-mysql-relay-bin
## log_slave_updates表示slave将复制事件写进自己的二进制日志
log_slave_updates=1
## slave设置只读（具有super权限的用户除外）
read_only=1
```

3 修改完配置需要重启slave容器实例  

```
docker restart mysql-slave
```

在主数据库中查看主从同步状态：  
  
1 进入主数据库容器：  

```
docker exec -it mysql-master /bin/bash
```

2 进入Mysql  

```
mysql -uroot -p
```

3 查看主从同步状态  

```
show master status;
```

主要查看返回结果的文件名File、当前位置Position  
  
进入从数据库容器，配置主从复制：  
  
1 进入从数据库容器：  

```
docker exec -it mysql-slave /bin/bash
```

2 进入数据库  

```
mysql -uroot -p
```

3 配置从数据库所属的主数据库：  

```
-- 格式：
-- change master to master_host='宿主机ip',master_user='主数据库配置的主从复制用户名',master_password='主数据库配置的主从复制用户密码',master_port=宿主机主数据库端口,master_log_file='主数据库主从同步状态的文件名File',master_log_pos=主数据库主从同步状态的Position,master_connect_retry=连接失败重试时间间隔（秒）;

change master to master_host='192.168.xxx.xxx',master_user='slave',master_password='123456',master_port=3307,master_log_file='mall-mysql-bin.000001',master_log_pos=769,master_connect_retry=30;
```

4 查看主从同步状态：  

```
# \G 可以将横向的结果集表格转换成纵向展示。
# slave status的字段比较多，纵向展示比友好
show slave status \G;
```

除了展示刚刚配置的主数据库信息外，主要关注 `Slave_IO_Running`、`Slave_SQL_Running`。目前两个值应该都为 `No`，表示还没有开始。

5 开启主从同步：  

```
start slave;
```

6 再次查看主从同步状态，Slave_IO_Running、Slave_SQL_Running都变为Yes。  
  
主从复制测试：  
  
1 在主数据库上新建库、使用库、新建表、插入数据  

```
create database db01;
use db01;
create table t1 (id int, name varchar(20));
insert into t1 values (1, 'abc');
```

2 在从数据库上使用库、查看记录  

```
show databases;
use db01;
select * from t1;
```

# [安装Redis](https://www.yuque.com/tmfl/cloud/ixlgsl#4df46ff7)

# Dockerfile
Dockerfile是用来构建Docker镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本。

构建步骤：
1.  编写Dockerfile文件
2.  `docker build`命令构建镜像
3.  `docker run`依据镜像运行容器实例

## 构建过程
Dockerfile编写：
-   每条保留字指令都必须为大写字母，且后面要跟随至少一个参数
-   指令按照从上到下顺序执行
-   `#`表示注释
-   每条指令都会创建一个新的镜像层并对镜像进行提交

Docker引擎执行Docker的大致流程：
1.  docker从基础镜像运行一个容器
2.  执行一条指令并对容器做出修改
3.  执行类似`docker commit`的操作提交一个新的镜像层
4.  docker再基于刚提交的镜像运行一个新容
5.  执行Dockerfile中的下一条指令，直到所有指令都执行完成
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221219151603.png)

## Dockerfile保留字

### FROM
基础镜像，当前新镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板。Dockerfile第一条必须是`FROM`
```
# FROM 镜像名
FROM hub.c.163.com/library/tomcat
```

### MAINTAINER
镜像维护者的姓名和邮箱地址
```
# 非必须
MAINTAINER ZhangSan zs@163.com
```

### RUN
容器构建时需要运行的命令。
有两种格式：
-   shell格式
```
# 等同于在终端操作的shell命令
# 格式：RUN <命令行命令>
RUN yum -y install vim
```
-   exec格式
```
# 格式：RUN ["可执行文件" , "参数1", "参数2"]
RUN ["./test.php", "dev", "offline"]  # 等价于 RUN ./test.php dev offline
```
`RUN`是在`docker build`时运行

### EXPOSE
当前容器对外暴露出的端口。
```
# EXPOSE 要暴露的端口
# EXPOSE <port>[/<protocol] ....
EXPOSE 3306 33060
```

### WORKDIR
指定在创建容器后， 终端默认登录进来的工作目录。
```
ENV CATALINA_HOME /usr/local/tomcat
WORKDIR $CATALINA_HOME
```

### USER
指定该镜像以什么样的用户去执行，如果不指定，默认是`root`。（一般不修改该配置）
```
# USER <user>[:<group>]
USER patrick
```

### ENV
用来在构建镜像过程中设置环境变量。
这个环境变量可以在后续的任何`RUN`指令或其他指令中使用
```
# 格式 ENV 环境变量名 环境变量值
# 或者 ENV 环境变量名=值
ENV MY_PATH /usr/mytest

# 使用环境变量
WORKDIR $MY_PATH
```

### VOLUME
容器数据卷，用于数据保存和持久化工作。类似于 `docker run` 的`-v`参数。
```
# VOLUME 挂载点
# 挂载点可以是一个路径，也可以是数组（数组中的每一项必须用双引号）
VOLUME /var/lib/mysql
```

### ADD
将宿主机目录下（或远程文件）的文件拷贝进镜像，且会自动处理URL和解压tar压缩包。

### COPY
类似`ADD`，拷贝文件和目录到镜像中。
将从构建上下文目录中`<源路径>`的文件目录复制到新的一层镜像内的`<目标路径>`位置。
```
COPY src dest
COPY ["src", "dest"]
# <src源路径>：源文件或者源目录
# <dest目标路径>：容器内的指定路径，该路径不用事先建好。如果不存在会自动创建
```

### CMD
指定容器启动后要干的事情。
有两种格式：
-   shell格式
```
# CMD <命令>
CMD echo "hello world"
```
-   exec格式
```
# CMD ["可执行文件", "参数1", "参数2" ...]
CMD ["catalina.sh", "run"]
```
-   参数列表格式
```
# CMD ["参数1", "参数2" ....]，与ENTRYPOINT指令配合使用
```
Dockerfile中如果出现多个`CMD`指令，只有最后一个生效。`CMD`会被`docker run`之后的参数替换。
例如，对于tomcat镜像，执行以下命令会有不同的效果：
```
# 因为tomcat的Dockerfile中指定了 CMD ["catalina.sh", "run"]
# 所以直接docker run 时，容器启动后会自动执行 catalina.sh run
docker run -it -p 8080:8080 tomcat

# 指定容器启动后执行 /bin/bash
# 此时指定的/bin/bash会覆盖掉Dockerfile中指定的 CMD ["catalina.sh", "run"]
docker run -it -p 8080:8080 tomcat /bin/bash
```
`CMD`是在`docker run`时运行，而 `RUN`是在`docker build`时运行。

### ENTRYPOINT
用来指定一个容器启动时要运行的命令。
类似于`CMD`命令，但是`ENTRYPOINT`不会被`docker run`后面的命令覆盖，这些命令参数会被当做参数送给`ENTRYPOINT`指令指定的程序。
`ENTRYPOINT`可以和`CMD`一起用，一般是可变参数才会使用`CMD`，这里的`CMD`等于是在给`ENTRYPOINT`传参。
当指定了`ENTRYPOINT`后，`CMD`的含义就发生了变化，不再是直接运行期命令，而是将`CMD`的内容作为参数传递给`ENTRYPOINT`指令，它们两个组合会变成 `<ENTRYPOINT> "<CMD>"`。
例如：

```
FROM nginx

ENTRYPOINT ["nginx", "-c"]  # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参
```
对于此Dockerfile，构建成镜像 `nginx:test`后，如果执行；
-   `docker run nginx test`，则容器启动后，会执行 `nginx -c /etc/nginx/nginx.conf`
-   `docker run nginx:test /app/nginx/new.conf`，则容器启动后，会执行 `nginx -c /app/nginx/new.conf`

## 构建镜像  
  
创建名称为Dockerfile的文件，示例：  

```
FROM ubuntu
MAINTAINER lee<lee@xxx.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN apt-get update
RUN apt-get install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "install ifconfig cmd into ubuntu success ....."
CMD /bin/bash
```
编写完成之后，将其构建成docker镜像。  
命令：  
```
# 注意：定义的TAG后面有个空格，空格后面有个点
# docker build -t 新镜像名字:TAG .
docker build -t ubuntu:1.0.1 .
```

# 虚悬镜像
虚悬镜像：仓库名、标签名都是 `<none>`的镜像，称为 dangling images（虚悬镜像）。
在构建或者删除镜像时可能由于一些错误导致出现虚悬镜像。
例如：
```
# 构建时候没有镜像名、tag
docker build .
```
列出docker中的虚悬镜像：
```
docker image ls -f dangling=true
```
虚悬镜像一般是因为一些错误而出现的，没有存在价值，可以删除：
```
# 删除所有的虚悬镜像
docker image prune
```

# [Docker发布微服务](https://www.yuque.com/tmfl/cloud/fbkthl)
写一个Dockerfile把jar打进去 构建启动镜像

# Docker网络
docker安装并启动服务后，会在宿主机中添加一个虚拟网卡。  

在Docker服务启动前，使用 ifconfig 或 ip addr 查看网卡信息：  
●ens33或eth0：本机网卡  
●lo：本机回环网络网卡  
●可能有virbr0（CentOS安装时如果选择的有相关虚拟化服务，就会多一个以网桥连接的私网地址的virbr0网卡，作用是为连接虚拟网卡提供NAT访问外网的功能。如果要移除该服务，可以使用 yum remove libvirt-libs.x86_64）  

使用 systemctl start docker启动Docker服务后，会多出一个 docker0 网卡。  
作用：  
●容器间的互联和通信以及端口映射  
●容器IP变动时候可以通过服务名直接网络通信而不受到影响  

Docker容器的网络隔离，是通过Linux内核特性 namespace和 cgroup 实现的。  
## docker网络命令  
查看Docker网络模式：

```
docker network ls
```
如果没有修改过docker network，则默认有3个网络模式：
-   `bridge`
-   `host`
-   `none`
添加Docker网络：
```
docker network add xxx
```
删除Docker网络：
```
docker network rm xxx
```
查看网络元数据：
```
docker network inspect xxx
```
删除所有无效的网络：
```
docker network prune
```
## Docker 网络模式
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221228112137.png)
查看某个容器的网络模式：
```
# 通过inspect获取容器信息，最后20行即为容器的网络模式信息
docker inspect 容器ID | tail -n 20
```
## docker0
Docker 服务默认会创建一个`docker0`网桥（其上有一个`docker0`内部接口），该桥接网络的名称为 `docker0`，它在内核层连通了其他的物理或虚拟网卡，这就将所有容器和本地主机都放到同一个物理网络。
Docker默认指定了`docker0`接口的IP地址和子网掩码，让主机和容器之间可以通过网桥互相通信。
查看`bridge`网络的详细信息，并通过`grep`获取名称：
```
docker network inspect bridge | grep name
```
可以看到其名称为`docker0`。
## bridge模式
Docker使用Linux桥接，在宿主机虚拟一个`Docker`容器网桥（`docker0`），Docker启动一个容器时会根据`Docker`网桥的网段分配给容器一个IP地址，称为`Container-IP`，同时Docker网桥是每个容器的默认网关。因为在同一个宿主机内的容器接入同一个网桥，这样容器之间就能够通过容器的`Container-IP`直接通信。
`docker run`的时候，没有指定`--network`的话，默认使用的网桥模式就是`bridge`，使用的就是`docker0`。在宿主机`ifconfig`就苦役看到`docker0`和自己`create`的`network`。
网桥`docker0`创建一对对等虚拟设备接口，一个叫`veth`，另一个叫`eth0`，成对匹配：
整个宿主机的网桥模式都是`docker0`，类似一个交换机有一堆接口，每个接口叫 `veth`，在本地主机和容器内分别创建一个虚拟接口，并让他们彼此联通（这样一对接口叫做 `veth pair`）。
每个容器实例内部也有一块网卡，容器内的网卡接口叫做`eth0`。
`docker0`上面的每个`veth`匹配某个容器实例内部的`eth0`，两两配对，一一匹配。
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221228112350.png)
例如：
启动tomcat容器，进入tomcat容器后，执行 `ip addr`，可以看到其网卡信息：
```
1: lo ..................

容器内的网卡为 eth0
@符号后面就是宿主机上对应的veth网卡的编号28
27: eth0@if28 ...............................
```
在宿主机执行 `ip addr` 查看宿主机网卡信息：
```
每个veth都有个编号：vethXXXXXX
@符号后面对应就是容器内的eth0网卡编号27

28: vethXXXXXX@if27  ................
```
## host模式
直接使用宿主机的 IP 地址与外界进行通信，不再需要额外进行 NAT 转换。
容器将不会获得一个独立的 Network Namespace，而是和宿主机共用一个 Network space。
容器将不会虚拟出自己的网卡，而是直接使用宿主机的 IP 和端口。
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221228112455.png)
如果在 `docker run` 命令中同时使用了 `--network host` 和 `-p`端口映射，例如：
```
docker run -p 8082:8080 --network host tomcat
```
那么会出现一个警告：
```
WARNING: Published ports are discarded when using host network mode
```
因为此时已经使用了`host`模式，本身就是直接使用的宿主机的IP和端口，此时的`-p`端口映射就没有了意义，也不会生效，端口号还是会以主机端口号为主。
正确做法是：不再进行`-p`端口映射，或者改用`bridge`模式
## none模式
禁用网络功能。
在`none`模式下，并不为docker容器进行任何网络配置。进入容器内，使用 `ip addr`查看网卡信息，只能看到 `lo`（本地回环网络`127.0.0.1`网卡）。
## container模式
新建的容器和已经存在的一个容器共享网络IP配置，而不是和宿主机共享。
新创建的容器不会创建自己的网卡、IP，而是和一个指定的容器共享IP、端口范围。两个容器除了网络共享，其他的如文件系统、进程列表依然是隔离的。
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221228112603.png)
示例：
```
docker run -it --name alpine1 alpine /bin/sh

# 指定和 alpine1 容器共享网络
docker run -it --netrowk container:alpine1 --name alpine2 alpine /bin/sh
```
此时使用 `ip addr`查看两台容器的网络，会发现两台容器的`eth0`网卡内的IP等信息完全相同。
如果关掉了`alpine1`容器，因为`alpine2`的网络使用的`alpine1`共享网络，所以关掉`alpin1`后，`alpine2`的`eth0`网卡也随之消失了。
## 自定义网络（增加了host？）
容器间的互联和通信以及端口映射。
容器 IP 变动时候可以通过服务名直接网络通信而不受影响。（类似Eureka，通过服务名直接互相通信，而不是写死IP地址）。
docker中还有一个 `--link` 进行容器网络互联，但是已经被标记为过时的，可能会在将来的版本中移除这个功能。推荐使用自定义网络替换link。
自定义桥接网络（自定义网络默认使用的是桥接网络 `bridge`）：
1.  新建自定义网络
```
docker network create tomcat_network
```
2.  查看网络列表
```
docker network ls
```
3.  创建容器时，指定加入我们自定义的网络中
```
docker run -d -p 8081:8080 --network tomcat_network --name tomcat1 tomcat:8.5-jdk8-corretto

docker run -d -p 8082:8080 --network tomcat_network --name tomcat2 tomcat:8.5-jdk8-corretto
```
4.  此时进入`tomcat1`中，使用`ping`命令测试连接`tomcat2`容器名，发现可以正常连通
```
# 安装ifconfig命令
yum install -y net-tools
# 安装ip addr命令
yum install -y iproute
# 安装ping命令
yum install -y iputils

# 直接ping容器名，不需要ping IP地址
ping tomcat2
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221228143614.png)

## link连接
示例：
```
# 启动一台mysql容器
# --name 为容器指定一个别名
docker run --name mysql-matomo -p 3308:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:8.0.28

# 启动另一个容器，通过--link连接到mysql容器
# --link 容器名称:本容器连接对方时的别名
docker run -d -p 8888:80 --link mysql-matomo:db --name matomo matomo:4.9.0

# 此时，在matomo容器中，便可以通过 db 这个hostname连接到mysql-matomo容器，而无须再通过ip
# 连接地址：db:3306
```

# Docker-compose容器编排
Docker-Compose 是 Docker 官方的开源项目，负责实现对Docker容器集群的快速编排。  
  
Docker-Compose可以管理多个Docker容器组成一个应用。需要定义一个yaml格式的配置文件 docker-compose.yml，配置好多个容器之间的调用关系，然后只需要一个命令就能同时启动/关闭这些容器。  
  
Docker建议我们每个容器中只运行一个服务，因为Docker容器本身占用资源极少，所以最好是将每个服务单独的分割开来。但是如果我们需要同时部署多个服务，每个服务单独构建镜像构建容器就会比较麻烦。所以 Docker 官方推出了 docker-compose 多服务部署的工具。  
  
Compose允许用户通过一个单独的 docker-compose.yml 模板文件来定义一组相关联的应用容器为一个项目（project）。可以很容易的用一个配置文件定义一个多容器的应用，然后使用一条指令安装这个应用的所有依赖，完成构建。  
  
核心概念：  
  
●服务（service）：一个个应用容器实例  
●工程（project）：由一组关联的应用容器组成的一个完整业务单元，在docker-compose.yml中定义  
  
Compose使用的三个步骤：  
  
1编写 Dockerfile 定义各个应用容器，并构建出对应的镜像文件  
2编写 docker-compose.yml，定义一个完整的业务单元，安排好整体应用中的各个容器服务  
3执行 docker-compose up 命令，其创建并运行整个应用程序，完成一键部署上线  
  

## 安装Docker-Compose  
  
Docker-Compose的版本需要和Docker引擎版本对应，可以参照官网上的[对应关系](https://docs.docker.com/compose/compose-file/compose-file-v3/)。  
  
安装Compose：  
  

```shell
# 例如从github下载 2.5.0版本的docker-compose
# 下载下来的文件放到 /usr/local/bin目录下，命名为 docker-compose
curl -L https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

# 连不上的情况
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 添加权限
chmod +x /usr/local/bin/docker-compose

# 验证
docker-compose version
```

  
卸载Compose：直接删除 usr/local/bin/docker-compose文件即可  
  

## 常用命令  
  
执行命令时，需要在对应的docker-compose.yml文件所在目录下执行。  
  
查看帮助：  
  

```
docker-compose -h
```

  
创建并启动docker-compose服务：（类似 docker run）  
  

```
docker-compose up

# 后台运行
docker-compose up -d
```

  
停止并删除容器、网络、卷、镜像：（类似 docker stop +  docker rm）  
  

```
docker-compose down
```

  
进入容器实例内部：  
  

```
docker-compose exec <yml里面的服务id> /bin/bash
```

  
展示当前docker-compose编排过的运行的所有容器：  
  

```
docker-compose ps
```

  
展示当前docker-compose编排过的容器进程：  
  

```
docker-compose top
```

  
查看容器输出日志：  
  

```
docker-compose log <yml里面的服务id>
```

  
检查配置：  
  

```
docker-compose config

# 有问题才输出
docker-compose config -q
```

  
重启服务：  
  

```
docker-compose restart
```

  
启动服务：（类似 docker start）  
  

```
docker-compose start
```

  
停止服务：  
  

```
docker-compose stop
```

  

## compose编排实例  
  
示例：  
  


```yaml
# docker-compose文件版本号
version: "3"
services:
# 配置各个容器服务

  lt-mysql:
      # 容器名(以后的控制都通过这个)
    container_name: lt-mysql
      # 重启策略
    restart: always
    image: mysql:5.7
    ports:
      - "3306:3306"
    volumes:
       # 挂挂载配置文件
       #  - ./mysql/db/:/docker-entrypoint-initdb.d
      # 挂挂载配置文件
      - ./mysql/conf:/etc/mysql
      # 挂载日志
      - ./mysql/logs:/var/log
      # 挂载数据
      - ./mysql/data:/var/lib/mysql
    command: [
          'mysqld',
          '--innodb-buffer-pool-size=80M',
          '--character-set-server=utf8mb4',
          '--collation-server=utf8mb4_unicode_ci',
          '--default-time-zone=+8:00',
          '--lower-case-table-names=1',
          '--default-authentication-plugin=mysql_native_password' # 解决外部无法访问
        ]
    environment:
      # root 密码
      MYSQL_ROOT_PASSWORD: 123456
  microService:
    image: springboot_docker:1.0
    container_name: ms01  # 容器名称，如果不指定，会生成一个服务名加上前缀的容器名
    ports:
      - "6001:6001"
    volumes:
      - /app/microService:/data
    networks:
      - springboot_network
    depends_on:  # 配置该容器服务所依赖的容器服务
      - redis
      - mysql

  redis:
    image: redis:6.0.8
    ports:
      - "6379:6379"
    volumes:
      - /app/redis/redis.conf:/etc/redis/redis.conf
      - /app/redis/data:data
    networks:
      - springboot_network
    command: redis-server /etc/redis/redis.conf

networks:
  # 创建 springboot_network 网桥网络
  springboot_network:
```
编写完成`docker-compose.yml`后，进行语法检查：
```
# 进行语法检查
docker-compose config -q
```
如果语法检查没有任何问题，进行创建、启动：
```
docker-compose up -d
```
