---
layout: post
title: 【vue组件通信①】父组件向子组件通信props
date: 2017-09-30 00:00:00 +0300
description:
img: i-rest.jpg # Add image post (optional)
tags: [vue.js]
---
组件实例的作用域是**孤立**的。这意味着不能 (也不应该) 在子组件的模板内直接引用父组件的数据。父组件的数据需要通过 prop才能下发到子组件中。

怎么做呢？

1、在子组件中，要显示地用props选项声明想要接收父组件的数据：
```
<template>
    <div class="mint-header is-fixed">
      <div class="mint-header-button is-left">
        <mt-button icon="back"  @click="pageGo"></mt-button>
      </div>
      <!--mytitle是接收父组件过来的数据-->
      <h1 class="mint-header-title">{ {mytitle}}</h1>
      <div class="mint-header-button is-left"></div>
    </div>
</template>
<script>
  export default {
    //此写法是控制传过来的数据类型
    props:{
      mytitle:String
    },
    data() {
      return{
      }
    },
    methods: {
      pageGo:function () {
        this.$router.go(-1)
      }/*回退到前一页的函数*/
    },
  }
</script>
```
上述写法是控制传递参数的类型props还有另一种写法，这种写法是直接声明参数，若是想要对参数加判断就使用上述写法

```
props:['mytitle'],
```
2、【静态props】在父组件中，若是单纯想要传一个静态字符串，直接如下声明即可：

```
<template>
    <div id="template">
        <!--mytitle是要传给子组件的静态字符串，
        这边my-topHeader是子组件，需要引入组件才可使用-->
        <my-topHeader mytitle="我是父组件"></my-topHeader>
    </div>
</template>
```
3、【动态props】在父组件中，有很多时候我们传递的是一个动态的参数，随着操作的变化而变化，则这时候父组件需要使用v-bind来绑定动态参数：

```
<template>
    <div id="template">
        <!--mytitle的item是要传给子组件的动态参数-->
        <my-topHeader :mytitle='item'></my-topHeader>
    </div>
</template>
```
item是一个动态参数，一般是现在data钩子里面定义，然后根据所需的操作进行动态改变数据

[本文在segmentfault的地址][1]

[1]: https://segmentfault.com/a/1190000011688941