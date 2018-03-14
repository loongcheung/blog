---
title: 几种常见的css布局
date: 2018-03-14 11:02:16
categories: 前端
tags: css
---

<h3>左边固定，右边自适应</h3>
<h4>1、flex</h4>
```html
<div style="display: flex;height: 50px;">
	<div style="width: 200px;background: green"></div>
	<div style="flex: 1;background: red"></div>
</div>
```
<div style="display: flex;height: 50px;">
	<div style="width: 200px;background: green"></div>
	<div style="flex: 1;background: red"></div>
</div>
<h4>2、float+margin</h4>
```html
<div>
	<div style="width: 200px;background: green;float:left;height: 50px;"></div>
	<div style="background: red;margin-left:200px;height: 50px;"></div>
</div>
```
<div>
	<div style="width: 200px;background: green;float:left;height: 50px;"></div>
	<div style="background: red;margin-left:200px;height: 50px;"></div>
</div>
<h4>3、float+overflow</h4>
```html
<div>
	<div style="width: 200px;background: green;float:left;height: 50px;"></div>
	<div style="background: red;overflow: hidden;height: 50px;"></div>
</div>
```
<div>
	<div style="width: 200px;background: green;float:left;height: 50px;"></div>
	<div style="background: red;overflow: hidden;height: 50px;"></div>
</div>
<h4>4、table</h4>
```html
<div style="display: table;width: 100%; table-layout: fixed;">
	<div style="width: 200px;background: green;height: 50px;display: table-cell;"></div>
	<div style="background: red;height: 50px;display: table-cell;"></div>
</div>
```
<div style="display: table;width: 100%; table-layout: fixed;">
	<div style="width: 200px;background: green;height: 50px;display: table-cell;"></div>
	<div style="background: red;height: 50px;display: table-cell;"></div>
</div>
<h3>右边固定，左边自适应</h3>
<h4>1、flex</h4>
```html
<div style="display: flex;height: 50px;">
	<div style="flex: 1;background: red;"></div>
	<div style="width: 200px;background: green;"></div>
</div>
```
<div style="display: flex;height: 50px;">
	<div style="flex: 1;background: red;"></div>
	<div style="width: 200px;background: green;"></div>
</div>
<h4>2、float+margin</h4>
```html
<div>
	<div style="background: red;float:left;width:100%;margin-right:-200px;height: 50px;"></div>
	<div style="width: 200px;background: green;float:right;height: 50px;"></div>
</div>
```
<div>
	<div style="background: red;float:left;width:100%;margin-right:-200px;height: 50px;"></div>
	<div style="width: 200px;background: green;float:right;height: 50px;"></div>
</div>
<h4>3、table</h4>
```html
<div style="display: table;width: 100%; table-layout: fixed;">
	<div style="background: red;height: 50px;display: table-cell;"></div>
	<div style="width: 200px;background: green;height: 50px;display: table-cell;"></div>
</div>
```
<div style="display: table;width: 100%; table-layout: fixed;">
	<div style="background: red;height: 50px;display: table-cell;"></div>
	<div style="width: 200px;background: green;height: 50px;display: table-cell;"></div>
</div>
<h3>两侧固定，中间自适应</h3>
<h4>1、flex</h4>
```html
<div style="display: flex;height: 50px;">
	<div style="width: 200px;background: red;"></div>
	<div style="background: blue; flex: 1;"></div>
	<div style="width: 200px;background: green;"></div>
</div>
```
<div style="display: flex;height: 50px;">
	<div style="width: 200px;background: red;"></div>
	<div style="background: blue; flex: 1;"></div>
	<div style="width: 200px;background: green;"></div>
</div>
<h4>2、float+margin</h4>
```html
<div style="overflow: hidden;">
	<div style="width: 200px;float: left;background: red;height: 50px;"></div>
	<div style="background: blue;height: 50px;float: left;width: 100%;margin-right: -400px;"></div>
	<div style="width: 200px;float: left;background: green;height: 50px;"></div>
</div>
```
<div style="overflow: hidden;">
	<div style="width: 200px;float: left;background: red;height: 50px;"></div>
	<div style="background: blue;height: 50px;float: left;width: 100%;margin-right: -400px;"></div>
	<div style="width: 200px;float: left;background: green;height: 50px;"></div>
</div>
<h4>3、table</h4>
```html
<div style="display: table;;width: 100%;table-layout: fixed;">
	<div style="width: 200px;display:table-cell;background: red;height: 50px;"></div>
	<div style="background: blue;height: 50px;"></div>
	<div style="width: 200px;display:table-cell;background: green;height: 50px;"></div>
</div>
```
<div style="display: table;;width: 100%;table-layout: fixed;">
	<div style="width: 200px;display:table-cell;background: red;height: 50px;"></div>
	<div style="background: blue;height: 50px;"></div>
	<div style="width: 200px;display:table-cell;background: green;height: 50px;"></div>
</div>
<h3>多列等分布局</h3>
<h4>1、flex</h4>
```html
<div style="display: flex;height: 50px;">
	<div style="flex: 1;background: red;"></div>
	<div style="background: blue; flex: 1;"></div>
	<div style="flex: 1;background: green;"></div>
	<div style="flex: 1;background: yellow;"></div>
</div>
```
<div style="display: flex;height: 50px;">
	<div style="flex: 1;background: red;"></div>
	<div style="background: blue; flex: 1;"></div>
	<div style="flex: 1;background: green;"></div>
	<div style="flex: 1;background: yellow;"></div>
</div>
<h4>2、float</h4>
```html
<div>
	<div style="width: 25%;background: red;float: left;height: 50px;"></div>
	<div style="width: 25%;background: blue; float: left;height: 50px;"></div>
	<div style="width: 25%;background: green;float: left;height: 50px;"></div>
	<div style="width: 25%;background: yellow;float: left;height: 50px;"></div>
</div>
```
<div>
	<div style="width: 25%;background: red;float: left;height: 50px;"></div>
	<div style="width: 25%;background: blue; float: left;height: 50px;"></div>
	<div style="width: 25%;background: green;float: left;height: 50px;"></div>
	<div style="width: 25%;background: yellow;float: left;height: 50px;"></div>
</div>
<h4>3、table</h4>
```html
<div style="display: table;width: 100%;table-layout: fixed;height: 50px;">
	<div style="display: table-cell;background: red;"></div>
	<div style="display: table-cell;background: blue;"></div>
	<div style="display: table-cell;background: green;"></div>
	<div style="display: table-cell;background: yellow;"></div>
</div>
```
<div style="display: table;width: 100%;table-layout: fixed;height: 50px;">
	<div style="display: table-cell;background: red;"></div>
	<div style="display: table-cell;background: blue;"></div>
	<div style="display: table-cell;background: green;"></div>
	<div style="display: table-cell;background: yellow;"></div>
</div>