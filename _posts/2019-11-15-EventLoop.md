---
layout:     post
title:      Event Loop 事件循环
subtitle:   Event Loop 事件循环
date:       2019-11-15
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - JavaScript
---

# Event Loop 事件循环


Event Loop是nodejs当中特有的概念, 跟JS没有特定的关系
浏览器当中的事件处理和Event Loop没有关系

## Event Loop到底是个什么东西呢

Event Loop我认为它是nodejs当中的一套完整的处理回调的机制, 它是运行在脱离于JS执行线程之外的, 它是处理Nodejs当中所有的回调, 包括常见的读文件, 写文件, 计时器等等

Event Loop这套机制是C++实现的

它是由各个阶段组成的, Event Loop中文含义就是事件循环, 出现循环二字差不多都是一套流程反复执行的意思
就好比人的一生

生 ==> 老 ==> 病==> 死==> 投胎==> 生==> 老 ==> 病………..

其实人的一生也是在不停的循环, 在每个阶段做每个阶段应该做的事

Event Loop也是一样的
### Event Loop的几个阶段

```
timers

pending callbacks

idle, prepare

poll

check

close callbacks
```

这是nodejs官网给出的大体示意图, 具体文末给出了链接点击查看

上面的6个阶段依次执行, 循环往复

如果我们只想弄清楚我们平时工作所遇到的一些困惑setTimeout setImmediate, nextTick等这些回调执行先后的顺序问题, 我们可以简化一下上面的阶段

```
timer

poll

check
```
只研究这三个阶基本可以弄清执行顺序问题了

下面就依次说一下这三个阶段都在干什么
- timer
**setTimeout**添加的回调函数并指定时间, 含义是在某某**时间后**执行这个回调,而不是在某某**固定时间**执行这个回调
这个阶段就是执行setTimeout添加的回调函数
- poll
轮询阶段的功能
	1. 如果发现计时器的时间到了, 就会绕回**timer**阶段执行计时器的回调函数, 在绕回**timer**阶段的过程中, 会路过**check**阶段
	2. 如果计时器时间没到, 就执行poll阶段队列里面的回调
		- 如果 poll 队列不是空的,event loop 就会依次执行队列里的回调函数,直到队列被清空或者到达 poll 阶段的时间上限
		- 如果 poll 队列是空的
			1. 如果有**setImmediate**任务, event loop就结束poll阶段, 进入**check**阶段,
			2. 如果没有**setImmediate**任务event loop 就会等待新的回调函数进入 poll 队列,并立即执行它
			
一旦poll队列为空, event loop就轮询检查有没有计时器到达时间, 如果有就绕回**timer**阶段, 去执行计时器回调函数
- check
这个阶段允许开发者在 poll 阶段结束后立即执行一些函数, 如果 poll 阶段空闲了, 同时存在** setImmediate**任务，event loop 就会进入 check 阶段

**只要弄清了这三个阶段就可以弄清异步函数执行的时机了**
## setImmediate vs setTimeout
两者很像, 但是其回调的执行时机有很大的不同
参考上面三个阶段的说明

从三个阶段的说明大体应该可以分析出, event loop大部分都处于** poll**阶段
那么就假设我们执行** setImmediate**和** setTimeout**时就处于** poll**阶段
分析这段代码

```js
setTimeout(() => {
  console.log('timeout');
}, 0);

setImmediate(() => {
  console.log('immediate');
});
```
如果按照上面所说的, 应该是先打印`immediate`然后打印`timeout`
因为处理**poll**阶段时发现有计时器到达时间, 那么就绕回**timer**阶段, 在路过**check**阶段时发现有**setImmediate**添加的回调, 那么就执行这个回调

但事实并非如此, 因为这取决去执行JS代码线程的性能, 再说的具体一些就是event loop开启的时机不能保证,所以不一定就在**poll**阶段, 也有可能在别的阶段, 所以执行的顺序是不能保证的

```js
const fs = require('fs');

fs.readFile(__filename, () => {
  setTimeout(() => {
    console.log('timeout');
  }, 0);
  setImmediate(() => {
    console.log('immediate');
  });
});
```
如果是这样就可以保证了执行顺序, 原因也就是上面分析的
如果在开启event loop之前,这时JS代码已经执行完了,则开始就处于**timer**阶段, 这时发现**timer**阶段有一个0ms后执行的回调(可能这个回调已经超过0ms了,过期的回调就更要立马执行掉了), 那么就执行这个回调, 所以**setTimeout**添加的回调可能是比**setImmediate**添加的还要先执行

## nextTick
其实**nextTick**理解起来更简单一些, 可以简单地粗暴的认为**nextTick**添加的回调无论处于哪个阶段, 都会在这个阶段结束之前将**nextTick**添加的回调执行完毕

## 浏览器的微任务和宏任务
其实浏览器的微任务和宏任务和nodejs的event loop在技术层面没有任何关系

这完全就是两个东西, 不要混在一起
宏任务和微任务是两个不同的任务队列

在浏览器中宏任务就是**setTimeout**添加的回调, 然后将这个回调放入宏任务队列中
微任务就是**promise.then**添加的回调, 然后将这个回调放入微任务的队列
**await**就是promise的语法糖, 所以也是微任务, 还有一个不常用的**MutationObseve**, Vue的nextTick就是用的这个实现的还有一个废弃的**Object.observe**,这些都是微任务

那么怎么理解微任务和宏任务呢
我们可以用生活中我们处理事情时的紧急程度的形容词来对应

我们平时说的马上就做就是等我把手里的这点任务完成之后就做
我们说的一会在做就是我得把我手里的任务完成之后,下午在做
手里的任务相当于同步代码

这个形容不是很恰当,但是可以描述微任务和宏任务的两个执行的先后关系了

如果同步代码执行完成之后,微任务是先于宏任务开始执行的

如果微任务和宏任务是交替添加的呢
其实这种情况也好理解
举个🌰

```js
async function async1(){
    console.log('1')
    await async2()
    console.log('2')
}
async function async2(){
    console.log('3')
}
console.log('4')
setTimeout(function(){
    console.log('5') 
},0)  
async1();
new Promise(function(resolve){
    console.log('6')
    resolve();
}).then(function(){
    console.log('7')
})
console.log('8')
```

先说答案 4  1  3  6  8  2  7  5
说一下分析过程

首先所有同步代码执行完毕肯定实现打印**4**
可以认为async1后面的所有代码都是作为promise.then的回调函数内部了
继续分析
打印完4, 执行了async1
在执行async1之前, setTimeout添加了一个宏任务
然后打印**1**
又执行了async2
那么就打印**3**
因为async2也是await的
所以async2后面的代码相当于then中回调函数的代码
被添加进微任务队列
这时async1执行完毕
new Promise的函数参数是立即执行的
所以打印**6**
然后的then添加了一个微任务
这时new Promise执行完了
打印**8**

现在的宏任务有一个回调, 微任务有两个回调
先执行微任务队列的回调打印**2**然后打印**7**
然后是宏任务队友打印**5**



[The Node.js Event Loop, Timers, and process.nextTick() | Node.js](https://nodejs.org/de/docs/guides/event-loop-timers-and-nexttick/)

建议看英文原版的, 这样应该可以理解的更好一些
