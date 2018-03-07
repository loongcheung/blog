---
title: React中key的讲解
date: 2018-03-07 10:48:45
categories: 前端
tags: react
---

通过阅读React的[文档](https://reactjs.org/)我们知道React这个框架的核心思想是，将页面分割成一个个组件，一个组件还可能嵌套更小的组件，每个组件有自己的数据（属性/状态）;当某个组件的数据发生变化时，更新该组件部分的视图。更新的过程是由数据驱动的，新的数据自该组件顶层向下流向子组件，每个组件调用自己的render方法得到新的视图，并与之前的视图作diff-比较差异，完成更新，这是react实现的diff算法。这个过程就叫作reconciliation-调和。

React通过virtual dom来实现高效的视图更新。基本原理是用纯js对象模拟dom树，每当更新时，根据组件们的render方法计算出新的虚拟dom树，并与此前的虚拟dom树作diff，得到一个patch（差异补丁），最后映射到真实dom树上完成视图更新。而两棵树的完全的 diff 算法是一个时间复杂度为 O(n^3)的问题。但是在前端当中，很少出现跨越层级移动DOM元素的情况，所以React采用了简化的diff算法，只会对virtual dom中同一个层级的元素进行对比，这样算法复杂度就可以达到 O(n)。

由于React采用的diff算法是对新旧虚拟dom树同层级的元素挨个比较，碰到循环输出的元素时会有一些问题，比如列表。先来看一个例子：
```html
// 旧v-dom
<ul>
  <li>first</li>
  <li>second</li>
</ul>
// 新v-dom
<ul>
  <li>zero</li>
  <li>first</li>
  <li>second</li>
</ul>
```
React在diff两棵树时，发现原来的两个li元素都与新v-dom中对应位置上的两个li元素不同，就会对其修改，并向真实dom树中插入新的second节点。实际上，我们可能只是进行了在first之前插入新zero节点的操作，而现在进行了额外的修改操作。
React官方文档提示我们应该使用key属性来解决上述问题。key是一个字符串，用来唯一标识同父同层级的兄弟元素。当React作diff时，只要子元素有key属性，便会去原v-dom树中相应位置（当前横向比较的层级）寻找是否有同key元素，比较它们是否完全相同，若是则复用该元素，免去不必要的操作。
延续第一个例子，如果每个li元素都有key属性：
```html
// 旧v-dom
<ul>
  <li key="1">first</li>
  <li key="2">second</li>
</ul>
// 新v-dom
<ul>
  <li key="0">zero</li>
  <li key="1">first</li>
  <li key="2">second</li>
</ul>
```
现在React就知道了，新增了key为"0"的元素，而"1"与"2"仅仅移动了位置。
key必须是字符串类型，它的取值可以用数据对象的某个唯一属性，或是对数据进行hash来生成key。
```jsx
<ul>
    {list.map(v=> <li key={v.idProp}>{v.text}</li>)}
</ul>
```
但是强烈不推荐用数组index来作为key。如果数据更新仅仅是数组重新排序或在其中间位置插入新元素，那么视图元素都将重新渲染。来看下例子：
```jsx
<ul>{list.map((v,idx)=><li key={idx}>{v}</li>)}</ul>
// ['a','b','c']=>
<ul>
    <li key="0">a</li>
    <li key="1">b</li>
    <li key="2">c</li>
</ul>
// 数组重排 -> ['c','a','b'] =>
<ul>
    <li key="0">c</li>
    <li key="1">a</li>
    <li key="2">b</li>
</ul>
```
React发现key为0,1,2的元素的text都变了，将会修改三者的html，而不是移动它们。

* 文章参考至[zenggo曾狗](https://www.jianshu.com/p/0218ff2591ec)

