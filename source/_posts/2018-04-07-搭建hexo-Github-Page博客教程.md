---
title: 搭建Hexo+Github Pages博客教程
date: 2018-04-07 22:03:46
categories: 教程
tags:
 - hexo
 - Github Page
---
## 前言
&emsp;&emsp;Hexo 是一个快速、简洁且高效的博客框架，支持Markdown。Hexo基于Node.js，有完善的中文文档、丰富的主题、方便集成第三方服务等优点，使得我放弃了[jekyll](http://jekyllcn.com/)转向Hexo。
&emsp;&emsp;GitHub Pages 是GitHub推出的免费的静态页面托管服务，重点是__免费__的，另外对于经常使用git和GitHub的程序员来说几乎无门槛。
&emsp;&emsp;简单介绍一下，具体大家可以百度，下面介绍如何一步一步搭建博客。
<!-- more -->
## 新建username.github.io仓库
&emsp;&emsp;这里跟在GitHub新建一个仓库是一样的，只是名字为username.github.io，其中username替换为自己的名字即可。这样的一个仓库Github会自动识别为GitHub Pages，而且每个GitHub用户最多能建立一个。

## 安装Hexo并初始化博客
> 前提是已经是安装了Node.js和Git

```
$ npm install hexo-cli -g
$ hexo init <folder>
$ cd <folder>
$ npm install
$ hexo server
```

这样本地就启动了hexo服务器。访问`http://localhost:4000/`就可以看到首页

## 部署到GitHub Pages
1. 安装 hexo-deployer-git

    ```
    $ npm install hexo-deployer-git --save
    ```

2. 修改站点配置文件`_config.yml`

    ``` yml
    # URL
    ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
    url: <GitHub Pages url>
    root: /
    # Deployment
    deploy:
    type: git
    repo: <repository url>
    ```

3. 生成静态文件并部署

    ```
    $ hexo deploy --generate
    ```

    上面的命令可以简写为

    ```
    $ hexo d -g
    ```

这样你本地生成的静态文件就推送到了github仓库了，此时访问`https://username.github.io`就可以看到你的博客了

## 绑定域名
1. 获取github pages的ip地址
![](/uploads/20180408141644.png)
1. 添加域名解析
此步大家在自己购买的域名管理网站中配置即可。我购买的是阿里云域名，使用DNSPod提供的免费域名解析服务
2. 配置github pages的custom 
![](/uploads/20180408142529.png)
3. 添加CNAME
在本地博客的source目录下添加CNAME文件，内容为域名。

现在使用上面的命令重新生成静态文件并部署，就可以用域名访问你的博客啦

## 集成NexT主题
大家参照[官方文档](http://theme-next.iissnan.com/)配置即可

## 腾讯公益404页面
参照[官方文档](http://theme-next.iissnan.com/theme-settings.html#volunteer-404)，注意的是这个在本地只能通过`http://localhost:4000/404.html`来访问，只有部署到服务器上才有404的效果

## 集成评论系统
&emsp;&emsp;多说已经下线了，网易云跟帖、畅言使用不太方便，风格与NexT主题不符，Disqus是国外的经常被墙。这里我选择的是Valine配合LeanCloud使用
&emsp;&emsp;__[Valine](https://valine.js.org/) 是一款基于[Leancloud](https://leancloud.cn/)的快速、简洁且高效的无后端评论系统__。而且NexT主题已经集成了Valine的配置，使用起来非常方便

1. 配置LeanCloud
请先登录或注册LeanCloud，进入控制台后点击左上角的创建新应用
![](/uploads/20180408145009.png)
创建好了后进入应用，点击左下角设置 > 应用Key，就可以看到appid和appkey了
![](/uploads/2.png)

    {% note warning %} 
    __为了你的数据安全，请设置自己的安全域名__
    ![](/uploads/20180408154027.png)
    {% endnote %}

2. 主题配置文件开启Valine
![](/uploads/20180408145611.png)

还可以设置头像和邮件提醒，请参考[官方文档](https://valine.js.org/quickstart/)

## 集成数据统计
我这里使用的是LeanCloud来实现文章阅读量统计功能，参见[为NexT主题添加文章阅读量统计功能](https://notes.wanghao.work/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)

## 集成内容分享服务
我这里使用的是百度分享
1. 编辑站点配置文件

    ``` yml
    # share
    baidushare: true
    ```

2. 编辑主题配置文件

    ``` yml
    # Baidu Share
    # Available value:
    #    button | slide
    # Warning: Baidu Share does not support https.
    baidushare:
    type: button
    ```

## 集成搜索服务
&emsp;&emsp;我这里使用的是local search，参见[NexT官方文档](http://theme-next.iissnan.com/third-party-services.html#local-search)
&emsp;&emsp;我这里踩了一个__坑__：当我配置完后在博客首页里点击搜索就一直转圈，打开F12开发者工具，看到了正常请求search.xml，点击Preview发现了parse error。一直困扰了许久，后来在[这里](https://www.v2ex.com/amp/t/298727)找到了答案，原来是我的有篇博客里有不可见的特殊字符，我用正则表达式全局搜索了`\x08`找到了那个地方，修改了之后搜索功能就好了

## 给Front-Matter增加notoc配置
&emsp;&emsp;如果开启了toc，默认所有的文章都会生成文章目录，想实现指定文章禁用目录的功能，比如关于页面，需要自己手动修改一下模板。
&emsp;&emsp;找到主题下的sidebar.swig文件，将

``` swig
{% set display_toc = is_post and theme.toc.enable or is_page and theme.toc.enable %}
```

这一句改为

``` swig
{% set display_toc = is_post and theme.toc.enable and !page.notoc or is_page and theme.toc.enable and !page.notoc %}
```

如果需要禁用目录，在对应的Front-Matter部分添加一个notoc: true就行了。

## 侧边栏头像旋转
打开`/themes/next/source/css/_common/components/sidebar/sidebar-author.styl`，修改代码如下：

``` stylus
.site-author-image {
  display: block;
  margin: 0 auto;
  padding: $site-author-image-padding;
  max-width: $site-author-image-width;
  height: $site-author-image-height;
  /* border: $site-author-image-border-width solid $site-author-image-border-color; */

  border-radius: 50%
  transition: 1.4s all;

  &:hover {
    transform: rotate(360deg);
  }
}
```

## 更改favicon
更改站点配置文件

``` yml
# To get or check favicons visit: https://realfavicongenerator.net
# Put your favicons into `hexo-site/source/` (recommend) or `hexo-site/themes/next/source/images/` directory.

# Default NexT favicons placed in `hexo-site/themes/next/source/images/` directory.
# And if you want to place your icons in `hexo-site/source/` root directory, you must remove `/images` prefix from pathes.

# For example, you put your favicons into `hexo-site/source/images` directory.
# Then need to rename & redefine they on any other names, otherwise icons from Next will rewrite your custom icons in Hexo.
favicon:
  small: /favicon-16x16.png
  medium: /favicon-32x32.png
  apple_touch_icon: /apple-touch-icon.png
  safari_pinned_tab: /logo.svg
  android_manifest: /site.webmanifest
  ms_browserconfig: /browserconfig.xml
```

强烈推荐一个生成各种格式的favicon的在线工具：https://realfavicongenerator.net/

## 参考资源
* [我的个人博客之旅：从jekyll到hexo](https://blog.csdn.net/u011475210/article/details/79023429)
* [GitHub Pages官网](https://pages.github.com/)
* [Hexo官网](https://hexo.io/)
* [NexT官网](https://theme-next.iissnan.com/)
* [Valine —— 一款快速、简洁且高效的无后端评论系统](https://valine.js.org/)
