---
layout: post
title: python3实战（1）：网络小说爬取工具
date: 2018-03-13 15:30:00 +0300
description:  # Add post description (optional)
img: # Add image post (optional)
tags: [python3] # add tag
---

### 一、前言 ###

    本文是http://blog.csdn.net/c406495762/article/details/71158264的学习笔记 
    作者:Jack-Cui 
    博主链接:http://blog.csdn.net/c406495762
    
    运行平台： OSX 
    Python版本： Python3.x 
    IDE： pycharm 

> **Beautiful Soup** 
>
> 简单来说，BeautifulSoup是python的一个库，最主要的功能是从网页抓取数据。官方解释如下：
>
> * BeautifulSoup提供一些简单的、python式的函数用来处理导航、搜索、修改分析树等功能。它是一个工具箱，通过解析文档为用户提供需要抓取的数据，因为简单，所以不需要多少代码就可以写出一个完整的应用程序。
>
> * BeautifulSoup自动将输入文档转换为Unicode编码，输出文档转换为utf-8编码。你不需要考虑编码方式，除非文档没有指定一个编码方式，这时，Beautiful Soup就不能自动识别编码方式了。然后，你仅仅需要说明一下原始编码方式就可以了。
>
> * BeautifulSoup已成为和lxml、html6lib一样出色的python解释器，为用户灵活地提供不同的解析策略或强劲的速度。

> **lxml**
>
>lxml是个非常有用的python库，它可以灵活高效地解析xml，与BeautifulSoup、requests结合，是编写爬虫的标准姿势。

### 二、实战步骤 ###

