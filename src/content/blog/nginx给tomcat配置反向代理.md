---
title: nginx给tomcat配置反向代理
tags:
  - Vue
  - JS
---

### 说在前面

可以利用 nginx 作为分发服务器,给 tomcat 下面的项目做代理.

### 配置方法

在 nginx 根目录下面的 conf 文件夹下,打开 nginx.conf 进行编辑(在 http 内添加如下内容):

```js
# 设置tomcat代理
 upstream tomcat {
       server 127.0.0.1:8080;
 }
 server {
     listen 80;
     server_name www.xieyezi.com;
     location / {
         proxy_set_header        Host $host;
         proxy_set_header        X-Real-IP $remote_addr;
         proxy_pass http://tomcat;
     }
 }
```

  <!-- more -->

在 nginx 根目录打开命令行,输入:

```
  nginx -s reload
```

注意这里有几个要注意的点,server_name 配置的域名必须与 tomcat 的 serve.xml 里面的 Host 的 name 字段要一致.
完成了重启之后,就可以访问`www.xieyezi.com\项目名 ` ,访问到 tomcat 服务器下面到项目.
那么实现分布式反向代理的原理是什么呢,请看下面这个图:

![寻找终点 (1).png](https://i.loli.net/2018/11/06/5be1b215b14da.png)

看了上面的图就很清楚了.
