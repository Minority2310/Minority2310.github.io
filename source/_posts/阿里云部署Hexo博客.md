---
title: 阿里云部署hexo博客
date: 2020-08-23 17:20:00
categories: 
- 学习
tags: 
- hexo
- blog
- 阿里云
---

# 一.准备

## 云服务器

### 域名购买

### 云服务器ECS购买

### 阿里云OSS购买



### 备案

>  ICP备案号和链接在主题文件夹的`_config.yml`中添加



### ECS配置

1. **安全组配置(入方向)**

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2020-01-16_17-28-51.png" alt="安全组" style="zoom: 67%;" />

2. **创建快照**

保证出问题时服务器可以回退到初始状态



### Git安装

```bash
sudo apt-get install git
```

### Node安装

## 本地主机

### Git安装

安装完成后查看是否安装成功

```powershell
git --version
```

### Node安装

安装完成后查看是否安装成功

```powershell
node -v
npm -v
```

### Xshell安装

用于连接云服务器

### PicGo安装

可以将本地图片上传到阿里云OSS生成url，便于Markdown图片的引用



# 二.配置

## 服务器创建git用户

创建用户：

```bash
sudo useradd git -m
```

增加git用户执行sudo的权限：

```bash
chmod 740 /etc/sudoers
vim /etc/sudoers
```

找到以下内容：`root ALL=(ALL) ALL`在下面添加一行

```bash
git     ALL=(ALL)       ALL
```

保存退出后改回权限：

```bash
chmod 400 /etc/sudoers
```



## 本机连接服务器

> **连接方式**：使用SSH的RSA非对称加密算法
>
> **工具**：XShell

### 开启认证配置

