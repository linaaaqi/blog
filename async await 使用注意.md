---
title: async / await 使用注意
date: 2019-05-28T00:00:00
lastMod: 2019-05-28T00:00:00
tags: [教程, JavaScript, async/await]
category: 笔记
summary: js 中的 async / await 有何作用？
---

## js 中的 async / await 有何作用？
有一种特殊的语法可以更舒适地与`Promise`一起使用，称为“`async/await`”。它非常容易理解和使用。

写过`JQuery`的同学对于其中的回调肯定印象深刻，在多层嵌套回调中，代码结构简直乱如麻。众多饱受其荼毒的受害者对此起了一个形象的名称：回调地狱。

为了解决此类问题，JS提供了两个新语法：`Promise` 和 `async / await`，其中 `async / await` 一般配套使用。

## async / await 语法

```javascript
async function name([param[, param[, ...param]]]) {
   statements
}
```
### 参数
* `name`
  函数名
* `param`
  形参
* `statements`
  函数主体语句，其中 `await` 为可选使用

### 返回值
返回一个`Promise`，它将由async函数返回的值来解决，或者被async函数抛出或未捕获到的异常拒绝。

### 示例
```javascript
function resolveAfter2Seconds() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('resolved');
    }, 2000);
  });
}

async function asyncCall() {
  console.log('calling');
  const result = await resolveAfter2Seconds();
  console.log(result);
  // expected output: "resolved"
}

asyncCall();
```

## 使用注意
### `async` 函数总是返回 `Promise`
即使返回值只是一个`primitive`值，`async`函数也会通过`return`自动将返回值包装成一个`Promise`对象返回。
因此，下面两组函数是等价的。

#### 正常 (Fulfill)
```javascript
// async函数
async function foo () {
  return 'a'
}

// Promise
function foo () {
  return Promise.resolve('a')
}
```

#### 异常 (Reject)
```javascript
// async函数
async function foo () {
  throw new Error('error')
}

// Promise
function foo () {
  return Promise.reject(new Error('error'))
}
```

### `await`总是按顺序执行
使用`async`函数之前，我们还得搞清楚它的运行机制。尤其是在执行顺序上，完全用同步的思维也许并不适用于`async`函数。

```javascript
function asyncGet (x) {
  return new Promise(resolve => setTimeout(() => {
    console.log('a')
    resolve(x)
  }, 500))
}

async function test () {
  console.log('b')
  const x = 3 + 5
  console.log(x)

  const a = await asyncGet(1)
  console.log(a)

  const b = await asyncGet(2)
  console.log(b)

  console.log('c')  
  return a + b
}

const now = Date.now()
console.log('d')
test().then(x => {
  console.log(x)
  console.log(`elapsed: ${Date.now() - now}`)
})
console.log('f')
```
1. `async`函数和普通函数一样按顺序执行，同时，在执行到`await`语句时，返回一个`Promise`对象
2. `await`可以理解为将`async`函数挂起，直到等待的`Promise`被`fulfill`或者`reject`，再继续执行之后的代码
3. `async`函数的返回值和普通`Promise`没有区别

因此，上面代码输出应该是

```text
d
b
8
f
a
1
a
2
c
3
elapsed: 1010
```
> 注意 d 和 f 中间的输出

让我们再来看一个混合了`Promise`的版本。

```javascript
function asyncGet (x) {
  return new Promise(resolve => setTimeout(() => {
    console.log('a')
    resolve(x)
  }, 500))
}

async function test () {
  console.log('b')
  const x = 3 + 5
  console.log(x)

  const [a, b] = await Promise.all([
    asyncGet(1),
    asyncGet(2)
  ])

  console.log('c')  
  return a + b
}

const now = Date.now()
console.log('d')
test().then(x => {
  console.log(x)
  console.log(`elapsed: ${Date.now() - now}`)
})
console.log('f')
```

输出结果

```text
d
b
8
f
a
a
c
3
elapsed: 509
```

注意到`elapsed`的差别了吗？这就是为什么我们说`await`总是顺序执行的。不同的`await`之间无法并行执行，想要真正的完全异步还得借助类似`Promise.all`这样的方法。

### `async` 函数和 `callback`
`await` 只能能影响直接包裹它的 `async` 函数。因此在 `callback` 函数中的 `await` 并不会挂起整个 `async` 函数的执行。

一种常见的错误

```javascript
async function getAll (vals) {
  return vals.map(v => await asyncGet(v))
}
```
这段代码有语法错误，`await` 并不在 `async` 函数内部。如果给 `map` 的 `callback` 加上 `async` 呢？

```javascript
async function getAll (vals) {
  return vals.map(async v => await asyncGet(v))
}
```

这段代码虽然能执行，但还有两个问题。

1. 返回一个`Promise`对象的数组，并不是我们期待的`value`数组
2. await只会暂停`map`的`callback`，因此`map`完成时，不能保证`asyncGet`也全部完成

正确的写法还得借助 `Promise.all`

```javascript
async function getAll (vals) {
  return Promise.all(vals.map(v => asyncGet(v)))
}
```

## 总结
从上文我们可以看出，`Promise` 是 `async` 函数的基础，想要愉快的使用 `async` 函数，必须对 `Promise` 有比较深入的理解。甚至一些常见的任务，仅仅依靠 `async` 函数无法实现。
希望大家看完本文后能对 `async` 函数有加更全面的认识，这样使用起来才会更加顺手。