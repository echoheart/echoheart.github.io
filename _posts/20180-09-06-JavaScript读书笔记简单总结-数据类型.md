---
layout:     post
title:      javaScript读书笔记-数据类型
subtitle:   javaScript读书笔记-数据类型
date:       2018-09-06
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - javaScript读书笔记
    - ES5
---
# 基本数据类型

``js分为七种数据类型,其中一种为ES6新增的数据类型,另外的六种分别是数值(number), 字符串(string), 布尔值(boolean), null, undefined, 对象(object),
其中数值,字符串,布尔值是原始类型,不能在细分了,是最基本的数据类型,对象可以看成是由多个基本类型组成的,null和undefined是两个特殊值
``

``对象包括是三种子类型:对象(狭义的对象),数组,函数
``

# typeof 运算符
``typeof 运算符在实际的开发中可以适当的使用,因为typeof从名字上来看好像是用来区分数据类型的一个方法,但是实际上大多数情况下都可以返回我们想要得到的类型,某些情况会不尽人意,这些情况需要单独记忆
至于使用方面我们在实际开发当中应该确定好当时情况选择使用
``
```
typeof 1 // number

typeof '1' // toString

typeof true //  boolean

function fn () {
}
typeof fn //    function 
//  按照上面基本类型所述此处似乎应该返回object,但是实际上却返回function,要单独记忆

typeof []   //  object

typeof {}  //   object

typeof undefined    //  undefined

typeof a    //  undefined
//此处a是未定义的变量, 未经声明就使用会报错,但是使用typeof 却不会报错,并且返回undefined

typeof null //  object
//  null使用typeof方法返回的是object, 因为js最开始是将null算为一个空的对象,所以返回object,也需要单独记忆

//  以上返回的都是字符串
```

# null 和 undefined

``null和undefined在平时的使用中大多数情况下都没有明显的区别(不包括明显的类型方面的检测),但是两者还是有一些区别,比如使用Number函数进行类型转换时,就可以看出差别,下面列出两者的异同点
``
```
var a = null;
var a = undefined;
//  两种方式不存在特别明显的差别

if(null){
    //  false
}
if(undefined){
    // false
}
//  在if当中转为布尔值的时候也没有区别都是false

//  但是下面的情况就存在差别
1 + null    //  1  null=> 0
1+ undefined    //   NaN  undefiend => NaN

Number(null)    //  0
Number(undefiend)   //  NaN
//  其实上面的情况,都是在null和undefiend转换为数值是null转为0, undefiend转为NaN

//  存在以上情况的的原因,我们这可以这样记忆,在js刚开始的时候 null表示空对象,表示没有值,和java一样,转为数值的时候是0, 
//  但是js的设计者觉得这样不好,他认为表示'没有值'最好不要是个对象,可能会产生歧义,所以就添加了一个undefiend来表示没有值
//  null表示空对象,转为数值的时候是0 
//  undefined 表示无定义的原始值, 转为数值的时候是NaN
//  只能死记,这是js语言限制
```

# 布尔值

``布尔值只有两种,要么真true,要么假false
``
```
//  可以产生布尔值的运算符
//  逻辑运算符
&& || !
//  相等运算符
=== !== == !=
//  比较运算符
< > >= <=
//  三目运算符的第一个

//  期待返回布尔值的地方 例如 if(...)

//  转换为布尔值为假的六个值
''  //  空字符串
false
0
null
undefiend
NaN //  这个容易被忽略,要多加记忆

// 除此以外的都为真包括 {} []
```

``睡觉
``