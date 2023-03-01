
## 一次性配置

### 准备工作

1. ios手机或者ipad一部，appstore下载安装iSH和obsidian

2. 打开iSH，进行更新

```ps1con
apk update
```

3. 依次安装好以下软件：**git、vim、openssh、openrc**，在iSH上使用命令行安装，如（apk add 软件名）

```text
apk add git
apk add vim
apk add openssh
apk add openrc
```

4. 打开obsidian，创建一个新的空仓库，例如命名为math，并关闭icloud同步，默认关闭。之后打开ios上的“文件”应用程序，在“我的iphone”中可以看到obsidian文件夹和其中的math文件夹

5. 使用git平台如codeup、github等，并创建私人仓库math。复制出该**仓库的ssh地址**，如`git@github.com:sinoeast/obsidian-.git`

### 以下需要打开iSH操作

1. 以codeup为例，在iSH上设置好和codeup托管平台一致的用户名

```text
git config --global user.name "sinoeast"
```

2. 设置和平台一致的邮箱

```text
git config --global user.email "752853800@qq.com"
```

3. ssh生成公钥

```text
ssh-keygen -t ed25519 -C "<注释内容>"
```

4. 因xclip复制出错，我们选择查看公钥

```text
cat ~/.ssh/id_ed25519.pub
```

5. 这时在ish上呈现出id_ed25519.pub里的内容

6. 将呈现出的内容从ssh开始选中到最后，进行复制。这就是iSH上的ssh公钥，一个完整的公钥格式如

`ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGUavhkkLnXrPz1EEFM2o3+ja/U6iuDIFTokIq4G46aF`

### 这一步需要登录codeup托管平台操作

配置公钥至托管平台，登陆codeup，右上角点击个人头像，点击个人设置，点击左侧的ssh公钥，将复制的公钥 `ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGUavhkkLnXrPz1EEFM2o3+ja/U6iuDIFTokIq4G46aF`粘贴上，标题随意，添加进去。

### 以下需要打开iSH操作

1. 用vim打开配置文件

```text
vim /etc/ssh/sshd_config
```

2. 在打开的文件中，添加一行新的参数为： （前面不加#号，vim操作方式请自行学习）

```text
PermitRootLogin yes
```

3. 保存退出文件后，检查ssh是否启动，如没启动会自动启动

```text
rc-service sshd restart
```

4. 在iSH中建立obsidian文件夹

```text
cd ~ && mkdir biji
```

5. 执行以下命令会打开手机文件管理器，之后需要选中本地的obsidian文件夹，点击完成。这样就让obsidian软件中的math文件夹，装载到ish上的obsidian文件夹内 ，之后对于obsidian软件内math文件的修改，iSH上也同步修改

```text
mount -t ios . obsidian
```

6. 通过cd命令，进入math文件夹内

```text
cd ~/biji
```

7. 初始化git

```text
git init
```

8. 使iSH链接你的仓库地址（之前获取的ssh地址）

```text
git remote add origin git@github.com:sinoeast/obsidian-.git
```

## 之后的日常使用

在使用obsidian编辑修改文件后，在iSH上操作同步与更新

1. 先从平台拉取到iSH

```text
git pull origin master
```

如果出错请尝试历史记录不一致拉取

```text
git pull origin master --allow-unrelated-histories
```

2. 将修改添加到缓存区

```text
git add .
```

3. 添加这次更新的备注信息

```text
git commit -m '新添加的文件内容描述'
```

4. 推送到git平台上

```text
git push origin master
```

## 注意

1. 使未更改文件提交不报错，可以设置允许空提交

```text
git commit -m "initial commit" --allow-empty
```

2. iSH是alpine linux，所以请先了解linux上vim的基本操作步骤，和linux命令行的基本文件操作，例如cd命令，rm命令，ls命令等

3. 删除iSH上的obsidian文件夹，需要在卸载obsidian后执行

```text
rm -rf obsidian
```