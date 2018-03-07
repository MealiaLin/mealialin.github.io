---
layout: post
title: java构造list，合并重复的数组
date: 2017-12-21 00:00:00 +0300
description:  # Add post description (optional)
img: # Add image post (optional)
tags: [java] # add tag
---

在开发项目中遇到了这样的一个问题：
一个repeatList里面有这样的数据：

```java
repeatList=[
    {sort='0', company='A公司', value='28432'}
    {sort='0', company='A公司', value='8263685'}
    {sort='0', company='A公司', value='1234'}

    {sort='0', company='B公司', value='2234'}
    {sort='0', company='B公司', value='567'}

    {sort='0', company='C公司', value='85789'}
    ...
]
```
我要怎么做才能把他们合并为

```java
list=[
    {sort='0', company='A公司', value1='28432' , value2='8263685',value3='1234'}
    {sort='0', company='B公司', value1='2234' , value2='567'}
    {sort='0', company='C公司', value1='85789'}
]
```
![merge-duplicate-data]({{site.baseurl}}/assets/img/merge-duplicate-data.png)

这边根据b字段的公司名将同一公司的不同数据构造一起，做法如下：

总结：双重遍历+去重

（1）双重遍历构造数据：
```java
//存储构造出来的list,list类型根据项目变化而变化,下面的问号都表示相关的类型，在这里都是要一致的
List<?> oList = new ArrayList<>();

Map<String,List<?>> map = new HashedMap();
//第一层for遍历
for(? input : repeatList){
    List<?> list = map.get(input.getString("company"));
    if (list == null) {
        list = new ArrayList<>();
        list.add(input);
        map.put(input.getString("company"), list);
    }
    for (? input2 : repeatList){
        if(input.getString("company").equals(input2.getString("company")) && !list.contains(input2)){
            list.add(input2);
        }
    }
    /*合并筛选出来的同类数据,new出一个用来暂时存储遍历的数据的，
    定义在外面，然后for完之后再用oLsit进行add，就会出现同属性名的值会被后面覆盖，不同属性名则补充添加，
    所以company和sort就无需变属性名，但是value就需要变化*/

    ? mergePd = new ?();

    //第二层for遍历
    for(? x : list){
        mergePd.put("company", String.valueOf(x.get("company")));
        mergePd.put("sort", String.valueOf(x.get("sort")));
        //定义一个标志，改变value的属性名，达到区分的效果
        String sign = 1;
        mergePd.put("value" + sign, String.valueOf(x.get(value)));
        sign ++;
    }
    oList.add(mergePd);
}
```
经过上面的构造，会出现这样的结果：

```java
oList=[
    {sort='0', company='A公司', value1='28432' , value2='8263685',value3='1234'}
    {sort='0', company='A公司', value1='28432' , value2='8263685',value3='1234'}
    {sort='0', company='A公司', value1='28432' , value2='8263685',value3='1234'}

    {sort='0', company='B公司', value1='2234' , value2='567'}
    {sort='0', company='B公司', value1='2234' , value2='567'}

    {sort='0', company='C公司', value1='85789'}
]
```
（2）去重
因为是每条数据都遍历了，所以会出现一定数量的重复，这时候就需要做去重的步骤，使用java的set方法即可，即在上面方法后面补上：

```java
//set去除outList里面重复的数据，保证每条数据的唯一性
Set set = new HashSet(oList);
oList = new ArrayList(set);

List<?> outList;
outList.addAll(oList);
```
（3）这时候的结果就是：

```java
outList=[
    {sort='0', company='A公司', value1='28432' , value2='8263685',value3='1234'}
    {sort='0', company='B公司', value1='2234' , value2='567'}
    {sort='0', company='C公司', value1='85789'}
]
```
这就达到我们想要的结果了

[本文在segmentfault的地址][1]

   [1]: https://segmentfault.com/a/1190000012522652

