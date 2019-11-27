---
layout:     post
title:      React-Life-Cycle
subtitle:   React-Life-Cycle
date:       2019-11-28
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - React
---


# React-Life-Cycle


## constructor()构造方法
`constructor`是ES6类中的默认方法, 通过`new`关键字实例化出对象时会被自动调用, 该方法必须存在于类中, 如果类中没有实现, 默认会添加空的`constructor`,
当类中存在`constructor`存在时并且该类作为子类, 就必须调用`super`方法,
如果要在`constructor`方法中使用`this`就必须在使用之前调用`super`,

如果要在`constructor`中访问`this.props`就必须在`super`中传入props,
无论是否在`super`中传入props在其他的生命周期中`this.props`都是可以使用的, 这是React实现的

`constructor`一般作为初始化state的生命周期
在这里可以异步触发`setState`来触发更新

## componentWillMount组件挂载之前
在组件挂在之前调用, 全局只调用一次, 在这里可以`setState`, 不会触发重复渲染, 因为这个钩子是在`render`之前
## render 渲染组件
必须定义的生命周期, 用来渲染dom, 不能在这里`setState`, 必须返回reactDOM
## componentDidMount组件挂载完成之后
在组件挂载完成之后调用, 全局只调用一次, 这里可以`setState`, 可以获取真实dom, 不会触发重复渲染
## componentWillReceiveProps(nextProps)  props变化之前
`props`发生变化之后或者父组件重新渲染都会触发这个生命周期函数, 在这个函数中可以通过`nextProps`获取到变化之后`props`, 通过`this.props`获取到之前的`props`, 在这可以进行`setState`
## shouldComponentUpdate(nextProps, nextState)是否重新渲染
**组件挂载之后**, 在每一次调用`setState`都会调用这个钩子函数用来判断是否需要重新渲染
`forceUpdate`不会触发这个钩子函数
如果没写这个钩子函数默认是返回`true`
返回`false`则不触发渲染,
如果写了这个钩子函数, 就必须显示的指定返回`true`或者`false`否则报错可以使用这个钩子进行一些性能优化
## componentWillUpdate(nextProps, nextState)
当`shouldComponentUpdate`返回`true`或者调用了`forceUpdate`之后都会触发这个钩子函数,
不能在这里`setState`, 因为能走到这个钩子函数说明了`shouldComponentUpdate`返回了`true`, 这时下一个`state`状态已经确定, 马上就要执行`render`函数了, 否则可能会导致整个证明周期混乱
会触发重复渲染

## componentDidUpdate(prevProps, prevState, snapshot) 组件完成渲染
除了首次的`render`之外, 其他的`render`调用之后都会触发这个钩子函数的调用, 在这`setState`可能会触发重复渲染, 要自行判断
`prevProps`上一次的`props`
`prevState`上一次的`state`
`snapshot`是`getSnapshotBeforeUpdate`返回的值
## componentWillUnmount 组件即将被卸载
组价被卸载之前调用, 在这里删除一些注册的函数, 以及设置的计时器

## 生命周期图

![](https://www.echoheart.cn/img/react-life-cycle.jpg)

## 代码示例
```jsx
import React from "react”;

class LifeCycle extends React.Component {
  constructor(props) {
    super(props);
    this.state = { string: “自身状态” };
  }

  componentWillMount() {
    console.log(“componentWillMount”);
  }

  render() {
    console.log(“render");
    return (
      <div>
        <span><h2>{this.props.obj.number}</h2></span>
        <br />
        <span><h2>{this.state.string}</h2></span>
        <button onClick={() => {
          this.setState({
            string: ‘改变了自身状态'
          })
        }}>改变state</button>
      </div>
    );
  }

  componentDidMount() {
    console.log(“componentDidMount”);
  }

  componentWillReceiveProps(nextProps) {
    console.log(“componentWillReceiveProps");
    console.log(‘nextProps’, nextProps)
    console.log(‘thisProps’,this.props)
  }

  shouldComponentUpdate(nextProps, nextState) {
    console.log(“shouldComponentUpdate”);
    console.log('nextProps’,nextProps)
    console.log(‘thisProps’,this.props)
    console.log(‘nextState',nextState)
    console.log(‘thisState’,this.state)
    return true;
  }

  componentWillUpdate(nextProps, nextState) {
    console.log(“componentWillUpdate”);
    console.log(‘nextProps’,nextProps)
    console.log('thisProps’,this.props)
    console.log(‘nextState’,nextState)
    console.log(‘thisState’,this.state)
  }

  componentDidUpdate() {
    console.log("componentDidUpdate");
  }

  componentWillUnmount() {
    console.log("componentWillUnmount");
  }
  
}

export default LifeCycle;
```

**代码示例**

[react-theory/life.js at master · echoheart/react-theory · GitHub](https://github.com/echoheart/react-theory/blob/master/src/class-life-cycle/life.js)

# React16生命周期函数变化
## getDerivedStateFromProps(nextProps, prevState)

这一个静态方法, 所以不能在这个钩子当中使用`this`
也就是不能调用`setState`
这个函数的两个参数分别是新的`props`和当前的`state`对象
**这个钩子会返回一个新的`state`对象来更新当前的`state`对象, 如果不需要更新可以返回`null`**

这个钩子函数会在**组件首次挂载时, 接收到新的props时, 父组件重新渲染, 调用setState时, 和foreUpdate时**被调用
也就是说这个钩子函数在**首次渲染阶段**, 以及**更新阶段**都会被调用
这个钩子就是为了替代之前的`componentWillMount`,`componentWillReceiveProps`,`componentWillUpdate`钩子函数的
`nextProps`当前的`props`
`prevState`上一次的`props`

## getSnapshotBeforeUpdate(prevProps, prevState)
这个用来替代`componentWillUpdate`,
这个钩子在更新阶段的`render`之后, `componentDidUpdate`之前调用
`prevProps`上一次的`props`
			


`prevState`上一次的`state`


## react16生命周期图
![](https://www.echoheart.cn/img/react16-life-cycle.jpg)







