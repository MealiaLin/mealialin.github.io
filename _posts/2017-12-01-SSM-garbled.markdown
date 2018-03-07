---
layout: post
title: 如何解决SSM框架前台传参数到后台乱码的问题
date: 2017-12-01 00:00:00 +0300
description:  # Add post description (optional)
img: # Add image post (optional)
tags: [java,SSM框架] # add tag
---

最近在做一个SSM框架的项目，总是遇到一个问题，就是后台接收前端传递的中文参数的时候，参数是乱码的，导致sql语句经常无法执行，但是有很奇怪，在测试环境和生产环境都是正常的，就是本地开发环境总是这么坑人，那如何解决呢？

1、比较累人，就是能不传中文就不传中文参数，对于这点，大家就笑笑而过就行了。。。。

2、还是挺累人，真的得传中文，那就将中文强制转码了，如下：
```
"中文".getBytes("UTF-8");
```
3、第二点我还没尝试就找到这第三点了，至于第二点，有兴趣的可以尝试下，这第三点才是根治的办法，找了好久原来是tomcat的配置问题，打开tomcat目录下的conf/server.xml文件，找到文件里面下面的代码部分：

```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"/>
```
然后请加上一句配置URIEncoding="UTF-8"，具体如下：

```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8"/>
```
到这里就完美解决了，本宝的问题也解决了，麻麻再也不担心我忧愁的心情了。。。。吃嘛嘛香，睡的也安心了。

[本文在segmentfault的地址][1]

   [1]: https://segmentfault.com/a/1190000012244499