爬虫在[github的地址](https://github.com/MealiaLin/myPython/tree/master/novelDownload)

**1、环境安装**

这里我们需要用到python的pip或者pip3来安装相关库和依赖，做法是运行cmd，转到你本地python安装目录下，有个script文件夹，到该文件夹的路径下运行安装命令就行了

安装BeautifulSoup：
```
pip3 install beautifulsoup4
```
安装lxml：
```
pip3 install lxml
```

**2、PyCharm配置**

如果使用开发工具的话，如果没有添加beautifulsoup4和lxml库的话，也会报错

打开File/Settings/Project:xxx/Project Interpreter，首先，在Project Interpreter选择本地python安装目录下的python.exe，
然后，在下面的列表中添加beautifulsoup4和lxml库

![图片1]({{site.baseurl}}/assets/img/python/pycharm.png)

**3、预备知识**

想了解爬虫知识，首先得了解**BeautifulSoup**相关知识

详细内容可参照官方文档：[URL](http://beautifulsoup.readthedocs.io/zh_CN/latest/)

但是我觉得参照的教程的博主写的更为通俗易懂：[URL](http://blog.csdn.net/c406495762/article/details/71158264)

**4、小说内容的爬取**

这边同样的，以《笔趣看》小说网站（[URL](http://www.biqukan.com/)）为例

**（1）单章小说内容的爬取**

打开《一念永恒》小说的第一章（[URL](http://www.biqukan.com/1_1094/5403177.html)），进行审查元素分析

由审查结果可知，文章的内容存放在id为content，class为showtxt的div标签中：

![图片2]({{site.baseurl}}/assets/img/python/biqu1.png) 

局部放大：

![图片3]({{site.baseurl}}/assets/img/python/biqu2.png)

 因此我们，可以使用如下方法将本章小说内容爬取下来：
 
 ```python
 # -*- coding:UTF-8 -*-
 from urllib import request
 from bs4 import BeautifulSoup
 
 if __name__ == "__main__":
     download_url = 'http://www.biqukan.com/1_1094/5403177.html'
     head = {}
     head['User-Agent'] = 'Mozilla/5.0 (Linux; Android 4.1.1; Nexus 7 Build/JRO03D) 
           AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.166  Safari/535.19'
     download_req = request.Request(url = download_url, headers = head)
     download_response = request.urlopen(download_req)
     download_html = download_response.read().decode('gbk','ignore')
     soup_texts = BeautifulSoup(download_html, 'lxml')
     texts = soup_texts.find_all(id = 'content', class_ = 'showtxt')
     soup_text = BeautifulSoup(str(texts), 'lxml')
     #将\xa0无法解码的字符删除
     print(soup_text.div.text.replace('\xa0',''))
 ```
 运行结果：
 
 ![图片4]({{site.baseurl}}/assets/img/python/biqu3.png)
 
  可以看到，我们已经顺利爬取第一章内容，但是我们不能满足于此，我们想要的是正本小说都爬取下来
  
  接下来就是如何爬取所有章的内容，爬取之前需要知道每个章节的地址。因此，我们需要审查《一念永恒》小说目录页的内容。
 
 **（2）各章小说链接的爬取**
 
 URL：[http://www.biqukan.com/1_1094/](http://www.biqukan.com/1_1094/)
 
 由审查结果可知，小说每章的链接放在了class为listmain的div标签中。链接具体位置放在html->body->div->dd->dl->a的href属性中，例如下图的第759章的href属性为/1_1094/14235101.html，
 那么该章节的地址为：[URL](http://www.biqukan.com/1_1094/14235101.html)
 
 因此，我们可以使用如下方法获取正文所有章节的地址：
 
 ```python
 # -*- coding:UTF-8 -*-
 from urllib import request
 from bs4 import BeautifulSoup
 
 if __name__ == "__main__":
     target_url = 'http://www.biqukan.com/1_1094/'
     head = {}
     head['User-Agent'] = 'Mozilla/5.0 (Linux; Android 4.1.1; Nexus 7 Build/JRO03D) 
                    AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.166  Safari/535.19'
     target_req = request.Request(url = target_url, headers = head)
     target_response = request.urlopen(target_req)
     target_html = target_response.read().decode('gbk','ignore')
     #创建BeautifulSoup对象
     listmain_soup = BeautifulSoup(target_html,'lxml')
     #搜索文档树,找出div标签中class为listmain的所有子标签
     chapters = listmain_soup.find_all('div',class_ = 'listmain')
     #使用查询结果再创建一个BeautifulSoup对象,对其继续进行解析
     download_soup = BeautifulSoup(str(chapters), 'lxml')
     #开始记录内容标志位,只要正文卷下面的链接,最新章节列表链接剔除
     begin_flag = False
     #遍历dl标签下所有子节点
     for child in download_soup.dl.children:
         #滤除回车
         if child != '\n':
             #找到《一念永恒》正文卷,使能标志位
             if child.string == u"《一念永恒》正文卷":
                 begin_flag = True
             #爬取链接
             if begin_flag == True and child.a != None:
                 download_url = "http://www.biqukan.com" + child.a.get('href')
                 download_name = child.string
                 print(download_name + " : " + download_url)
 ```
运行结果：

 ![图片5]({{site.baseurl}}/assets/img/python/biqu4.png)
 
  **（3）爬取所有章节的内容，并保存在文件中**
  
  综合上面两点，我们要做的就是根据爬取下来所有章节的链接地址，再遍历爬取所有章节的内容，并保存在本地文件中
  
  ```python
  # -*- coding:UTF-8 -*-
  from urllib import request
  from bs4 import BeautifulSoup
  import re
  import sys
  
  if __name__ == "__main__":
      #创建txt文件
      file = open('一念永恒.txt', 'w', encoding='utf-8')
      #一念永恒小说目录地址
      target_url = 'http://www.biqukan.com/1_1094/'
      #User-Agent
      head = {}
      head['User-Agent'] = 'Mozilla/5.0 (Linux; Android 4.1.1; Nexus 7 Build/JRO03D) 
                    AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.166  Safari/535.19'
      target_req = request.Request(url = target_url, headers = head)
      target_response = request.urlopen(target_req)
      target_html = target_response.read().decode('gbk','ignore')
      #创建BeautifulSoup对象
      listmain_soup = BeautifulSoup(target_html,'lxml')
      #搜索文档树,找出div标签中class为listmain的所有子标签
      chapters = listmain_soup.find_all('div',class_ = 'listmain')
      #使用查询结果再创建一个BeautifulSoup对象,对其继续进行解析
      download_soup = BeautifulSoup(str(chapters), 'lxml')
      #计算章节个数
      numbers = (len(download_soup.dl.contents) - 1) / 2 - 8
      index = 1
      #开始记录内容标志位,只要正文卷下面的链接,最新章节列表链接剔除
      begin_flag = False
      #遍历dl标签下所有子节点
      for child in download_soup.dl.children:
          #滤除回车
          if child != '\n':
              #找到《一念永恒》正文卷,使能标志位
              if child.string == u"《一念永恒》正文卷":
                  begin_flag = True
              #爬取链接并下载链接内容
              if begin_flag == True and child.a != None:
                  download_url = "http://www.biqukan.com" + child.a.get('href')
                  download_req = request.Request(url = download_url, headers = head)
                  download_response = request.urlopen(download_req)
                  download_html = download_response.read().decode('gbk','ignore')
                  download_name = child.string
                  soup_texts = BeautifulSoup(download_html, 'lxml')
                  texts = soup_texts.find_all(id = 'content', class_ = 'showtxt')
                  soup_text = BeautifulSoup(str(texts), 'lxml')
                  write_flag = True
                  file.write(download_name + '\n\n')
                  #将爬取内容写入文件
                  for each in soup_text.div.text.replace('\xa0',''):
                      if each == 'h':
                          write_flag = False
                      if write_flag == True and each != ' ':
                          file.write(each)
                      if write_flag == True and each == '\r':
                          file.write('\n')
                  file.write('\n\n')
                  #打印爬取进度
                  sys.stdout.write("已下载:%.3f%%" % float(index/numbers) + '\r')
                  sys.stdout.flush()
                  index += 1
      file.close()
  ```
  
  代码略显粗糙，运行效率不高，还有很多可以改进的地方
 
 