---
title: js事件防抖动、节流处理
date: 2018-01-29 10:58:53
categories: 前端
tags: 性能
---
## 问题出发点
在开发中针对一些会频繁触发的事件如scroll、resize，如果正常绑定事件处理函数的话，有可能在很短的时间内多次连续触发事件，十分影响性能。

因此针对这类事件要进行防抖动或者节流处理

### 防抖动
它的做法是限制下次函数调用之前必须等待的时间间隔。正确实现 debouncing 的方法是将若干个函数调用合成 一次，并在给定时间过去之后仅被调用一次。

```javascript
// 将会包装事件的 debounce 函数
function debounce(fn, delay) {
  // 维护一个 timer
  let timer = null;

  return function() {
    // 通过 ‘this’ 和 ‘arguments’ 获取函数的作用域和变量
    let context = this;
    let args = arguments;

    clearTimeout(timer);
    timer = setTimeout(function() {
      fn.apply(context, args);
    }, delay);
  }
}

// 当用户滚动时被调用的函数
function foo() {
  console.log('You are scrolling!');

}

// 在 debounce 中包装我们的函数，过 2 秒触发一次
let elem = document.getElementById('container');
elem.addEventListener('scroll', debounce(foo, 2000));
```

* 首先，我们为scroll事件绑定处理函数，这时debounce函数会立即调用，因此给scroll事件绑定的函数实际上是debounce内部返回的函数。

* 每一次事件被触发，都会清除当前的 timer 然后重新设置超时调用。 这就会导致每一次高频事件都会取消前一次的超时调用，导致事件处理程序不能被触发。

* 只有当高频事件停止，最后一次事件触发的超时调用才能在delay时间后执行

更进一步，我们不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。 
这里增加一个immediate参数来设置是否要立即执行：

```javacript
function debouce(func,delay,immediate){
    var timer = null;
    return function(){
        var context = this;
        var args = arguments;
        if(timer) clearTimeout(time);
        if(immediate){
            //根据距离上次触发操作的时间是否到达delay来决定是否要现在执行函数
            var doNow = !timer;
            //每一次都重新设置timer，就是要保证每一次执行的至少delay秒后才可以执行
            timer = setTimeout(function(){
                timer = null;
            },delay);
            //立即执行
            if(doNow){
                func.apply(context,args);
            }
        }else{
            timer = setTimeout(function(){
                func.apply(context,args);
            },delay);
        }
    }
}
```

### 节流

节流是另一种处理类似问题的解决方法。 
节流函数允许一个函数在规定的时间内只执行一次。

它和防抖动最大的区别就是，节流函数不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数。

比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用节流阀技术来实现。

主要有两种实现方法：

* 时间戳
* 定时器

#### 时间戳实现：

```javascript
var throttle = function(func,delay){
    var prev = Date.now();
    return function(){
        var context = this;
        var args = arguments;
        var now = Date.now();
        if(now-prev>=delay){
            func.apply(context,args);
            prev = Date.now();
        }
    }
}
```

当高频事件触发时，第一次应该会立即执行（给事件绑定函数与真正触发事件的间隔如果大于delay的话），而后再怎么频繁触发事件，也都是会每delay秒才执行一次。而当最后一次事件触发完毕后，事件也不会再被执行了。

#### 定时器实现： 
当触发事件的时候，我们设置一个定时器，再触发事件的时候，如果定时器存在，就不执行；直到delay秒后，定时器执行执行函数，清空定时器，这样就可以设置下个定时器。

```javascript
var throttle = fucntion(func,delay){
    var timer = null;

    return funtion(){
        var context = this;
        var args = arguments;
        if(!timer){
            timer = setTimeout(function(){
                func.apply(context,args);
                timer = null;
            },delay);
        }
    }
}
```

当第一次触发事件时，肯定不会立即执行函数，而是在delay秒后才执行。 
之后连续不断触发事件，也会每delay秒执行一次。 
当最后一次停止触发后，由于定时器的delay延迟，可能还会执行一次函数。

可以综合使用时间戳与定时器，完成一个事件触发时立即执行，触发完毕还能执行一次的节流函数：

```javascript
var throttle = function(func,delay){
    var timer = null;
    var startTime = Date.now();

    return function(){
        var curTime = Date.now();
        var remaining = delay-(curTime-startTime);
        var context = this;
        var args = arguments;

        clearTimeout(timer);
        if(remaining<=0){
            func.apply(context,args);
            startTime = Date.now();
        }else{
            timer = setTimeout(func,remaining);
        }
    }
}
```

需要在每个delay时间中一定会执行一次函数，因此在节流函数内部使用开始时间、当前时间与delay来计算remaining，当remaining<=0时表示该执行函数了，如果还没到时间的话就设定在remaining时间后再触发。当然在remaining这段时间中如果又一次发生事件，那么会取消当前的计时器，并重新计算一个remaining来判断当前状态。

## 总结
防止一个事件频繁触发回调函数的方式：

* 防抖动：将几次操作合并为一此操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

* 节流：使得一定时间内只触发一次函数。它和防抖动最大的区别就是，节流函数不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而防抖动只是在最后一次事件后才触发一次函数。 原理是通过判断是否到达一定时间来触发函数，若没到规定时间则使用计时器延后，而下一次事件则会重新设定计时器。