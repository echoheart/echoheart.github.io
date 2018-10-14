---
layout:     post
title:      javaScript读书笔记Array对象
subtitle:   javaScript读书笔记Array对象
date:       2018-10-09
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - javaScript读书笔记
    - ES5
    - Array
---
# 构造函数
``Array是js原生的对象,同时也是一个构造函数,可以用来生成新的数组
``
```
var a = new Array(2)
a   //  [empty × 2]
//  不使用new的时候也是一样的
```
``Array构造函数的缺陷是传入的参数可能会造成歧义,传入一个值时该参数被当做数组的长度,而传入1个以上的参数的时候会被当做新数组的每一项
传入一个参数的时候,该参数只能是正整数,其余的类型会报错
``
```
var a = new Array(2)
a   //  [empty × 2]
var b = new Array(1, 2);
b   //  [1, 2]
```
``还需要注意的是如果参数是一个正整数,生成的新数组的每一位是空位
``
```
var a = new Array(3);
var b = [undefined, undefined, undefined];
0 in a  //  false
0 in b  //  true
```

# 数组的静态方法

``Array.isArray 返回一个布尔值来判断参数是否为数组, 该方法弥补了typeof 的不足
``
# 实例方法

``valueOf,
valueOf是所有对象都拥有的方法, 但是数组重写了该方法, 数组的valueOf返回数组本身
``
```
var a = [1, 2, 3];
a.valueOf() //  [1, 2, 3]
```
``toString,
toString也是对象通用的方法,调用数组的toString可以返回数组元素的字符串形式
``
```
var a = [1, 2, 3];
a.toString()    //  1,2,3
var b = [1,2,[3,4]];
b.toString()    //  1,2,3,4
```

``push,
push用于在数组的最后添加元素,改变了原数组
``
``pop,
pop用于将数组的最后一个元素移出,改变了原数组
``
``shift,
shift用于删除数组的第一个元素, 改变了原数组
``
``unshift,
unshift用于在数组的首位添加元素, 改变了原数组
``


``join,
join指定参数为分隔符,将数组的所有元素按照此分隔符链接最后返回字符串,如果不提供参数,默认按照逗号分隔
``
```
var a = [1,2,3];
a.join('-') //  1-2-3
a.join()    //  1,2,3
```
``如果数组的元素是null, undefined, 或者是空位,则会被转换为空字符串
``
```
var a = [null, undefined];
a.join('-') //  -
```
``这个方法也可以用于字符串或者类数组对象
``
```
var a = '123';
var b = {
    '0': 'a',
    '1': 'b',
    'length': 2
}
Array.prototype.join.call(a, '-');  //  1-2-3
Array.prototype.join.call(b, '-');  //  a-b
```

``concat,
concat用于对个数组的合并,然后返回新数组,并不会改变原数组
``
``reverse,
reverse用于将数组的元素倒序排列,然后返回数组, 该方法会改变原数组
``

``slice,
slice方法用于提取数组的一部分, 然后将提取的部分按照一个数组返回, 该方法并不会改变原数组
``

```
//  arr.slice(start, end)
//  start为数组起始索引(包括此位置), end为数组的结束索引(不包括此位置), 顾头不顾尾
var a = [1,2,3,4];
a.slice(0)  //  [1,2,3,4]
a.slice(0, 1)   //  [1]
a.slice()   //  [1,2,3,4]
```
``slice的重要应用是将类数组的对象转换为数组
``
```
var b = {
    '0': 'a',
    '1': 'b',
    'length': 2
}
Array.prototype.slice.call(b);  //  ['a', 'b']
```

``splice,
splice用于将数组的一部分删除,然后也可以在删除位置添加新的元素, 会改变原数组
``

```
var a = [1,2,3,4,5,6];
a.splice(0,2)   //  [1,2]
a   //  [3,4,5,6]
a.splice(0,1,1,2,3);
a   //  [1,2,3,4,5,6]
a.splice(0,0,0);
a   //  [0,1,2,3,4,5,6]
```

``sort,
sort对数组的元素进行排序, 排序后改变原数组
``

``map,
map方法将数组的所有的成员依次传入函数,然后把每一次的执行结果组成一个新数组返回,
map接受一个函数作为参数,该函数接受三个参数,当前数组元素,当前索引, 整个数组,map还可以接受第二个参数,第二个参数为第一个参数的执行环境也就是this
``
``forEach和map类似,只不过不返回新的数组
``

``filter用于过滤数组成员,返回满足条件的元素组成的新数组,使用方法和map一样
``

``需要注意的是以上的三个方法会跳过数组的空位,也就是在循环遍历的时候不会遍历数组的空位
``

``some,every
some是只要数组的所有元素有一个满足条件就返回true,
every是数组的所有元素必须都满足条件,就返回true否则返回false
``

``reduce,
reduce会一次处理数组的每一个元素,最终返回一个累计的值
``
```
var a = [1,2,3,4];
a.reduce(function (result,item,index,array) {
    return result + item;
})
```
``改方法也可以传入第二个参数,指定result的初始值
``
```
var a = [1,2,3,4];
a.reduce(function (result,item,index,array) {
    return result + item;
}, 10)
```

``indexOf,
indexOf返回给定参数在数组第一次出现的位置, 如果数组元素不包括给定参数则返回-1,
改方法不能用来搜索NaN,因为indexOf内部是使用严格相等运算符
``









