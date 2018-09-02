---
title: nginx常用知识总结
categories: 后端
tags: nginx
---

## location用法总结

## 403 Forbidden排错记录
很多人以为是目录权限问题，把网站目录权限改为777后发现还是403，其实是nginx配置中的用户权限问题
1. 查看nginx worker进程的用户
![](http://p6ure4q2q.bkt.clouddn.com/20180503173742.png)
2. 查看网站目录的用户和组
![](http://p6ure4q2q.bkt.clouddn.com/20180503174024.png)
3. 配置nginx权限和网站目录统一
在nginx配置文件的第一行加上用户和组的配置

    ``` bash
    # user 用户名 用户组;
    user  root root;   
    ```

4. 重启nginx
```
nginx -s reload
```

## 反向代理
`proxy_pass`指令

## 负载均衡
`upstream`模块

## 页面缓存

## URL重写
`rewrite`指令

## 参考资料
1. [Nginx 反向代理、负载均衡、页面缓存、URL重写及读写分离详解](http://blog.51cto.com/freeloda/1288553)
2. [nginx配置location总结及rewrite规则写法](https://segmentfault.com/a/1190000002797606)