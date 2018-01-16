---
layout: post
title:  "scrollIntoView与scrollIntoViewIfNeeded的使用"
date:   2017-10-04 8:32:41 +0800
categories: 前端
tags: 页面交互
---
在日常开发中经常遇到需将某个元素放在视窗之内，让用户一直可见，但是移动端的软键盘会挡住元素。此前一般都是更改scroll位置和更改页面布局来解决此问题，这样
处理起来颇为麻烦。但是通过Element.scrollIntoView()和Element.scrollIntoViewIfNeeded()可以方便的实现这个需求。
<br/>
scrollIntoView,此API基本所有浏览器都是支持的。scrollIntoViewIfNeeded，此APIIE和foxfire不支持，所以PC端不能使用，但是移动端是没有问题的。
<br/>
<h3>scrollIntoView</h3>
先看demo
<iframe width="100%" height="300" src="//jsfiddle.net/ppkgc8m1/embedded/js,html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
同时scrollIntoView可以接受一个两种类型的参数，分别是Boolean和Object。
<br/>
先说Boolean型参数，顾名思义，参数可以使true和false。如果为true，元素的顶端将和其所在滚动区的可视区域的顶端对齐。若为false，元素的底端将和其所在滚动区的可视区域的底端对齐。demo:
<iframe width="100%" height="300" src="//jsfiddle.net/ppkgc8m1/2/embedded/js,html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
Object型参数，这个对象有两个选项，也就是对象里面的key。block与之前的Boolean型参数一致，不过值不再是true和false，是更语义化的start和end。
<h3>scrollIntoViewIfNeeded</h3>
两者主要区别有两个。首先是scrollIntoViewIfNeeded是比较懒散的，如果元素在可视区域，那么调用它的时候，页面是不会发生滚动的。其次是scrollIntoViewIfNeeded只有Boolean型参数。
<br/>
scrollIntoViewIfNeeded可以接受一个Boolean型参数，和scrollIntoView不同，true为默认值，但不是滚动到顶部，而是让元素在可视区域中居中对齐；false时元素可能顶部或底部对齐，视乎元素靠哪边更近。
<br/>
demo:
<iframe width="100%" height="300" src="//jsfiddle.net/ppkgc8m1/3/embedded/js,html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>