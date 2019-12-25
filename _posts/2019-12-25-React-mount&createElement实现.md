---
layout:     post
title:      实现React-createElement&mount
subtitle:   实现React-createElement&mount
date:       2019-12-25
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - React
---


# 实现React-createElement&mount

## 简介
本文介绍简单的React的createElement和mount实现
目的是帮助我自己或者阅读本文的人更好地理解React对于虚拟dom以及真实dom的理解

## DOM元素的构成
真实的dom元素主要有三个部分组成
- nodeType
- props
- children

nodeTye定义了dom的类型, props定义了dom元素上的属性值
children定义了dom元素内部的子元素

举个例子, 假设存在一个函数m

`m(‘a’, {href: ‘http:/www.baidu.com’}, ‘百度’)`
最终可以生成一个这样的真实dom
`<a href=“http://www.baidu.com">百度</a>`

其实这个例子的函数m就是createElement函数的功能

也就是将声明配置转化成真实dom

## mount(element->dom)

我们将ReactDOMElement定义成如下的形式

```
type ReactDOMElement = {
  type : string,
  props : {
    children : ReactNodeList,
    className : string,
    etc.
  }
};
```

其中props.children对应上面所说的children
最终可以通过mount函数将ReactElement转化为dom

`mount = ReactDOMElement => dom`

不难想出mount函数一定是一个递归的函数
因为ReactDOMElement和dom就是嵌套递归存在的

简易版的mount实现

```js
function mount(element) {
  const type = element.type;
  const props = element.props;
  let children = props.children;
  console.log(children)
  children = children.filter(Boolean);  //  过滤empty

  const node = document.createElement(type);
  Object.keys(props).forEach((propName) => {
    if (propName !== ‘children’) {
      node.setAttribute(propName, props[propName])
    }
  })

  //  递归渲染

  children.forEach((childElement) => {
    let childNode = null;
    if (typeof childElement === 'string') {
      childNode = mountText(childElement);
    } else {
      childNode = mount(childElement);
    }

    node.appendChild(childNode);
  })

  return node;
}

function mountText(text) {
  return document.createTextNode(text)
}

//测试
const elementOne = {
  type: ‘a’,
  props: {
    href: ‘http://www.baidu.com’,
    children: [{
      type: 'button',
      props: {
        class: ‘btn',
        children: ['this is a button’]
      },
    },
    ‘百度’]
  }
}
console.log(mount(elementOne));
document.body.appendChild(mount(elementOne));
```


## component(element->element)
对于React来说最重要的一部分就是组件化的思想, 几个组件可以组合出一个功能模块
组件也提供了element组合能力来构建更复杂的element
比如常用的`Form`组件一般都是包含了`Input`组件

举个例子
```js
function Button(props) {
  return {
    type: ‘button’,
    props: {
      class: ‘btn’,
      children: [props.text]
    }
  }
}

const element = {
  type: ‘a’,
  props: {
    href: ‘www.baidu.com’,
    children: [
      Button({text: ‘button one’}),
      Button({text: 'button two'}),
      '百度'
    ]
  }
}
```

最终这个element传给mount函数可以生成一个真实dom

这就是组件的组合构建出更复杂的组件

上面的形式也等价于这样的形式

```js
const reactComponetElement = {
  type: ‘a’,
  props: {
    href: ‘http://www.baidu.com’,
    children: [
      {
        type: Button,
        props: {
          text: ‘button one’
        }
      },
      {
        type: Button,
        props: {
          text: ‘button two’
        }
      },
      '百度'
    ]
  }
}

```
到此我们也可以得出一种新的类型

```js
type ReactComponentElement<TProps> = {
  type : ReactFunc<TProps>,
  props : TProps
};
```


## ReactElement
由上面的两种类型可以知道ReactElement大概由两种类型组成

```js
type ReactElement = ReactComponentElement | ReactDOMElement;
```

## 升级版的mount
可以看出mount函数需要处理三种情形
- ReactComponentElement
- ReactDOMELement
- ReactText