**服务器(root@user:~#)**：

```bash
# 编辑sshd_config文件
vim /etc/ssh/sshd_config
```

在`sshd_config`中进行如下配置：

```bash
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

重启SSH

```bash
systemctl stop sshd.service
systemctl start sshd.service
```

### 建立SSH信任关系

#### 本机生成密钥对

```powershell
ssh-keygen -t rsa -C "邮箱" 
# 回车默认生成路径：C:\Users\username\.ssh
# 回车输入密钥密码：************
# 回车
```

成功后默认路径下会出现新建的密钥对

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/SSH-RSA_01_20200820.png)



#### 发送公钥到服务器

**服务器(root@user:~#)**：

在`/home/git`下创建`.ssh`目录并设置权限为**600**

```bash
mkdir /home/git/.ssh
chomd 600 /home/git/.ssh
```

在`.ssh`目录中创建`authorized_keys`文件并设置权限为**600**

```bash
vi /home/git/.ssh/authorized_keys
chomd 600 /home/git/.ssh/authorized_keys
```

> 注意：
>
> - 使用证书登陆，公钥`id_rsa.pub`是有对应关系的，要把公钥写在要登陆的账户的`authorized_ keys`文件中
>
>   1. 比如要用**root**账号登陆，那么就要把公钥写在`/root/.ssh/authorized_keys`文件里
>
>   2. 如果要用**git**账号登陆，那么就要把公钥写在`/home/git/.ssh/authorized_ keys`文件里
>
> - `.ssh`目录与`authorized_ keys`权限不能太大，777之类的肯定是无法登陆的

**本机：**

将`C:\Users\username\.ssh`路径中的`id_rsa.pub`文件内容复制

**服务器(root@user:~#)**：

将本机复制的**单行**内容粘贴到`/home/git/.ssh/authorized_keys`文件中保存

```bash
vim /home/git/.ssh/authorized_keys
# i
# 粘贴
# ESC -> :wq!
```



#### 本地免密连接服务器

1. 打开XShell

2. 导入本机密钥

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/SSH-RSA_02_20200820.png" style="zoom:67%;" />

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/SSH-RSA_03_20200820.png)

3. 新建连接

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/SSH-RSA_04_20200820.png" style="zoom: 50%;" />



## 服务器创建空仓库

**切换为git用户(git@user:~$)：**

```bash
su - git                
cd /home/git/
mkdir -p projects/blog  //创建你的博客目录
mkdir repos && cd repos
git init --bare blog.git  //创建一个空仓库
cd blog.git/hooks		//创建hook钩子函数
vi post-receive
```

**改变 blog.git 目录的拥有者为git用户(git@user:~/repos$)**

```bash
sudo chown -R git:git blog.git
```

**添加备份目录**

```bash
cd /home/git/projects/
mkdir -p tmp/blog
```

**修改`post-receive`钩子函数的内容**：`vim ~/repos/blog.git/hooks/post-receive`

> 重点是git clone配置网站根目录`home/git/projects/blog`

```bash
#!/bin/bash
GIT_REPO=/home/git/repos/blog.git    # git仓库路径
TMP_GIT_CLONE=/home/git/projects/tmp/blog
PUBLIC_WWW=/home/git/projects/blog  # 网站目录
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
chmod +x post-receive          # 赋予脚本的执行权限
```

**git用户赋权**：

```bash
exit // 退出到 root 登录
chown -R git:git /home/git/repos/blog.git // 添加权限
chown git:git -R /home/git/projects/
```

本机测试，成功后实际拉下来的是你的`/home/git/projects/blog`

```
git clone git@xxx:/home/git/repos/blog.git
```

## Nginx配置

> 直接使用`sudo apt install nginx`安装出现问题，所以采用编译安装

### 下载源码编译工具

```bash
sudo apt-get update
sudo apt-get install gcc
sudo apt-get install libpcre3 libpcre3-dev
sudo apt-get install zlib1g zlib1g-dev zlib1g.dev
sudo apt-get install openssl openssl-dev
sudo apt-get install openssl libssl-dev
```

### 下载安装包

**解压到常用目录**：

```bash
cd /usr/local/
wget http://nginx.org/download/nginx-1.8.1.tar.gz
tar -zxvf nginx-1.8.1.tar.gz
#重命名
mv nginx-1.8.1 nginx 
cd nginx
#开始配置
./configure --prefix=/usr/local/nginx --with-http_ssl_module
# 编译nginx：
sudo make
# 安装nginx：
sudo make install
```

### 修改配置

```bash
vi /usr/local/nginx/conf/nginx.conf
```

- 端口号
- 修改域名
- 修改根目录位置
- 修改用户为root用户

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/nginx_01_20200823.png)

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/nginx_02_20200823.png)

### 启动Nginx

```bash
sudo /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```

> 报错：缺少下图两个文件
>
> 解决方法：创建对应目录和文件，重新执行上面的命令

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/nginx_03_20200823.png)



# 三.部署

## 创建Hexo博客

首先在本机选择一个文件夹来存放博客文件

在该文件夹中打开CMD输入命令：

```bash
npm install hexo-cli -g       # hexo全局安装
hexo init <博客名>             # 初始化博客并命名
cd <博客名>                     
cnpm install                   # 安装依赖
hexo generate(g)               # 生成静态页面(markdowm文件->html文件)  
hexo server(s)                 # 网站本地预览
```

## Hexo配置

修改博客根目录配置文件`_config.yml`中**deploy**部分，添加repo(以git用户的身份操作)：

```yaml
deploy:
  type: git
  message: update
  repo: git@xxx:/home/git/repos/blog.git
  branch: master
```

安装远程部署插件：

```bash
npm install hexo-deployer-git --save
```

文章发布：

```bash
hexo clean && hexo g -d
```



# 参考

[Hexo自动部署到阿里云(Ubantu16.04)【超详细踩坑记录】](https://blog.csdn.net/cungudafa/article/details/104585711?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522159819588219724846447789%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=159819588219724846447789&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v3~pc_rank_v3-1-104585711.pc_ecpm_v3_pc_rank_v3&utm_term=Hexo%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2%E5%88%B0%E9%98%BF%E9%87%8C%E4%BA%91%28Ubantu16.04%29%E3%80%90%E8%B6%85%E8%AF%A6%E7%BB%86%E8%B8%A9&spm=1018.2118.3001.4187](https://blog.csdn.net/cungudafa/article/details/104585711?ops_request_misc=%7B%22request%5Fid%22%3A%22159819588219724846447789%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=159819588219724846447789&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v3~pc_rank_v3-1-104585711.pc_ecpm_v3_pc_rank_v3&utm_term=Hexo自动部署到阿里云(Ubantu16.04)[超详细踩&spm=1018.2118.3001.4187) ——作者：[cungudafa](https://me.csdn.net/cungudafa)









