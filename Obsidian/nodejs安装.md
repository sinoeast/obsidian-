# node.js 安装教程 (Windows zip 版)

来源：[node.js 安装教程 (Windows zip 版)_「已注销」的博客-CSDN博客_node.exe怎么安装](https://blog.csdn.net/qq_50975945/article/details/124133158)

node.js 安装教程 (Windows zip 版)
# 1 官网
地址：[Node.js (nodejs.org)](https://nodejs.org/zh-cn/)

界面：

![image-20220723000841424](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723000841424.png)

# 2 下载
地址：下载 | [Node.js (nodejs.org)](https://nodejs.org/zh-cn/download/)

自行选择安装版本，这里我选择 **Windows 二进制文件 (.zip)** 64位

![image-20220723001105774](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723001105774.png)

# 3 安装
将文件解压到要安装的位置，并创建 node_global 和 node_cache 两个文件夹。

![image-20220723001452043](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723001452043.png)

# 4 配置
① 环境变量

将 node.exe 所在的目录添加到 系统变量 => path 环境变量，这样我们在使用命令行时就可以在任意路径使用 node 命令了，同时该目录下有一个 npm.cmd 文件，打开文件其实就是将我们的 npm 命令映射到 node.exe npm-cli.js，由于存在该映射所以只要把 node.exe 所在的目录添加到 path 环境变量，就可以在任何目录下执行 npm install 了。

![image-20220723001558680](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723001558680.png)

在命令框中输入以下命令进行测试：

```shell
node -v
npm -v
```

![image-20220723002307717](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723002307717.png)到这里 node 就安装好了，但为了我们更方便使用 node，建议进行如下配置。

② 使用 npm 安装模块分为本地安装和全局安装。

本地安装：npm install express 会安装到当前项目

全局安装：npm install express -g 会安装到指定的目录(node_global)

> 注意：如果没有设置全局目录 node_global，那么全局安装的文件将会保存到C:\Users\“自己设置的计算机名字”\AppData\Roaming\npm
>
> 所以，安装好 node 后，要设置一下node_global和node_cache(node缓存文件夹)

打开 cmd，依次输入如下命令设置 node_global 和 node_cache

```sh
npm config set prefix "D:\node-v16.16.0-win-x64\node-global"
npm config set cache "D:\node-v16.16.0-win-x64\node-cache"
```

![image-20220723002603303](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723002603303.png)

> 注意：如果提示权限不够，可以使用管理员方式打开 cmd 窗口。 

接下来通过安装 express 来进行测试：

`npm install express -g`

![image-20220723002855970](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723002855970.png)

> 注意：如果安装失败，原因是 npm 安装模块时都是去国外的镜像下载的，有时候可能因为网络原因导致 npm 安装模块很慢或者安装失败，这时可以用淘宝 npm 镜像代替 npm 进行安装模块。

打开 cmd，输入以下命令。

- 查看当前镜像源

  `npm config get registry`

![image-20220723003017529](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723003017529.png)

- 
  设置淘宝镜像源


		`npm config set registry https://registry.npm.taobao.org`

![image-20220723003047016](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723003047016.png)

- 
  恢复为官方的镜像源


		`npm config set registry https://registry.npmjs.org`
设置好淘宝镜像源后，我们再重新执行 npm install express -g 命令。

![image-20220723003120950](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723003120950.png)

出现上图所示，表示已安装成功。可以在 node 根目录下找到 express 如下图：

![image-20220723003355259](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723003355259.png)