代码修改如下
```js
function mount(element) {
  if (typeof element === ‘string’) {
    return mountText(element);
  }
  if (typeof element.type === ‘function’) {
    return mountCompositeElment(element);
  }
  return mountDOMElement(element);
}

function mountDOMElement(element) {
  const type = element.type;
  const props = element.props;
  let children = props.children || [];
  console.log(children)
  children = children.filter(Boolean);  //  过滤empty

  const node = document.createElement(type);
  Object.keys(props).forEach((propName) => {
    if (propName !== 'children') {
      node.setAttribute(propName, props[propName])
    }
  })

  //  递归渲染
  children.length > 0 && children.forEach((childElement) => {
    let childNode = null;
    childNode = mount(childElement);
    node.appendChild(childNode);
  })

  return node;
}

function mountText(text) {
  return document.createTextNode(text)
}

function mountCompositeElment(element) {
  element = element.type(element.props);
  return mount(element);
}

//测试
const elementOne = {
  type: 'a',
  props: {
    href: ‘http://www.baidu.com’,
    children: [
      {
        type: Button,
        props: {
          text: ‘button one’,
        }
      },
      {
        type: Button,
        props: {
          text: ‘button two’,
        }
      },
      '百度'
    ]
  }
}
console.log(mount(elementOne));
document.body.appendChild(mount(elementOne));
```


## createElement
虽然children作为props的一部分
但是它是区别于其他的html属性, 所以可以提供多种形式

提供一个createElement函数统一处理

```js
function createElement(type, props, ...args) {
  const children = [].concat(…args);
  props.children = children;
  return {
    type,
    props
  }
}
```

使用示例
```js
function createElement(type, props, ...args) {
  const children = [].concat(…args);
  props.children = children;
  return {
    type,
    props
  }
}

const Element = createElement(
  ‘a’,
  {
    href: ‘www.baidu.com’
  },
  createElement(Button, {text: ‘button one'}),
  createElement(Button, {text: 'button two’}),
  ‘百度’
)
```


其实对应到平时常用的JSX来说原理是一样的, JSX不过是一层语法糖, 可以用使用babel转译一下

改写成JSX
```jsx
const Element = (
  <a href=“www.baidu.com”>
    <Button text=“button one”/>
    <Button text=“button two”/>
    百度
  </a>
)
```

## 完整的mount, createElement
```js
function Button(props) {
  return {
    type: ‘button’,
    props: {
      class: ‘btn’,
      children: [props.text]
    }
  }
}

function createElement(type, props, …args) {
  const children = [].concat(…args);
  props.children = children;
  return {
    type,
    props
  }
}

function mount(element) {
  if (typeof element === 'string') {
    return mountText(element);
  }
  if (typeof element.type === ‘function’) {
    return mountCompositeElment(element);
  }
  return mountDOMElement(element);
}

function mountDOMElement(element) {
  const type = element.type;
  const props = element.props;
  let children = props.children || [];
  console.log(children)
  children = children.filter(Boolean);  //  过滤empty

  const node = document.createElement(type);
  Object.keys(props).forEach((propName) => {
    if (propName !== 'children') {
      node.setAttribute(propName, props[propName])
    }
  })

  //  递归渲染
  children.length > 0 && children.forEach((childElement) => {
    let childNode = null;
    childNode = mount(childElement);
    node.appendChild(childNode);
  })

  return node;
}

function mountText(text) {
  return document.createTextNode(text)
}

function mountCompositeElment(element) {
  element = element.type(element.props);
  return mount(element);
}

const Element = createElement(
  ‘a’,
  {
    href: ‘www.baidu.com’
  },
  createElement(Button, {text: 'button one'}),
  createElement(Button, {text: 'button two'}),
  '百度'
)
// 对应的JSX形式
// const Element = (
//   <a href=“www.baidu.com”>
//     <Button text=“button one”/>
//     <Button text=“button two”/>
//     百度
//   </a>
// )
const node = mount(Element)
console.log(node);
document.body.appendChild(node);
```

**以上只是实现了简单的FunctionComponent**















