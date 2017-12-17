---
title: ECharts-Java 详细介绍
tags: 
	- ECharts
	- Java
	- 报表
---

这是一个针对 ECharts 2.0 版本的Java类库，实现了所有ECharts中的Json结构对应的Java对象，并且可以很方便的创建Option
<!-- more -->
本项目是一个供Java开发使用的ECharts的开发包，主要目的是方便在Java中构造ECharts中可能用到的全部数据结构，如完整的结构 Option。Option中的数据Series，包含Bar-柱状图，Line-折线图，Pie-饼图，Chord-和弦图等，支持ECharts中的 所有图表。支持所有的Style类，如AreaStyle,ChordStyle,ItemStyle,LineStyle,LinkStyle等等。支 持多种Data数据类型，一个通用的Data数据，以及PieData,MapData,ScatterData,KData等针对性的数据结构。

你可以使用本项目直接构造一个Option对象，转换为JSON后直接用js设置myChart.setOption(option)，或者你也可以在前段构造基本的Option对象，然后使用本项目来构造其中需要的任意某部分的数据，如使用Series支持的图表类型创建Series数据。

### 关于本项目的最新状态和更新日志请看
github: https://github.com/abel533/ECharts

### 项目支持
#### 图表类型
* Line - 折线（面积）图
* Bar - 柱状（条形）图
* Scatter - 散点（气泡）图
* K - K线图
* Pie - 饼（圆环）图
* Radar - 雷达（面积）图
* Chord - 和弦图
* Force - 力导向布局图
* Map - 地图
* Gauge - 仪表盘
* Funnel - 漏斗图
* Island - 孤岛图（官方未提供，这里只有数据Island对象）
* EventRiver - 事件河流图
* Venn - 韦恩图
* Tree - 树图
* Treemap - 矩形树图
* WordCloud - 词云
* Heatmap - 热力图

#### ECharts组件
* Axis - 坐标轴
* Grid - 网格
* Title - 标题
* Tooltip - 提示
* Legend - 图例
* DataZoom - 数据区域缩放
* DataRange - 值域漫游
* Toolbox - 工具箱

#### Option说明

1. Option正式代码中使用，不需要任何依赖。
2. GsonOption正式代码中可以使用，需要引入Gson包，使用toString()方法可以转换为JSON结构的数据（支持function，详情看下面的function说明）。
3. EnhancedOption测试专用，主要方便在浏览器中直接查看效果。

#### function说明

由于JSON标准中不包含function类型，因而大多数的JSON库都不直接支持这种类型，处理这种类型最简单的方式就是转换为JSON字符串时，对字符串进行处理。
虽然像jackson json和fastjson通过注解或者自定义的实现序列化接口可以实现，毕竟和JSON库的结合太密切了，而且使用起来也很麻烦，不如直接处理字符串，或者在js中处理。
本项目中提供了GSON实现的GsonOption，就是重写了toString()方法，改为输出JSON结构的字符串，并且支持以下两种function形式:

```
{
    formatter:function(value){
        return value.substring(0,8);
    }}//和{
    formatter:(function(){
        return 'Temperature : <br/>{b}km : {c}°C';
    })()
 }
```

当然这种形式在Java中书写的时候有着严格的要求：
{% codeblock %}
option.tooltip().trigger(Trigger.axis).formatter("function(value){return value.substring(0,8);}");option.tooltip().trigger(Trigger.axis).formatter("(function(){return 'Temperature : <br/>{b}km : {c}°C';})()");
{% endcodeblock %}

1. 先看第一种，这里的"function中，双引号和function必须连着，中间不能存在空格，否则不会识别。然后是结尾的}"，这里也必须连着不能有空格，在首尾这两段代码之间不能出现}"，否则会判断出错。如果在{}中的代码有字符串，请使用单引号'，双引号出现在单引号内时使用\"即可。除此之外没有别的限制。

2. 再看第二种，这里的"(function中，双引号、括号和function必须连着，中间不能存在空格，否则不会识别。然后是结尾的})()"，这里也必须连着不能有空格，在首尾这两段代码之间不能出现})()"，否则会判断出错。如果在{}中的代码有字符串，请使用单引号'，双引号出现在单引号内时使用\"即可。除此之外没有别的限制。

###### 上述对function的处理很简单，不限制在formatter使用，所有值都能这么写，你可以参考写出自己的Option

