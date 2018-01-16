---
layout: post
title:  "Jquery或zepto事件委托在ios无效"
date:   2017-09-21 12:19:22 +0700
categories: 前端
tags: dom
---

如果要给动态添加的dom绑定点击事件，就要将事件委托到document或绑定元素的父级元素上，具体代码如下：
```javascript
$(documnent).on('click','.item',function(e) {

    })
```
dom中绑定事件的元素结构为`<div class="item">按钮</div>`
<br/>
此种方式在web和安卓测试都是正常，但是在ios设备会失效，原因就是H5的标签语义化：
<br/>
根据H5新定义，默认可点击的元素有a标签和button标签，也就是给这两种标签进行事件委托是完全没有问题的。
<br/>
由此解决的办法有：
<br/>
第一种：换dom结构完全按照语意规定，点击元素使用button，超链使用a标签。
<br/>
第二种：把document换成selector的父元素，前提是父元素不是由JS生成的。
<br/>
第三种：解决的方法很巧妙，就是给需要绑定事件的元素添加一个css cursor: pointer。
<br/>
exp:
<br/>
```css
.item {
       cursor:pointer;
}
```
推荐使用第三种方法，灵活，好控制。需要注意的是，cursor属性是可继承的，如果点击元素还有子元素，而且需禁用事件，记得将cursor属性值设置为default。