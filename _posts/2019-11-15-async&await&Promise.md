---
layout:     post
title:      async&await&Promise全解
subtitle:   async&await&Promise全解
date:       2019-11-15
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - JavaScript
---



# async&await&Promise全解


之前写过一篇关于async,await的文章, 感觉写的不好,当时理解的也没有现在好
所以决定再写一篇

先做出一个约定, 文中出现Promise代表的是Promise这个类
出现promise代表的是Promise的实例对象

在讨论async,await之前还是要先讨论一下Promise, 在我看来async,await就是Promise的一种语法糖

`const result = await promise`, await后面必须跟一个promise
**如果后面不是promise, 也会被转化成promise**
`const result = await 10`会被转化成`const result = await new Promise((resolve) => {resolve(10)})`

看一段关于Promise的代码

```js
function 摇色子(){
	return new Promise((resolve, reject)=>{
		setTimeout(()=>{
			resolve(Math.floor(Math.random()*6)+1)
		},2000)
	})
}
摇色子().then((result) => {
	console.log(result);
})
```
一段简单的关于Promise的基本用法的代码示例, 执行摇色子这个函数返回一个promise, 最终在then的第一个回调函数中会打印出色子的点数

下面做一些改造

```js
function 摇色子(){
	return new Promise((resolve, reject)=>{
		setTimeout(()=>{
			resolve(Math.floor(Math.random()*6)+1)
		},2000)
	})
}
摇色子().then((result) => {
	console.log(result);
})
console.log('end')
setTimeout(() => {
	console.log('timeout')
})
```
了解Promise和setTimeout执行顺序的同学应该很容易回答出打印的顺序
`end`, `色子点数`,`timeout`

讲了这么多就是为了引出**宏任务(Macro task)**,**微任务(Micro task)**这个两个概念, 但看英文单词真的是容易令中国人混淆, 如果你在听英文发音可能会感觉这个不就是同一个单词吗😭

所以容易混的话就别理会英文了, 就记住宏任务(宏伟的任务), 微任务(微小的任务), 可以大体的认为存在两个异步队列, 一个是专门的微任务异步队列, 另一个是宏任务异步队列
在这里不过多讨论这两个概念, 记住两点即可:

	- 微任务优先级高, 微任务执行完再执行宏任务
	- Promise是微任务, setTimeout是宏任务
	
## Promise的API
- `Promise.resolve(v)`制造一个成功(或者失败)
```js
function 摇色子(){
	return Promise.resolve(6)
}
```
每次都摇出6点, 为什么说或者失败呢, 看这个例子

```js
function 摇色子(){
	return Promise.resolve(
		new Promise((resolve, reject) => reject('作弊'))
	)
}
```

这个就是失败的例子
- `Promise.reject(r)` 制造一个失败
- `Promise.all(promise的数组)`等待全部成功或者其中一个失败
```js
const PA  = [Promise.resolve(1), Promise.resolve(2), Promise.resolve(3)]
Promise.all(PA).then((values) => {
	console.log(values) //[1, 2, 3]
})
```
第一个就失败了
```js
const PA  = [Promise.reject(1), Promise.resolve(2), Promise.resolve(3)]
Promise.all(PA).then((values) => {
	console.log(values) // 1
})
```
只要遇到一个失败, 后面的promise无论成功与否都不在关心. 直接就返回失败

**这也是Promise.all的一个问题, 因为我们可能需要关心所有的状态, 但是它遇到一个失败就不在关系剩余的状态了, 那么如何解决这个问题呢**

使用Promise.allSettled 这个一直等待所有的promise状态返回, 无论成功还是失败, 最终将所有的结果返回, 唯一的缺点建荣性极差, 因为这是一个新出的API

```js
const PA  = [Promise.reject(1), Promise.resolve(2), Promise.resolve(3)]
Promise.allSettled(PA).then((values) => {
	console.log(values) // 1
})
```

其实也可以用Promise.all模拟Promise.allSettled, 看下面的例子

```js
const task1 = Promise.reject(1)
const task2 = Promise.reject(2)
const task3 = Promise.resolve(3)
const PA  = [
	task1.then(function(v) {
		return {status: 'ok', v}
	}, function(r) {
		return {status: 'not ok', r}
	}), 
	task2.then(function(v) {
		return {status: 'ok', v}
	}, function(r) {
		return {status: 'not ok', r}
	}), 
	task3.then(function(v) {
		return {status: 'ok', v}
	}, function(r) {
		return {status: 'not ok', r}
	})
]

Promise.all(PA).then((values) => {
	console.log(values)
})
```

