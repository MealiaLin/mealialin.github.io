---
layout: post
title: 使用echarts3实现散点地图
date: 2017-12-28 00:00:00 +0300
description:  # Add post description (optional)
img: # Add image post (optional)
tags: [echarts] # add tag
---

在开发过程中，我们总会接到关于数据处理分析的需求，其中有一部分很重要就是数据统计可视化展示，对于数据可视化方面，echarts这点就做的非常好。最近研究echarts，对于散点地图这一块挺感兴趣的，在这里就做一篇整个过程的分享，首先给大家看下效果图：


![echarts-scatter-plot(1)]({{site.baseurl}}/assets/img/echarts-scatter-plot(1).png)

颜色方面大致比较淡，你们可以根据自己需求调整

**一、准备**

1、新建html，这边我建立的是echarts-map.html，然后引入echarts文件，可以去[官网][1]下载（下载完整版的），然后解压我这边结合的是layui和jquery来的，所以总的引入如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1"/>
    <title>echarts-map</title>
    <link rel="stylesheet" href="plugin/layui/css/layui.css"/>
    <script src="plugin/layui/layui.js"></script>
    <script src="plugin/jquery/jquery.min.js"></script>
    <!-- 我把echarts.js改名字了，以便区分其他版本 -->
    <script src="plugin/echarts/echarts3.8.4.js"></script>
</head>
<body>
</body>
```

2、创建画布容器

```
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- 这里是上面的引用文件 -->
</head>
<body>
    <div class="model">
        <div class="panel-body">
            <div id="map" style="width:80%;height: 500px;float: left">
                <!-- 这边是将要存放的地图画布 -->
            </div>
        </div>
    </div>
</body>
```
**二、绘制地图**
1、echarts的中国地图想要详细绘制出各省市，需要另外引入js和json文件，先下载[map的js和json][2]，密码是uqbj。

在开始的引用文件地方将china.js文件引入：
```
<script src="plugin/echarts/map/china.js"></script>
```
2、从这里开始使用js来绘制地图，所有代码写在<script>标签里面，

（1）首先是先初始化 ECharts 示例，在 init() 中传入图表容器 Dom 对象，

（2）同时定义一个变量 option，作为图表的配置项，

（3）通过配置 option，新建一个地理坐标系 geo ，地图类型为中国地图，

（4）然后调用 setOption(option) 为图表设置配置项。

**注意：中国地图的map值为 ‘china’ ，世界地图的map值为 ‘world’ ，但如果要引用省市自治区地图 map 值为简体中文，例如 beijing.js，map 值为’北京’。**

这里结合layui和jquery：

```javascript
<script type="text/javascript">
        var layer;

        function map() {
            // 1、初始化echarts示例map
            var map = echarts.init(document.getElementById('map'));

            // 2、map的配置，配置 option，新建一个地理坐标系 geo ，地图类型为中国地图
            var option = {
                geo: {
                  	map: 'china'
              	}
            };

            //3、调用 setOption(option) 为图表设置配置项
            map.setOption(option);
        }

        layui.use(['element', 'layer'], function() {
            var element = layui.element();
            layer = layui.layer;
            $(document).ready(function () {
                map();
            });
        });
</script>
```
然后引用json格式的地图数据，通过异步加载的方式，加载完成后需要手动注册地图
这里我们使用 jQuery 的 $.get() 方法异步加载 china.json ，在回调函数中，以上述同样的方法初始化一个 mapCharts 、注册地图并设置 option，所以修改上面的代码为：

```javascript
<script type="text/javascript">
        var layer;

        function map() {
            // 1、初始化echarts示例map
            var map = echarts.init(document.getElementById('map'));

            $.get('plugin/echarts/map/json/china.json',function(chinaJson){
                echarts.registerMap('china', chinaJson); // 注册地图

                // 2、map的配置，配置 option，新建一个地理坐标系 geo ，地图类型为中国地图
                var option = {
                    geo: {
                      	map: 'china'
                  	}
                };

                //3、调用 setOption(option) 为图表设置配置项
                map.setOption(option);
            })
        }

        layui.use(['element', 'layer'], function() {
            var element = layui.element();
            layer = layui.layer;
            $(document).ready(function () {
                map();
            });
        });
