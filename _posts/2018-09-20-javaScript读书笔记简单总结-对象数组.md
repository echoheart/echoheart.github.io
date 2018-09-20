---
layout:     post
title:      javaScript读书笔记-字符串(string)
subtitle:   javaScript读书笔记-字符串(string)
date:       2018-09-17
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - javaScript读书笔记
    - ES5
---
# 对象
``对象就是一组无序的数据集合,键值对的数据集合,本次将对象和数组放在一起就是因为对象和数组有很多相同的地方,也可以将数组理解为一种特殊的对象
``

```
//  对象的键(key)其实就是字符串,ES6又新引入了Symbol当做对象唯一的键
var a = {
    foo: 1,
    bar: 2
}
//  等价于

var a = {
    'foo': 1,
    'bar': 2
}

// 如果键是数值,其实也是被转化为了字符串

var b = {
    1: 1
    2.00: 2
}
//  等价于
var b = {
    '1': 1,
    '2': 2
}
```

``如果对象的键是不合法的标识符(不符合标识符的命名规则)是会报错的
不符合条件的键都要将键写成字符串的形式
``
```
var a = {
    1p: 1
}
//  error
//  标识符是不可以数字开头的

//  如果想要1p当做键,可以换成字符串的写法

var a = {
    '1p': 1
}
```

``对象的每个键名又称为属性,对应的键值(value)又称为属性值,如果键值是函数名,那么该函数又被陈成为该对象的一个方法
可以被调用
``

``对象是引用类型,和原始类型不同的是一个变量表示引用类型或者原始类型所存储的数据是不同的
其中引用类型是存储的该引用类型的地址,而原始类型存储的就是原始类型的值
根本的原因是引用类型存储在堆中,而原始类型存储在内存中
``
```
var a = {
    foo: 1
}
var b = a;
b.bar = 2;
a.bar === 2 //  true;
//  此时a和b都是存储的对象的地址,所以同时指向同一个对象,改变的都是同一个对象

//  但是如果取消对对象的引用之后是不会影响原来的指向那个对象的那个变量

var a = {
    foo: 1
}
var b = a;
b = null;
a === null  //  false
```

``读取对象的属性有两种操作,点运算符和方括号运算符
``

```
var a = {
    foo: 1
}
a.foo   //  1
a['foo']    //  1
a[foo]  //  error
```
``一定要注意的是使用点运算符的时候foo被当做字符串
而使用方括号运算符的时候foo被当成了变量,所以如果不变成字符串的话,有可能会出错(变量未声明的时候)或者意想不到的结果,例如上面的例子,下面的例子证实此处所说
``
```
var bar = 'foo';
var a = {
    foo: 1,
    bar: 2
}
a[bar]  //  1
a['bar']    /   2
```

``方括号的内部还可以使用表达式
``
```
var a = {
    3: 3,
    'nihao': '你好'
}
a[1 + 2]    //  3
a['ni' + 'hao'] //  你好
//  此时并没有加上引号转化为字符串,因为数值会自动转化为字符串
```

``数值类型的键不可以使用点运算符,因为点会被当成小数点
此处可以想想数组是如何访问对应位置的值
``
```
var a = {
    1: 1
}
a.1 //  error
a[1]    //  1
```

``查看对象的所有属性可以使用Object.keys方法(Object构造函数上的静态方法)
此方法并不能得到对象的属性还是对象的属性
``
```
var a = {
    foo: 1,
    bar: 2,
    baz: 3
}
Object.keys(a)  //  ['foo', 'bar', 'baz']
```

``delete用于删除对象的属性
``
```
var a = {
    foo: 1
}
delete a.foo    //  true
Object,keys(a)  //  []
```

``如果删除一个对象不存在的属性是不会报错的,仍然返回true
``
``如果删除的是一个不可配置的属性是会报错的
``
```
var a = defineProperty({}, 'foo', {
    value: 1,
    configurable: false
})
a.foo   //  1
delete  a.foo   //  false
a.foo   //  1
```
``delete运算符只能删除对象本身的属性,不可以删除对象继承的属性
``
```
var a = {}
delete  a.toString  //  true
a.toString  //  native  fn
```

``in运算符用于检查对象是否包含某个属性,如果包含就返回true,否则返回false
``
```
var a = {
    foo: 1
}
'foo' in a  //  true
```
``in运算符不能分辨是否是继承的属性
``
```
var a = {}
'toString'  in a    //  true
```

``for in 用来遍历一个对象的全部属性
``
```
var a = {
    foo: 1,
    bar: 2,
    baz: 3
}
var item;
for (item in a) {
    console.log(item)
    console.log(a[item])
}
//  foo 1
//  bar 2
//  baz 3
```

``for in 的两个注意点
1,for in只会遍历可遍历的属性,会跳过不可遍历属性
2,for in也不能分辨对象的属性是本身的还是继承来的
``
``上面之所以没有把a对象继承的原型上的属性遍历出来是因为原型上的属性是不可遍历的
``
