感兴趣的可以复制上面的代码到控制台看一下, 打印出的格式和allSettled是一样的
升级一下代码

```js
const task1 = Promise.reject(1)
const task2 = Promise.reject(2)
const task3 = Promise.resolve(3)
const PA  = [task1, task2, task3]

const allSettled = promiseList => 
	promiseList.map((promise) => 
		promise.then(function(v) {
			return {status: 'ok', v}
		}, function(r) {
			return {status: 'not ok', r}
		})
	) 
Promise.all(allSettled(PA)).then((values) => {
	console.log(values)
})
```

将allSettled挂载到Promise上

```js
const task1 = Promise.reject(1)
const task2 = Promise.reject(2)
const task3 = Promise.resolve(3)
const PA  = [task1, task2, task3]

const allSettled = promiseList => 
	promiseList.map((promise) => 
		promise.then(function(v) {
			return {status: 'ok', v}
		}, function(r) {
			return {status: 'not ok', r}
		})
	) 
Promise._allSettled = function(promiseList) {
	return Promise.all(allSettled(promiseList))
};
Promise._allSettled(PA).then((v) => {
	console.log(v)
})
```

- `Promise.race(promise的数组)`, 等待第一个状态发生改变的promise
- `promise.allSettled(promise的数组)`, 前面说过了, 等待所有的状态改变完, 返回所有的状态结果

### Promise应用场景
- 多次处理同一个结果
- 串行
这里的串行是指任务串行,不是一直then,then的那种(`promise.then().then().then()`)
把promise任务放进队列, 完成一个在做下一个,结合数组的reduce很好用
- 并行
`promise.all`, `promise.allSettled`

## async&await
最常见的用法

```js
const fn = async ()=>{
	const temp = await makePromise()
	return temp + 1
}
```

优点完全没有缩进, 就像在写同步代码

如果要想失败的话不需要调用`reject`, `throw error`就行了

```js
function 摇色子() {
	throw new Error()
}
async function fn() {
	try {
		const result = await 摇色子()
		console.log(result);
	} catch(e) {
		console.log(e);
	}
}
fn();
```

其实对于await捕获错误不单单 try catch一种办法
还可以用另外一种

```js
function 摇色子() {
	throw new Error()
}
async function fn() {
	const result = await 摇色子().catch((e) => {
		console.log(e);
	})
	console.log(result);
}
fn();
```

但是这个得前提是`摇色子`这个函数必须返回一个promise

**大家有没有思考过这样一个问题为什么要用await的使用必须在函数之前添加一个async, 难道存在await的函数不就是代表是异步的吗**

后来我搜索到一个答案是这样说的

在没有await,async之前有人实现了await这样的函数, 如果没有async这个关键字, 那么可能就会影响旧的代码, 其实归根结底设计async这个关键字 就是为了兼容旧代码

### await具有传染性(promise.then的回调函数也具有传染性)

这个传染性的意思是, await右面和后面的代码都会编程异步的, 虽然是同步的写法, 举个🌰解释一下

```js
function 摇色子() {return 6}
async function fn() {
	console.log(11)
	const result = await 摇色子();
	console.log(result);
	console.log(22)
}
fn();
// 11
//	 6
//	 22
```

#### await和promise应用场景的对比
- 多次处理同一个结果
```js
cosnt r1 = await makePromise();
cosnt r2 = handleR1(r1);
const r3 = handleR2(r2);
```

- 串行, 天生串行
```js
const a1 = await makePromise1();
const a2 = await makePromise2();
const a3 = await makePromise3();
```

这种情况就会依次按顺序执行

但是有一个情况会存在问题, 举个🌰

```js
const a = [makePromise1, makePromise2, makePromise3];
for(let i = 0; i < a.length; i++) {
	await a[i]();
}
```

这种情况不会按照循环的顺序来执行, 而是并发的执行
具体解决办法可以google搜索关键字**async await loop**

### 题目
```js
let a = 0;
let test = async () => { 
	a = a + await 10; 
	console.log(a)
}
test();
console.log(++a); 
```
请问打印什么

最终的结果是 1  10

分析: ** await左边的, 上面的代码是同步的, 右面的后面的是异步**, 如果你的答案错了看我这句话在理解一下