</script>
```
然后就可以看到这样的地图了：

![echarts-scatter-plot(2)]({{site.baseurl}}/assets/img/echarts-scatter-plot(2).png)

4、给地图改颜色，地图的绘制都在option里面操作，有各种配置项，可以查找[官方文档][3]

```javascript
var option = {
    geo: {
      	map: 'china',
      	label: {
            emphasis: {
                show: false
            }
        },
        roam: false,
        // 定义样式
        itemStyle: {
            // 普通状态下的样式
            normal: {
                areaColor: '#ABCDEF99',
                borderColor: '#fff'
            },
            // 高亮状态下的样式,默认黄色
            emphasis: {
                //areaColor: '#2a333d'
            }
        }
  	}
};
```
改颜色后的地图如图：

![echarts-scatter-plot(3)]({{site.baseurl}}/assets/img/echarts-scatter-plot(3).png)


**三、绘制散点图**
1、新建散点图series

这里用到的数据需要两个，一个是各城市的坐标数据，一个是每个城市对应所需要的值，这里到echarts3的官网例子里面就有，这里不详细赘述，只引几个

所以这里要进行的步骤是：

（1）在 option 中添加一个 series ， series 的类型为散点图 scatter ，坐标系为地理坐标系 geo 。

（2）引入城市对应的要显示的data值

（3）引入城市的坐标值

（4）使用函数让data值和坐标值按城市名对应起来
具体看以下代码注释

```javascript
<script type="text/javascript">
        var layer;

        function map() {
            var map = echarts.init(document.getElementById('map'));

            //（2）引入data数据
            var data = [
                {name: '海门', value: 9},
                {name: '鄂尔多斯', value: 12},
                {name: '招远', value: 12},
                {name: '舟山', value: 12},
                {name: '齐齐哈尔', value: 14},
                {name: '盐城', value: 15},
                {name: '赤峰', value: 16},
                {name: '青岛', value: 18},
                {name: '乳山', value: 18},
                ...
            }

            $.get('plugin/echarts/map/json/china.json',function(chinaJson){
                echarts.registerMap('china', chinaJson); // 注册地图

                //（3）引入城市坐标
                var geoCoordMap = {
                    '海门':[121.15,31.89],
                    '鄂尔多斯':[109.781327,39.608266],
                    '招远':[120.38,37.35],
                    '舟山':[122.207216,29.985295],
                    '齐齐哈尔':[123.97,47.33],
                    '盐城':[120.13,33.38],
                    '赤峰':[118.87,42.28],
                    '青岛':[120.33,36.07],
                    '乳山':[121.52,36.89],
                    ...
                }

                //（4）将数据和城市坐标对应上
                var convertData = function (data) {
                    var res = [];
                    for (var i = 0; i < data.length; i++) {
                        var geoCoord = geoCoordMap[data[i].name];
                        if (geoCoord) {
                            res.push({
                                name: data[i].name,
                                value: geoCoord.concat(data[i].value)
                            });
                        }
                    }
                    return res;
                };

                var option = {
                    geo: {
                      	...
                  	}
                  	//（1）series 的类型为散点图 scatter
                    series: [
                		{
                            name: 'pm2.5',            // series名称
                            type: 'scatter',          // series图表类型
                            coordinateSystem: 'geo',  // series坐标系类型
                            data: convertData(data),  // series数据内容
                            //控制显示文本
                            label: {
                                normal: {
                                    show: false
                                },
                                emphasis: {
                                    show: true
                                }
                            },
                            //series样式
                            itemStyle: {
                                normal: {
                                    color: '#ddb926'
                                }
                            }
                        }
                	]
                };
                map.setOption(option);
            })
        }

        layui.use(['element', 'layer'], function() {
            var element = layui.element();
            layer = layui.layer;
            $(document).ready(function () {
                map();
            });
        });
</script>
```
这样就可以将散点渲染出来了


![echarts-scatter-plot(4)]({{site.baseurl}}/assets/img/echarts-scatter-plot(4).png)

到此基本就完成了，接下来就是样式变动了。

**四、修改样式**

1、根据数值大小改变点的大小，这个在series配置里面加上symbolSize即可：

```javascript
series: [
{
    ...
    symbolSize: function (val) {//根据数值大小控制点的大小
        return val[2] / 10;
    },
    ...
},
```
效果这样

![echarts-scatter-plot(5)]({{site.baseurl}}/assets/img/echarts-scatter-plot(5).png)

2、改变点的颜色和新增图示等,在option加上下面部分

```javascript
var option = {
    title: {
        text: '全国主要城市空气质量',
        left: 'center',
        textStyle: {
            color: '#fff'
        }
    },
    //提示框组件
    tooltip : {
        trigger: 'item'
    },
    //图例组件
    legend: {
        /*orient: 'vertical',
        y: 'bottom',
        x:'right',
        data:['pm2.5'],
        textStyle: {
            color: '#000'
        }*/
    },
    visualMap: {
        min: 0,
        max: 300,
        calculable: true,
        inRange: {
            color: ['#ABCDEF', '#99CC99']
        },
        textStyle: {
            color: '#fff'
        }
    },
    geo:{
        ...
    }
    series:[
        ...
    ]
}
```

![echarts-scatter-plot(6)]({{site.baseurl}}/assets/img/echarts-scatter-plot(6).png)

3、改变前面五大数值点的样式，首先要计算出前面五大数据，然后根据这五大数据另外添个data数据显示，如下面代码，在series再添加个配置：

```javascript
 series: [
        {
            //前面说过的配置
        },
        //后面新增配置
        {
            name: 'Top 5',
            type: 'effectScatter',
            coordinateSystem: 'geo',
            data: convertData(data.sort(function (a, b) {
                return b.value - a.value;
            }).slice(0, 5)),
            symbolSize: function (val) {
                return val[2] / 10;
            },
            showEffectOn: 'render',
            rippleEffect: {
                brushType: 'stroke'
            },
            hoverAnimation: true,
            label: {
                normal: {
                    formatter: '{b}',
                    position: 'right',
                    show: true
                }
            },
            itemStyle: {
                normal: {
                    color: '#99CC99',
                    shadowBlur: 10,
                    shadowColor: '#333'
                }
            },
            zlevel: 1
        }
    ]

};
```
到此就完成整个配置了：

![echarts-scatter-plot(7)]({{site.baseurl}}/assets/img/echarts-scatter-plot(7).png)

当然，还有很多配置项的操作可以控制整个地图变成你想要的样子，官方配置项文档就可以查看，这里就不一一列举了，举个例子给你们引导下怎么玩地图

[本文在segmentfault的地址][4]

  [1]: http://echarts.baidu.com/download.html
  [2]: https://pan.baidu.com/s/1c2wa1R6
  [3]: http://echarts.baidu.com/option.html#title
  [4]: https://segmentfault.com/a/1190000012626333

