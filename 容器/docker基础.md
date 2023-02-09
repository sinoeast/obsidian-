### 原理
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221214163601.png)

![](https://gitee.com/sinoeast/imgs/raw/master/img/20221214163518.png)

Docker Client 命令行
Docker Deamon 守护进程
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221214163952.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221214164147.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221214164523.png)
## 安装（centos）
编译环境
`yum -y install gcc`
`yum -y install gcc-c++`
安装需要的软件包
`yum install -y yum-utils`
设置镜像仓库
`yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo`
更新yum软件包索引
`yum makecache fast`
安装docker ce
`yum -y install docker-ce docker-ce-cli containerd.io`
启动docker
`systemctl start docker`
测试
`docker run hello-world`
## 镜像加速器

```linux
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF' { "registry-mirrors": ["https://rfxf12q1.mirror.aliyuncs.com"] } EOF
sudo systemctl daemon-reload 
sudo systemctl restart docker
```
## 帮助启动类命令
启动docker                         `systemctl start docker`
停止docker                         `systemctl stop docker`
重启docker                         `systemctl restart docker`
查看docker状态                  `systemctl status docker`
开机启动                             `systemctl enable docker`
查看docker概要信息           `docker info`
查看docker整体帮助文档    `docker --help`
查看docker命令帮助文档    `docker 具体命令 --help` ^37851f
## 镜像命令
### 1.列出本地镜像             
```linux
docker images
				-a 列出本地所有镜像（含历史映像层）
				-q 只显示镜像id
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215140324.png)
### 2.搜索远程镜像             
```linux
docker search  镜像名
					--limit 5 只展示5个
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215140447.png)
### 3.拉取远程镜像
```linux
docker pull 镜像名：版本
```
### 4.查看镜像、容器、数据卷所占的空间
```linux
docker system df
```
### 5.删除镜像
```linux
docker rmi              镜像id：版本
			-f 强制删除
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215141951.png)
### 6.虚悬镜像
仓库名、标签都是none的镜像
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215142348.png)

## 容器命令
### 1.启动容器

```linux
docker run -it --name=myubu ubuntu bash                   
                                                前台交互式启动(bash shell脚本命令终端)
```
 
```linux
docker run -d --name=myredis -p 主机(宿主)端口:容器端口 redis                  
                                                后台守护式启动(后台服务器)
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215143244.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215172253.png)

### 2.退出交互模式：
- 容器停止
	`exit`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215144228.png)
- 容器不停止
	`ctrl+p+q`
### 3.启动已经停止的容器

```linux
docker start 容器id/容器名
```

### 4.重启容器

```linux
docker restart 容器id/容器名
```

### 5.停止容器

```linux
docker stop 容器id/容器名
```

### 6.强制停止容器

```linux
docker kill 容器id/容器名
```

### 7.删除已经停止的容器

```linux
docker rm 容器id/容器名
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215163253.png)

### 8.展示容器列表
```linux
docker ps
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215145211.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215145456.png)

### 9.查看容器日志

```linux
docker logs 容器id/容器名
```

### 10.查看容器内运行的进程

```linux
docker top 容器id/容器名
```

### 11.查看容器内部细节

```linux
docker inspect 容器id/容器名
```

### 12.进入正在运行的容器并以命令行交互

```linux
docker exec -it 容器id/容器名 bash
```

```linux
docker attach 容器id/容器名
```

![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215172212.png)

![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215172622.png)

### 13.容器拷贝到主机

```linux
docker cp 容器id：容器内路径 目标主机路径
```

### 14.导出和导入容器

```linux
docker export 容器id > 文件名.tar
```

```linux
cat 文件名.tar |docker import - 镜像用户/镜像名：镜像版本号
```

![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215184740.png)

![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215185150.png)

### 15.命令总结
[Docker 命令大全 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/docker-command-manual.html)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215185512.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215185702.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215185625.png)

## docker镜像（深入）
### 什么是镜像
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192238.png)

### 分层的镜像
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192353.png)

### UnionFS（联合文件系统）
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192410.png)

### Docker镜像加载原理
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192441.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192623.png)

### 镜像分层的好处
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192708.png)

### 重点理解
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192819.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221215192843.png)

## 发布镜像(aliyun)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221216101406.png)

1.对ubuntu容器添加vim命令
```linux
apt-get update
apt-get -y install vim
```
2.从容器创建一个新的镜像
	[[#14.导出和导入容器]]（从归档文件中创建镜像）
```linux
docker commit -a="作者" -m="描述" 容器id 镜像用户/镜像名:版本
```
3.发布到阿里云
创建命名空间和镜像空间(https://cr.console.aliyun.com/cn-hangzhou/instance/repositories)
```linux
$ docker login --username=tb48277060 registry.cn-hangzhou.aliyuncs.com
$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/sinoeast/myubuntu:[镜像版本号]
$ docker push registry.cn-hangzhou.aliyuncs.com/sinoeast/myubuntu:[镜像版本号]
```
4.拉取镜像
```linux
docker pull registry.cn-hangzhou.aliyuncs.com/sinoeast/myubuntu:[镜像版本号]
```
## 发布镜像（私人库）
1.下载镜像

```
docker pull registry
```
2.运行私有库registry，相当于本地有个私有的docker hub

```
docker run -d -p 5000:5000 -v /test/:/tmp/registry --privileged=true registry
```
3.curl模拟get请求，查询库中存在的镜像

```
curl -XGET http://192.168.20.128:5000/v2/_catalog
```
4.将要推送的镜像修改成符合私服规范的Tag

```
docker tag 镜像:tag         Host:5000/镜像仓库名:tag
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221216112814.png)


5.修改配置文件使之支持http

[root@192 test]# vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://rfxf12q1.mirror.aliyuncs.com"]*,
  "insecure-registries":["192.168.20.128:5000"]*
}
重启docker 
`systemctl restart docker`[[#^37851f]]  
6.推送push

```
docker push Host:5000/镜像仓库名:tag
```

7.curl再次验证

```
curl -XGET http://192.168.20.128:5000/v2/_catalog
```
8.pull到本地

```
docker pull Host:5000/镜像仓库名:tag
```
## 容器数据卷
### 是什么
卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性：
卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂载的数据卷
容器和容器卷的数据互相同步（容器停止后恢复也会同步）
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221216143945.png)

### 怎么使用容器数据卷
docker run -it *--privileged=true -v /宿主机绝对路径目录:/容器内目录* 镜像名
查看绑定信息
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221216143719.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221216143821.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221216143752.png)

```
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
																		只读（容器内部）
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名
																		读写
```

### 容器卷的继承
docker run -it --privileged=true *--volumes-from 容器id*  镜像名
