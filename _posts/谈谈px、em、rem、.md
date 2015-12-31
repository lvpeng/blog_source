title: '谈谈px、em、rem、%'
date: 2015-11-09 14:13:20
tags: rem css
---
### 1 px, 像素， 绝对值单位

### 2 em， 相对值， 相对于父元素字体大小
```html
<div class="parent">
    <div class="myself"></div>
</div>
<style>
.parent{ font-size：10px;}
.myself{ font-size：1.4em;}
/*1.4em=10*1.4=14px*/
</style>
```
### 3.rem,也是相对值，相对于html
```css
<style>
html{font-size:20px;}
.parent{ font-size:10px;}
.myself{font-size:1.4rem;}
/*1.4rem=20*1.4=28px*/
</style>
```

### 4.%，相对于默认字体的百分比，浏览器一般默认字体是16px
font-size:62.5%;

62.5%=16*62.5%=10px;

附一张换算表
![](http://7xjx9u.com1.z0.glb.clouddn.com/px2em.png)

px,em,rem单位转换工具: http://pxtoem.com


总结：
em由于父级标签字体大小的不确定性，个人觉得不是很好用，项目中完全可以考虑排除；
px适用于普通的项目，但在做移动端时会很累，很多标签的字体大小宽高都要根据分辨率定义多次；
rem几乎适用于所有项目，在做响应式布局时，只要根据分辨率定义下html的字体的大小就好了，其他标签都字体宽度高度都跟着一起变了，淘宝就是这么干的

注：个人的理解，如有错误请帮忙纠正，别误导了新手。
