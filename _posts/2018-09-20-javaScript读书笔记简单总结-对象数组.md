---
layout:     post
title:      javaScript读书笔记-对象数组
subtitle:   javaScript读书笔记-对象数组
date:       2018-09-17
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - JavaScript
---
# 对象
对象就是一组无序的数据集合,键值对的数据集合,本次将对象和数组放在一起就是因为对象和数组有很多相同的地方,
也可以将数组理解为一种特殊的对象


```js
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

如果对象的键是不合法的标识符(不符合标识符的命名规则)是会报错的
不符合条件的键都要将键写成字符串的形式

```js
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

对象的每个键名又称为属性,对应的键值(value)又称为属性值,如果键值是函数名,那么该函数又被陈成为该对象的一个方法
可以被调用

对象是引用类型,和原始类型不同的是一个变量表示引用类型或者原始类型所存储的数据是不同的
其中引用类型是存储的该引用类型的地址,而原始类型存储的就是原始类型的值
根本的原因是引用类型存储在堆中,而原始类型存储在内存中

```js
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

读取对象的属性有两种操作,点运算符和方括号运算符


```js
    var a = {
        foo: 1
    }
    a.foo   //  1
    a['foo']    //  1
    a[foo]  //  error
```

一定要注意的是使用点运算符的时候foo被当做字符串
而使用方括号运算符的时候foo被当成了变量,所以如果不变成字符串的话,
有可能会出错(变量未声明的时候)或者意想不到的结果,例如上面的例子,下面的例子证实此处所说

```js
    var bar = 'foo';
    var a = {
        foo: 1,
        bar: 2
    }
    a[bar]  //  1
    a['bar']    /   2
```

方括号的内部还可以使用表达式

```js
    var a = {
        3: 3,
        'nihao': '你好'
    }
    a[1 + 2]    //  3
    a['ni' + 'hao'] //  你好
    //  此时并没有加上引号转化为字符串,因为数值会自动转化为字符串
```

数值类型的键不可以使用点运算符,因为点会被当成小数点
此处可以想想数组是如何访问对应位置的值

```js
    var a = {
        1: 1
    }
    a.1 //  error
    a[1]    //  1
```

查看对象的所有属性可以使用Object.keys方法(Object构造函数上的静态方法)
此方法并不能得到对象的属性还是对象的属性

```js
    var a = {
        foo: 1,
        bar: 2,
        baz: 3
    }
    Object.keys(a)  //  ['foo', 'bar', 'baz']
```

delete用于删除对象的属性

```js
    var a = {
        foo: 1
    }
    delete a.foo    //  true
    Object,keys(a)  //  []
```

如果删除一个对象不存在的属性是不会报错的,仍然返回true

如果删除的是一个不可配置的属性是会报错的

```js
    var a = defineProperty({}, 'foo', {
        value: 1,
        configurable: false
    })
    a.foo   //  1
    delete  a.foo   //  false
    a.foo   //  1
```
delete运算符只能删除对象本身的属性,不可以删除对象继承的属性

```js
    var a = {}
    delete  a.toString  //  true
    a.toString  //  native  fn
```

in运算符用于检查对象是否包含某个属性,如果包含就返回true,否则返回false

```js
    var a = {
        foo: 1
    }
    'foo' in a  //  true
```
in运算符不能分辨是否是继承的属性

```js
    var a = {}
    'toString'  in a    //  true
```

for in 用来遍历一个对象的全部属性

```js
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

for in 的两个注意点

1. for in只会遍历可遍历的属性,会跳过不可遍历属性
2. for in也不能分辨对象的属性是本身的还是继承来的

上面之所以没有把a对象继承的原型上的属性遍历出来是因为原型上的属性是不可遍历的



# 数组
数组的本质其实就是对象,只不过数组当中的键值全部都是按照次序排列的整数

```js
    var a = [];
    typeof a    //  object
    var b = ['a', 'b', 'c'];
    Object.keys(b)  //  ['0', '1', '2']
```
js对象的键名一律都是字符串,数组本质是对象,所以数组的键名本质也是字符串,如果是按照数字来读取的话被转成了字符串

```js
    var a = ['foo', 'bar'];
    a['0']  //  foo
    a[0]    //  foo
    a[1]    //  bar
    a[1.00] //  bar
```
上面的几种写法都是被转成了字符串,所以都是可以读取到值得
一定要注意的一个问题是,我们现在读取的方法都是用的方括号,而不是点运算符,对于数字而言不能使用点运算符来读取值,这样存在语法错误
数组成员只能使用方括号来读取值

```js
    var a = [1, 2];
    a.0 //  error
```

数组的length属性使我们经常使用的一个属性,该属性是一个动态的值,length等于数组键值的最大整数加1,
数组的数字键值不一定是连续的,非连续的也可以,将数组的length设置为0,就直接清空了数组

```js
    var a = [];
    a[100] = 1;
    a.length    //  101
    a.length = 0;
    a   //  []
```
数组本质是对象,所以我们也可以像对象那样来给数组设置属性值,不一定非要是数字键值,如果是非数字键值,并不会影响到数组的length,
因为数组的length只是数组的键值最大数加1

```js
    var a = [1, 2];
    a.length    //  2
    a.foo = 'foo';
    a.length    //  2
```

in 运算符不仅仅适用于对象,也适用于数组

```js
    var a = ['a', 'b', 'c'];
    0 in a  //  true
    '0' in a    //  true
    100 in a    //  false
```

还需要特别明确的一点是:数组中的空位问题,不要将数组中的空位和数组的某一位置是undefiend弄混,
虽然空位取值的时候也是undefined,但这是两个概念,要理清空位(empty)和某一位虽然是undefined但是不是空位

```js
    var a = [];
    a[3] = 3;
    0 in a  //  false
    var b = [undefined, undefined, undefined, 3];
    0 in b  //  true
```

for in 对数组的遍历,for in 不经会遍历数字键还会遍历非数字键


```js
    var a = [1, 2];
    a.foo = 3;
    for (var key in a) {
        console.log(key)
    }
    //  0 1 foo
```

所以不推荐使用for in遍历数组,除非有特别的需求

数组的空位问题,数组的两个逗号之间没有任何值,那么这个位置就是空位(empty),但是数组最后的一个元素后面如果有一个逗号是没有任何影响的

```js
    var a = [1,,2];
    1 in a  //  false
```

如果使用delete删除数组的一个属性,会形成空位,但是不会影响length属性


```js
    var a = [1,2,3];
    delete a[2];
    2 in a  //  false
```

如果数组的某个位是空位的话,对数组的所有遍历操作都是会跳过这个空位的
对于空位的理解我们就理解为empty就好了,就是什么都没有


类数组是对象,但是类数组存在length属性,并且所有的键名都是正整数,
其实类数组的本质就是一个对象存在length属性,但是这个length不像真正的数组那样式动态的,
类数组不是数组,所以不可以使用数组特有的方法

可以使用Array.prototype.slice.call(arrayLike);

类数组是可以使用for循环来遍历的,也可以使用forEach,Array.prototype.forEach.call(arrayLike, function(){})

