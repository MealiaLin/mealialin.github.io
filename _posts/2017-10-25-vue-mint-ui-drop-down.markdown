---
layout: post
title: vue使用mint-ui实现下拉刷新和无限滚动
date: 2017-10-25 00:00:00 +0300
description:  # Add post description (optional)
img: # Add image post (optional)
tags: [vue.js,mint-ui] # add tag
---

在开发web-app中，总会遇到v-for出来的li会有很多，当数据达几百上千条的时候，一起加载出来会造成用户体验很差的效果。
这时候我们可以使用无限滚动和下拉刷新来实现控制显示的数量，当刷新到底部的边界的时候会触发无限滚动的事件，再次加载一定数量的条目。

还是拿在项目中的功能来举栗子介绍。

![drop-down(1)]({{site.baseurl}}/assets/img/vue-drop-down(1).png)

有个列表，几千条数据，做分页查询，限制每次显示查询20条，每次拉到最后20条边缘的时候，触发无限滚动，这时候会出现加载图标，继续加载后续20条数据，加载到最后的时候会提示数据“加载完毕”。

![drop-down(2)]({{site.baseurl}}/assets/img/vue-drop-down(2).png)

项目的ui使用的mint-ui，所以使用的无限滚动也是mint-ui里面的，详细参考[官方文档][1]

接下来给大家介绍代码实现：
1、为元素添加 v-infinite-scroll 指令即可使用无限滚动。滚动该元素，当其底部与被滚动元素底部的距离小于给定的阈值（通过 infinite-scroll-distance 设置）时，绑定到 v-infinite-scroll 指令的方法就会被触发。

```html
<!--ul里面几个scoller就是无限滚动的几个api-->
<ul class="mui-table-view" v-infinite-scroll="loadMore" infinite-scroll-disabled="moreLoading"
            infinite-scroll-distance="0" infinite-scroll-immediate-check="false">
  <!--li数据遍历循环部分-->
  <li class="mui-table-view-cell" v-for="item in list">
    <a class="mui-navigate-right">
      <span class="mui-pull-left">{ {item.name}}</span>
      <span class="mui-pull-right">{ {item.date.substring(0,10)}}</span>
    </a>
  </li>
  <!--底部判断是加载图标还是提示“全部加载”-->
  <li class="more_loading" v-show="!queryLoading">
    <mt-spinner type="snake" color="#00ccff" :size="20" v-show="moreLoading&&!allLoaded"></mt-spinner>
    <span v-show="allLoaded">已全部加载</span>
  </li>
</ul>
```
2、script部分

```javascript
<script>
  export default {
    data() {
      return {
        //初始化无限加载相关参数
        queryLoading: false,
        moreLoading: false,
        allLoaded: false,
        totalNum: 0,
        pageSize: 20,
        pageNum: 1,
      }
    },
    computed: {
      params() {
        return{
          //这里先定义要传递给后台的数据
		  //然后将每次请求20条的参数一起提交给后台
          pageSize: this.pageSize
      	}
      }
    },
    methods: {
	  //无限加载函数
      loadMore() {
        if(this.allLoaded){
          this.moreLoading = true;
          return;
        }
        if(this.queryLoading){
          return;
        }
        this.moreLoading = !this.queryLoading;
        this.pageNum++;
        this.$http.post("请求后台数据的接口",
                    Object.assign({pageNum:this.pageNum},this.params)).then((res) => {
          if(res.sData && res.sData.list){
            this.list = this.list.concat(res.sData.list);
            this.allLoaded = this.debtList.length==this.totalNum;
          }
          this.moreLoading = this.allLoaded;
        });
      }
    },
  }
</script>
```
到这里就可以实现无限滚动了，这里结合了mint-ui的[Infinite scroll][2]和[Spinner][3]

  [1]: http://mint-ui.github.io/docs/#/zh-cn2/infinite-scroll
  [2]: http://mint-ui.github.io/docs/#/zh-cn2/infinite-scroll
  [3]: http://mint-ui.github.io/docs/#/zh-cn2/spinner

[本文在segmentfault的地址][4]

   [4]: https://segmentfault.com/a/1190000011719169

