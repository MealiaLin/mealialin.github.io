---
layout: post
title: 【vue组件通信②】使用$emit和$on进行组件之间的传值
date: 2017-09-30 00:00:00 +0300
description:
img:  # Add image post (optional)
tags: [vue.js]
---
$emit和$on可以实现组件之间的传值，我们知道父组件传值给子组件使用props，但是不允许子组件传值给父组件，这时候使用这个就可以实现了。这也可以用在兄弟组件之间的通信。

注意：$emit和$on的事件必须在一个公共的实例上，才能够触发。

例子：我要实现某个系统的通讯录功能，在web端我们可以使用基于jQuery的ztree插件实现目录的展现，但是在vuejs框架里面，tree目录需要通过递归组件实现。
1、现在有两个组件，父组件contact_index.vue，子组件cust_tree.vue

![emit-on]({{site.baseurl}}/assets/img/emit-on.png)

2、点击父组件里面的选择银行，跳出银行树目录结构（使用vuejs的递归组件实现）,这里面需要做两种传值：
（1）父组件通过props将树目录的数据list传到子组件中形成目录结构的展示；
（2）子组件里面通过点击相应的银行请求查询银行的通讯录，这里面需要将点击的银行的机构代码回传给父组件，父组件接收后通过input将机构代码提交给后台请求查询；

第一种是父组件给子组件传值使用props即可，现在我们来谈谈第二种情况，如何将子组件的值回传给父组件。

网上百度千篇一律全是使用$emit来实现，可是都有一个严重的误区没有给别人说明，开始我都按照搜索的结果进行操作，都会出现子组件$emit后父组件没有监听到函数的变化，研究了好久才发现$emit和$on的事件必须在一个公共的实例上，才能够触发。我的操作如下：

首先在src目录下新加bus.js作为一个公共的实例

```
import Vue from 'vue'

export var bus = new Vue()
```
其次，父组件在created里面定义$on监听事件

```
//父组件与子组件都要import bus.js
import {bus} from '../../bus.js'
```

```
created(){
      bus.$on('custTreeSay',(id)=>{
        //监听传值--机构代码
        this.instCode = id;
        //关闭弹窗
        this.popupVisibleTree = false;
        //调用查询方法刷新通讯录列表
        this.query();
      });
      bus.$on('custTreeSayName',(name)=>{
        //监听传值--机构名称
        this.instName = name;
      });
}
```

最后，在子组件中定义点击事件，调用父组件方法通过$emit将相应值传给父组件

```
<span @click="propInstCode(model);propInstName(model)">
   { {model.name}}
</span>
```

```
<script type="text/javascript">
  import {bus} from '../../bus.js'
  export default {
     props: ['model'],//这里通过props接收父组件的传值

     //method钩子定义传值方法，这边需要传不同的值
     methods: {
      //通过总线将值传给父组件
      propInstCode:function (model) {
        //$emit触发当前实例事件
        bus.$emit('custTreeSay',this.model.id);
      },
      propInstName:function (model) {
        bus.$emit('custTreeSayName',this.model.name);
      }
    },
 }
```
这样就实现了子组件能通过$emit将值传给bus在传给父组件了，最后是通过传的机构代码传给提交给后台查询的，但是我们同时也需要相应的机构名字来给客户展现，所以这里面只需要设置两个input就行了，机构代码的input隐藏起来，需要传值，另外的机构名称的input显示出来就可以了，如下：

```
//将点击跳出目录选择的方法放到显示的机构选择就可以了
<div class="query_condition_item">
    <label>选择机构</label>
    <input name="instName" v-model="instName" readonly @click="showTree()">
</div>

<div class="query_condition_item">
    <input name="instCode" v-model="instCode" hidden>
</div>
```
这篇文章就到这里了，我将我开发遇到的一些问题经验记录下来，也希望能够帮到大家！！
