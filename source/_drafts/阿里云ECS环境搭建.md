---
title: 阿里云ECS服务器环境搭建
categories: 教程
tags: 环境 nginx
---
&emsp;&emsp;我购买的阿里云ECS服务器操作系统是__CentOS 7.4 64位__，下面介绍安装各种开发、部署有关的工具步骤。
<!-- more -->

## ssh免密登录
1. 创建并绑定密钥对
    阿里云ECS服务器提供了管理界面方便操作，可以参考帮助文档，先[创建密钥对](https://help.aliyun.com/document_detail/51793.html?spm=a2c4g.11186623.2.7.bb3k1l)，创建成功后会自动下载私钥，记得保存好；然后[绑定密钥对](https://help.aliyun.com/document_detail/51796.html?spm=a2c4g.11186623.2.10.yHQnCc)
    ![](/uploads/20180410113212.png)
    记得重启服务器后生效，使用ssh登录测试一下
    ```
    $ ssh -i [.pem文件路径] username@ip地址
    ```
2. 配置登录别名
    上面的命令太长，在`~/.ssh/config`文件中增加如下配置（没有该文件则新建一个）:
    ```
    Host aliyun
        HostName [ip地址]
        User [username]
        IdentityFile [.pem文件路径]
    ```
    这样直接使用以下命令就可以免密登录了
    ```
    ssh aliyun
    ```

## 安装git
yum安装比较简单，一条命令就行

```
$ yum install git-core
```

但是yum仓库的往往更新不及时，版本比较低，推荐使用下载源码的方式进安装，参见[CentOS-7-安装最新的-Git](https://ehlxr.me/2016/07/30/CentOS-7-%E5%AE%89%E8%A3%85%E6%9C%80%E6%96%B0%E7%9A%84-Git/)

## 安装nodejs

``` bash
$ curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
$ sudo yum install -y nodejs
$ sudo yum install gcc-c++ make  # Optional: install build tools
$ npm config set registry https://registry.npm.taobao.org/ # 修改npm源，建议
```

## git hook自动部署
1. 在服务器初始化一个远程git裸仓库

    ```
    $ git init --bare koa-bare.git
    ```

2. 配置git hook
    将目录切换至`koa-bare.git/hooks`，用`cp post-receive.sample post-receive`复制并重命名文件后用`vim post-receive`修改，如果没有`post-receive.sample`文件则直接新建一个：`touch post-receive`，内容如下：

    ``` bash
    #!/bin/sh

    unset GIT_DIR
    NOW_PATH=`pwd`
    DEPLOY_PATH='/user/koa'

    cd $DEPLOY_PATH
    git clean -df
    git pull origin master

    npm install
    pm2 restart index.js

    cd $NOW_PATH
    exit 0
    ```

    使用`chmod +x post-receive`增加脚本执行权限

3. 在服务器新建一个本地仓库

    ```
    $ mkdir koa
    $ cd koa
    $ git init
    $ git remote add origin ~/koa-bare.git/
    ```

4. 本地仓库添加remote源

    ``` bash
    $ git init
    $ git remote add origin ssh://user@1.2.3.4:/user/koa-bare.git 
    # 如果配置了ssh登录别名，则远程地址以一定要写成 [别名]:/user/koa-bare.git的形式，不能用ip了
    $ git push --set-upstream origin master
    ```

至此，自动化部署配置完成，当本地推送代码后，远程服务器会自动部署。这里以pm2启动的nodejs应用为例，需要提前在服务器上安装pm2 `npm install -g pm2`,另外第一次启动执行`pm2 restart`会报错，可以在服务器端执行`pm2 start`来手动启用应用，以后就可以正常使用了。

## 安装nginx
nginx安装有yum和源码包安装两种方式，建议使用源码包安装，参见[nginx服务器详细安装过程（使用yum 和 源码包两种安装方式，并说明其区别）](https://segmentfault.com/a/1190000007116797)。
使用源码包安装后，可以执行以下命令将nginx添加到环境变量
```
$ echo "export PATH=$PATH:/usr/local/nginx/sbin" >> /etc/bashrc
$ source /etc/bashrc # 实时生效
```

## 参考资料
* [SSH Config 那些你所知道和不知道的事](https://deepzz.com/post/how-to-setup-ssh-config.html)