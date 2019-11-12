---
layout:     post
title:      javaScript读书笔记-数值(number)
subtitle:   javaScript读书笔记-数值(number)
date:       2018-09-15
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - JavaScript
---

# 整数和浮点数
在js中所有的数值都是以64浮点数形式存储的,所以1和1.0是相等的, 在js中其实根本没有整数所有的数字都是小数(64位浮点数),
如果某些运算必须要求是整数,这时js会将数值转换为32位整数后再进行运算


由于浮点数不是精确的,所以涉及到小数的运算要小心
```js
    0.1 === 0.3 - 0.2   //  false
    0.3 / 0.1   //  2.9999999999999996
    0.3 - 0.2 === 0.2 - 0.1 //  false
```

# 数值的范围
js中64位浮点数的64个二进制位是这样组成的

    1. 第1位->符号位 0正数 1负数->决定数值的正负
    2. 第2位到底12位(11位)->指数部分->决定数值的大小
    3. 第13位到第64位(52位)->小数部分->决定数值的精度

指数部分的最大值为2的11次方减1等于2048-1=2047,也就是说能表示的指数部分的最大值为2047,分出一半表示负数,则
js能表达的最大最小数值范围是2的1024次方到2的-1023次方,超出这个范围的无法表示

如果一个数值大于等于2的2014次方就发生了正向溢出,这时返回Infinity
如果一个数值小于等于2的-1075(指数部分的1023加上小数部分的52位)次方就发生了负向溢出,这时返回0

```js
    Math.pow(2, 1024) //    Infinity
    Math.pow(2, 1075) //    0
```
js的Number对象提供了两个能表示最大最小值得属性,返回js可以表示的最大值最小值

```js
    Number.MAX_VALUE // 1.7976931348623157e+308
    Number.MIN_VALUE // 5e-324
```
# 数值的进制
 js提供了四种进制的表示方法
 默认情况下js会将这些进制全部都转化为十进制
 
```js
    //  1.十进制-没有前导0
    //  2.八进制-0o或者0O开头或者有前导0并且符合八进制规则(只用到0-7之间的数字)
    //  3.十六进制-0x或者0X开头
    //  4.二进制-0b或者0B开头
    0Xff    //  255
    0O17    //  15
    0B11    //  3
```
如果在对应的进制里面出现了不属于改进制的数字就会报错

```js
    0O99    //  error
    0Xgg    //  error
    0B22    //  error
```
# 特殊数值
## 正零和负零
因为js内部存储的数值都是64位浮点数,第一位表示符号位,所以每个数值都有正负之分
 js内部是存在两个零的+0和-0,它们之间的区别就是64位浮点数的符号位不同
 
```js
    +0 === -0   //  true
    0 === +0    //  true
    0 === -0    //  true
```
他们之间唯一的区别就是如果当做分母的时候返回的值是不同的

```js
    1 / 0 === 1 / -0    //  false
    1 / 0 === -1 / -0   //  true
    //  等价于
    Infinity === -Infinity  //  false
    Infinity ====   Infinty    //  true
```

## NaN

not a nunber
主要的应用场景是字符串解析成数值出错的时候,并且NaN本质是一个数值类型
NaN 不等于任何值包括本身,NaN与任何值比较都为false
NaN转化为布尔值的时候为false
NaN和任何数值运算的时候也是NaN

```js
    typeof NaN //   number
    0 / 0 //    NaN
    NaN > 1 //  false
    NaN < 1 // false
    'x' + 1 //  NaN
    NaN + 1 // NaN
```

## Infinity
Infinity的应用场景:

    1.数值太大无法表示或者数值太小无法表示
    2.非0数值除以0得到Infinity
    
Infinity 有正负之分
Infinity 大于一切数值(不含NaN)
-Infinity 小于一切数值(不含NaN)
Infinity的四则运算符合数学当中的无穷计算规则
0乘以Infinity为NaN
0除以Infinity为0
Infinity乘以(除以/加上)Infinity都是Infinity
Infinity减去Infinity为NaN
Infinity与null计算时null会被转为0
Infinity与undefined的计算都是NaN

```js
    Math.pow(2, 1024)   //  Infinity
    1 / 0   //  Infinity
    Infinity === -Infinity  //  false
    Infinity > Number.MAX_VALUE //  ture
    -Infinity < Number.MIN_VALUE    //  ture
    Infinity + Inifinity === Infinity //    true
    Infinity / 5 === Infinity //    true

```

# 与数值相关的全局方法

## parseInt

parseInt用于将字符串转化为十进制整数
两个关键点->字符串,十进制整数,记住这两个关键点
如果被转换的不是字符串则会被转化为字符串
如果被转的头部尾部存在的空格或者换行符会被忽略

```js
    parseInt('  12 ')   //  12
    parseInt(12)    //  12
    //  等价于
    parseInt(String(12))    //  12
```


字符串转化为整数时是一个字符一个字符转换,如果遇到不能转换为数值的字符就停止转换,返回已经转化的部分
如果被转换的字符串开头存在负号则结果也会是负数,存在正好就会被忽略,因为默认就是整数了

```js
    parseInt('112.22')  //  112
    parseInt('-11') //  -11
```

如果被转换的数值(不是字符串)会被自动转换成科学计数法就会产生歧义

```js
    parseInt(1000000000000000000000)    //  1
    //等价于
    parseInt('1e+21') //  1
```

parseInt可以接受第二个参数(2-36),表示被解析的值的进制，返回该值对应的十进制数。
默认情况下，parseInt的第二个参数为10，即默认是十进制转十进制。
如果第二个参数不是数值，会被自动转为一个整数。这个整数只有在2到36之间，才能得到有意义的结果，超出这个范围，则返回NaN。
如果第二个参数是0、undefined和null，则直接忽略。

## parseFloat
和parseInt绝大部分的规则都适用,区别这个是转化为小数,遇到第一个非数值的字符或者第二个小数点停止转换

## isNaN
判断一个值是否是NaN,包括转换之后的值

```js
  isNaN(true) //  true
```

## isFinite
判断一个值是否是正常的数值
除了Infinity, -Infinity, NaN, undefined返回false其余的都是true
这个方法感觉用处不大

