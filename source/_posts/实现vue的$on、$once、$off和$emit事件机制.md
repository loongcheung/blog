---
title: 实现vue的$on、$once、$off和$emit事件机制
date: 2018-03-13 16:43:27
categories: 前端
tags: vue
---

这几天利用空闲的时间了解了一下vue的源代码，学习了vue的内部实现，真的获益匪浅。然后就自己实现了类似于vue事件机制的功能代码，加强自己的编码能力。
```javascript
class Event{
	constructor() {
		this._events = Object.create(null);
	}
	$on(event,fn) {
		if(Array.isArray(event)) { //如果传的是数组就递归为每个成员绑定方法
			for(let i = 0; i < event.length; i++) {
				this.$on(event[i],fn);
			}
		}else {
			(this._events[event] || (this._events[event] = [])).push(fn);
		}
	}
	$off(event,fn) {
		if (!arguments.length) { //没传参数则全部销毁、
			this._events = Object.create(null);
			return
		}
		if (Array.isArray(event)) { //数组则递归销毁
			for(let i = 0; i < event.length; i++) {
				this.$off(event[i],fn);
			}
			return
		}
		// 特殊处理event
		const cbs = this._events[event];
		if (!cbs) { //如果不存在就返回
			return
		}
		if(arguments.length === 1) { //如果只传了event参数，则销毁event下的所有方法
			this._events[event] = null;
			return
		}
		// 特殊处理fn
		/*遍历所有方法找到对应方法删除*/
		let cb;
		let i = cbs.length - 1;
		while(i >= 0) {
			cb = cbs[i];
			if (cb === fn || cb.fn === fn) { 
				cbs.splice(i,1)
				break;
			}
			i --
		}
	}
	$once(event,fn) {
		function on () {
			//每次执行事件先销毁
			this.$off(event,on);
			//执行handler
			fn.apply(this,arguments)
		}
		this.$on(event,on);
	}
	$emit(event,arg) {
		let cbs = this._events[event];
		let args = Array.from(Array.prototype.splice.call(arguments,1,arguments.length-1));
		if (cbs) {
			cbs = cbs.length > 1 ? Array.from(cbs) : cbs;
			for (let i = 0; i < cbs.length; i++) {
				cbs[i].apply(this,args);
			}
		}
	}
}
```
