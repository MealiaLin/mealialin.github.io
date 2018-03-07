---
layout: post
title: vue自定义通用函数（全局函数）
date: 2017-10-18 00:00:00 +0300
description:  # Add post description (optional)
img: # Add image post (optional)
tags: [vue.js] # add tag
---

在一个项目中，经常会出现多个地方都要使用到同一个函数的情况，尤其在vue的组件之间是相互独立的情况下，要是每次要使用到函数都要重新定义，这样会显得特别累赘繁琐，也会造成代码的冗余，所以有没有办法注册一个通用的函数供所有组件使用呢？

这就需要我们来自定义全局函数了，思想跟java的通用工具类基本是一样的，看过我几篇文章的亲们就会知道我做的项目是webpack+vue2.0+...的框架下实现的，所以基于此框架，我们该如何定义全局函数呢？

1、我们可以在src目录下新建一个通用的js来存放全局函数，比如我这里是建了util.js,你们也可以取名tool.js之类的，随你们高兴。因为可能会用到其他通用的js，为了代码的规范，我都将他们放到utils的文件夹里了，这些都是自定义的，命名没有强制要求

![function]({{site.baseurl}}/assets/img/global-function.png)

然后怎么在util.js里面定义全局函数呢，如下：

```javascript
/*
 *首先是相互调用，接收的地方用import，输出的地方用export
 *比如这里面需要用到cookie.js中的方法，那么就要先把cookie引用进来，这个思想跟后面的引用是一致的
 */
import cookie from './cookie';

/*
 *接下来是定义全局函数
 *因为全局函数是要给外部使用的，所以需要将函数用export告知外部即可
 *比如我们在这里定义了日期的格式，供后面组件统一改变
 */
//Date对象转化为yyyy-MM-dd格式
export function dateFormat(dateObj){
  var year = dateObj.getFullYear();
  var month = ("0" + (dateObj.getMonth() + 1)).slice(-2);
  var day = ("0" + dateObj.getDate()).slice(-2);
  return year + "-" + month + "-" + day;
}
```
2、如何调用全局函数，就跟上面提到的一样，那里需要用到就在那个组件的script开始的地方import进util.js的文件，然后调用util里面的函数就行了，如下：

```javascript
<script>
    import * as util from '../../../utils/util'
    export default {
        data() {
          return {
              //这里调用了util的dateFormat()函数
              outputStartDate: util.dateFormat(new Date()),
          }
        }
    }
</script>
```

[本文在segmentfault的地址][1]

[1]: https://segmentfault.com/a/1190000011602147

