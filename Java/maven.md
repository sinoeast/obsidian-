# 四大特性
1. 依赖管理系统
maven为java世界引入了一个新的依赖管理系统jar包管理jar升级时修改配置文件即可，在java世界中，可以用groupid，artifactid，version组成的Coordination（做标）唯一标识一个依赖。
任何基于Maven构建的项目自身也必须定义这三项属性，生成的包可以是jar包，也可以是war包或者jar包，一个典型的依赖如下：
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230308143819.png)

2. 多模块构建
	项目复查时dao  service  controller  层分离将一个项目分解为多个模块已经是很通用的一种方式。
	 在maven中需要定义一个parent  POM作为一组module的聚合POM，在该POM中可以使用`<modules>`标签来定义一组子模块，parent POM不会有什么实际构建产生。而parent POM中的build配置以及依赖配置都会自动继承给子module。

3. 一致的项目构建
	主要解决了我们在idea中和eclipse中创建的项目，目录可能不一样，但是如果使用了maven后，我们创建的项目目录都是一样的。
	![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230308144846.png)

4. 一致的构建模型和插件机制
	如果我们需要tomcat服务器的时候，我们就把tomcat服务器的引进来。
	![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230308144448.png)

# 修改配置
本地仓库
```xml
<localRepository>C:/software/m2/repository</localRepository>
```

```xml
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

# 常用命令
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230308152636.png)
