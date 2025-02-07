---
title: js 中的 debounce(防抖) 和 throttle(节流)
date: 2020-04-20T00:00:00
lastMod: 2019-04-20T00:00:00
tags: [教程, 节流和防抖, JavaScript]
category: 笔记
summary: 在进行窗口的resize、scroll，输入框内容校验等操作时，如果事件处理函数调用的频率无限制，会加重浏览器的负担，导致用户体验非常糟糕。
---

## 使用场景

在进行窗口的resize、scroll，输入框内容校验等操作时，如果事件处理函数调用的频率无限制，会加重浏览器的负担，导致用户体验非常糟糕。  
此时我们可以采用debounce（防抖）和throttle（节流）的方式来减少调用频率，同时又不影响实际效果。

## 名词解释

### 防抖函数

防抖（debounce）：当持续触发事件时，一定时间段内没有再触发事件，  
事件处理函数才会执行一次，如果设定的时间到来之前，又一次触发了事件，就重新开始延时。  
如下图，持续触发scroll事件时，并不执行handle函数，当1000毫秒内没有触发scroll事件时，才会延时触发scroll事件。

![debounce.png](https://res-static.hc-cdn.cn/fms/img/6862e0a0a403e16445761f89e2966fb71603448561229)

### 节流函数

节流（throttle）：当持续触发事件时，保证一定时间段内只调用一次事件处理函数。  
节流通俗解释就比如我们水龙头放水，阀门一打开，水哗哗的往下流，秉着勤俭节约的优良传统美德，我们要把水龙头关小点，最好是如我们心意按照一定规律在某个时间间隔内一滴一滴的往下滴。  
如下图，持续触发scroll事件时，并不立即执行handle函数，每隔1000毫秒才会执行一次handle函数。
![throttle.png](https://res-static.hc-cdn.cn/fms/img/3d0c29700d9a89ef7d33a2d7ac2e9fb31603448561230)

## 代码实现

### 防抖：

```ts
// 防抖
function debounce (fn: Function, wait) {
  var timeout = null;
  return function () {
    if (timeout !== null) {
      clearTimeout(timeout);
    }

    timeout = setTimeout(fn, wait);
  }
}

// 处理函数
function handle () {
  console.log(Math.random());
}

// 滚动事件
window.addEventListener('scroll', debounce(handle, 1000));
```

当持续触发scroll事件时，事件处理函数handle只在停止滚动1000毫秒之后才会调用一次。  
也就是说在持续触发scroll事件的过程中，事件处理函数handle一直没有执行。

### 节流
```ts
var throttle = function (func, delay) {
  var timer = null;
  return function () {
    var context = this;
    var args = arguments;

    if (!timer) {
      timer = setTimeout(function () {
        func.call(context, args);
        timer = null;
      }, delay);
    }
  }
}

function handle () {
  console.log(Math.random());
}

window.addEventListener('scroll', throttle(handle, 1000));
```

当触发事件的时候，我们设置一个定时器，再次触发事件的时候，如果定时器存在，就不执行，直到delay时间后，定时器执行执行函数，并且清空定时器，这样就可以设置下个定时器。当第一次触发事件时，不会立即执行函数，而是在delay秒后才执行。而后再怎么频繁触发事件，也都是每delay时间才执行一次。当最后一次停止触发后，由于定时器的delay延迟，可能还会执行一次函数。

## 总结

### 防抖：
将几次操作合并为一此操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

### 节流：
使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。

### 区别：
函数节流不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而函数防抖只是在最后一次事件后才触发一次函数。 比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用节流技术来实现。

## 参考
[https://www.lodashjs.com/docs/lodash.debounce](https://www.lodashjs.com/docs/lodash.debounce)
[https://www.huaweicloud.com/articles/d3842d8455d26db6868c19c7daced54b.html](https://www.huaweicloud.com/articles/d3842d8455d26db6868c19c7daced54b.html)
[https://dev.to/heymarkkop/debounce-x-throttle-23k5](https://dev.to/heymarkkop/debounce-x-throttle-23k5)