---
layout:     post
title:      React-useState
subtitle:   React-useState
date:       2019-11-25
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - React
---

# React-useState
#React#


# useState
useState是react16Hooks的一个新的API, 作用就是react老版本的state的替身, 但是原理有了一些变化

看一段代码
[react-theory/App.js at master · echoheart/react-theory · GitHub](https://github.com/echoheart/react-theory/blob/master/src/App.js)

```ts
import React from "react";
import ReactDOM from “react-dom”;
const rootElement = document.getElementById(“root”);

function App() {
  const [n, setN] = React.useState(0);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
      </p>
    </div>
  );
}
ReactDOM.render(<App />, rootElement);
```

分析几个问题
- **执行setN的时候会发生什么**?
-  n会变吗?
- App函数会重新执行吗?
- 如果App每次都重新执行, 那么useState(0)的时候,n的值会每次都不同吗?
带着这几个问题一起分析一下useState

首先大致的分析一下每次点击按钮之后发生了什么
1. 首次渲染
	- 调用App得到虚拟dom
	- dom渲染成真实dom
2. 首次之后的渲染(点击button)
	- 调用setN(n + 1), n的值反生变化
	- 再次render App,得到虚拟dom
	- domdiff得到patch(补丁),更新真实dom
以上两种过程都在调用App这个函数, 也就是说都运行了 useState(0)


分析上面的过程可以和下面的结论联系起来
- 每个组件一定有一个state
- setN一定会修改数据state, 然后将n+1存入state
- setN一定会触发组件App重新渲染(rerender)
- useState肯定会从state读取n的最新值

下面代码验证我们上面得到的结论
[react-theory/App2.js at master · echoheart/react-theory · GitHub](https://github.com/echoheart/react-theory/blob/master/src/App2.js)

```js
import React from "react";
import ReactDOM from 'react-dom';
const rootElement = document.getElementById('root');

let _state = null;

function myUseState(initValue) {
  _state = _state || initValue;
  console.log(initValue);
  function setState(newState) {
    _state = newState;
    console.log(newState);
    console.log('重新渲染')
    render()
  };
  return [_state, setState];
}

const render = () => ReactDOM.render(<App/>, rootElement)
function App() {
  console.log('App执行')
  const [n, setN] = myUseState(0);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
      </p>
    </div>
  );
}
```

这段代码对于一个useState可以适用, 那如果两个useState怎么办
由于所有的数据都放在_state, 所有必定会有问题
## 单个组件的多个useState
改进思路把_state做成一个对象, 例如`_state = {n: 0, m: 0}`但是仅仅通过useState这个方法我们无法确定出唯一确定的key
可以把_state做成数组, 看下面的代码
[react-theory/App3.js at master · echoheart/react-theory · GitHub](https://github.com/echoheart/react-theory/blob/master/src/App3.js)

```js
import React from "react";
import ReactDOM from 'react-dom';
const rootElement = document.getElementById('root');

let _state = [];
let index = 0;

function myUseState(initValue) {
  const currentIndex = index;
  _state[currentIndex] = _state[currentIndex] || initValue;

  // console.log(_state[index]);
  console.log(currentIndex, 'currentIndex')
  function setState(newState) {
    _state[currentIndex] = newState;
    console.log(newState);
    console.log('重新渲染')
    render()
  };
  index += 1;
  return [_state[currentIndex], setState];
}

const render = () => {
  index = 0;  //  很重要 必须重置为0
  ReactDOM.render(<App/>, rootElement)
}
function App() {
  console.log('App执行')
  const [n, setN] = myUseState(0);
  const [m, setM] = myUseState(0);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
      </p>
      <p>{m}</p>
      <p>
        <button onClick={() => setM(n + 1)}>+1</button>
      </p>
    </div>
  );
}
```

上面代码很重要的一点就是`render`函数中的`index = 0`, 因为每次重新渲染都会执行App函数, 所以就会执行两次`myUseState`, 如果不重置为0的话, index会一直的增加导致错误
还一个需要注意的点是`myUseState`当中的`currentIndex`, 因为`index`必须在调用`myUseState`之后增加1, 所以在增加1之前必须获取到增加之前的值, 可以理解为拿到一个快照

这个方案的缺点, 因为使用了数组作为`_state`, 所以`myUseState`的顺序是必须保证的, 不允许使用`if else`这类的条件判断语句当中出现`myUseState`
**这也真是React所要求的, 在使用useState时, 不能用在条件判断语句当中**

## 多个组件的useState
还有问题没有解决, 上面代码中只有一个App组件
 如果存在多个组件怎么办呢?
那就给每个组件都创建`_state`和`index`
那么如果重名了怎么办呢?
那就把组件渲染出来的React节点作为唯一表示, 可以理解创建一个React节点为key,`_state`为value的map


**总结**
- 每个组件函数都对应React节点
- 每个节点都保存这`state`和`index`
- `useState`读取`stata[index]`
- `index`由`useState`出现顺序决定
- `setState`会修改`state`, 并触发更新

以上总结并不是React真正的实现🤣, 但是也算是一种简化实现, 大体的逻辑原理是这样的
在正真的React实现中,其中React节点就是FiberNode, `_state`对应的是memorizeState, index是使用链表实现的

## n的分身

[react-theory/App4.js at master · echoheart/react-theory · GitHub](https://github.com/echoheart/react-theory/blob/master/src/App4.js)
```js
import React from "react";
import ReactDOM from 'react-dom';
const rootElement = document.getElementById('root');

function App() {
  const [n, setN] = React.useState(0);
  const log = () => setTimeout(() => console.log(`n: ${n}`), 2000);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
        <button onClick={log}>log</button>
      </p>
    </div>
  );
}
```

两种操作
- 点击+1再点击log  打印出来的是1  符合预期
- 点击log再点击+1 打印出来的是0   不符合预期

虽然都是2秒之后打印, 为什么第二种不符合预期呢

这个问题就类似于那个for循环6次, 每次用var以及setTimeout打印i的值得闭包问题
这个也有闭包的作用, 同过上面的分析可以发现
setN并没有改变原有的那个n
而是每次都生成一个新的n,覆盖上一次的n, 旧的n变量一般就被垃圾回收了
对于第二种情况, setTimout的回调其实引用的是上一次n, 所以由于闭包上一次旧的n无法被垃圾回收, 因为那个回调函数持有旧的n, 所以就同时存在两个n, 一个被setTimeout回调引用的n, 以及setN新生成的n, 这也就解释了为什么第二种情况不符合预期输出



