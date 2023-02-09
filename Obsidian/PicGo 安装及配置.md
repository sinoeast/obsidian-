# PicGo 安装及配置

来源：[PicGo 安装及配置_长得很抽象的博客-CSDN博客_picgo](https://blog.csdn.net/Y2ANGAO/article/details/124201503)

# 1 概述
一个用于快速上传图片并获取图片 URL 链接的工具。

# 2 官网
地址：[PicGo](https://picgo.github.io/PicGo-Doc/)

界面展示：

![image-20220723004210436](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004210436.png)

# 3 下载
官网下载地址：[Release 2.3.0 · Molunerfinn/PicGo · GitHub](https://github.com/Molunerfinn/PicGo/releases/tag/v2.3.0)

![image-20220723004445267](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004445267.png)

阿里云盘链接：[PicGo-2.3.0-x64](https://www.aliyundrive.com/s/QMFoia98JYT)
# 4 安装
直接打开 .exe 文件，一步步安装即可。

![image-20220723004459154](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004459154.png)

# 5 Gitee
我们需要使用 Git 来保存我们的图片，又因为国内访问 Github 速度比较慢，所以这里推荐使用国内的 Git 托管服务——[Gitee（gitee.com)](https://gitee.com/?utm_source=remote_blog_cnjc)。

step 1：注册账号：自行注册即可。

step 2：新建仓库

![image-20220723004559971](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004559971.png)

输入仓库名称，设置模板选择 Readme 文件，选择分支模型选择单分支模型（只创建 master 分支），然后点击创建。如下图：

![image-20220723004614773](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004614773.png)

创建完毕后，点击管理选项，如下图：

![image-20220723004625721](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004625721.png)

接着在基本信息中，将是否开源设置为开源，点击保存。

![image-20220723004636765](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004636765.png)

然后依次点击个人头像 => 设置 => 私人令牌 => 生成新令牌

![image-20220723004651954](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004651954.png)

私人令牌描述任意，权限选择按下图修改即可

![image-20220723004703935](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004703935.png)

点击提交并验证后显示如下界面：(我的令牌：42c1f31ba85c98c06a9605e7b39b5d66)

![image-20220723005003053](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723005003053.png)

> 注意：生成的私人令牌要保存好，之后的操作要用到。
>

# 6 node.js
安装 PicGo 需要用到 node.js,所以我们必须安装它。

安装教程：[[nodejs安装]]

# 7 配置 PicGo
通过插件添加 gitee，依次点击插件设置 => 输入 gitee => 安装 gitee-uploader

![image-20220723004800207](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723004800207.png)

在图床设置中选择 gitee，配置如下图所示

![image-20220723010340278](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723010340278.png)

# 8 在 Typora 中使用
旧版免费安装包：[typora-x64](https://www.aliyundrive.com/s/rF4c6usVegF)

安装后，选择文件 => 偏好设置，然后按照下图进行配置

> 注意：因为已经上传过的图片不能再次上传测试图片上传功能直接用PicGo自带的

![image-20220723005224415](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220723005224415.png)

> 注意：gitee 上的图片超过 1MB 需要登录才能查看，可以进行压缩后再上传。
