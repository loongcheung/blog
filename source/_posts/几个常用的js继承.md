---
title: 几个常用的js继承
date: 2018-03-13 13:59:14
categories: 前端
tags: js
---

##写了三个比较常用的js继承，作为一个小的笔记方便以后查看。

### 组合式继承
```javascript
function A(name) {
	this.name = name;
	this.age = 12;
	this.colors= [1,2]
}

A.prototype.getName = function() {
	console.log(this.name)
}

function B(name,height) {
	A.call(this,name);  //调用基类构造方法，使用call将this指向派生类
	this.height = height;
}

B.prototype = new A();
B.prototype.constructor = A;
B.prototype.getHeight = function () {
	console.log(this.height)
}

var a = new A('CC');

var b = new B('BB',169);
b.getHeight();
b.getName();
b.colors.push(3);

console.log(a.name)

var c = new B('AA',170);
c.getHeight();
c.getName();
c.colors.push(4);

console.log(b.colors);
console.log(c.colors);
```

### 寄生组合式继承
```javascript
function inherit(parent, child) {
	var p = parent.prototype;
	p.constructor = parent;
	child.prototype = p;
}

function A(name) {
	this.name = name;
	this.age = 19;
}

function B(name){
	A.call(this,name);
}

inherit(A,B);

B.prototype.getB = function() {
	console.log(this.name);
}

var b = new B('HH');

b.getB();
console.log(b.age);
```

### ES6继承
```javascript
class A {
	constructor(name){
		this.name = name;
		this.age = 21;
	}
	getName() {
		console.log(this.name)
	}
	getAge() {
		console.log(this.age)
	}
}

class B extends A{
	constructor(name, height) {
		super(); //相当于 A.protoytype.constructor.call(this)
		this.name = name;
		this.height = height;
	}
	getHeight() {
		console.log(this.height)
	}
}

let bb = new B('CCC',198);
bb.getName();
bb.getAge();
bb.age = 22;
bb.getAge();
bb.getHeight();
let aa = new A('AA');
aa.getAge();
```