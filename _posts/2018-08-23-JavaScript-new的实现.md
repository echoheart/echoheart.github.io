---
layout:     post
title:      javaScript-new的简单实现
subtitle:   new的实现
date:       2018-08-23
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - JavaScript原理
    - javaScript
---

#new的简单实现
``
我们都知道js是动态语言,也就是弱类型语言,简单了解过java的人都知道java中存在一个经常使用的new关键字操作符
在java中new的作用是为了将一个类实例化出对应的实例(对java了解的不多,就不多做讨论了,感兴趣的同学可以看看,可以在空闲时间再学习一门强类型语言)
js中的new大家一定也会经常使用,其实和java的大体意思是很相似的,是将构造函数实例化出一个对象(实例)
其实这个new实现中间的过程也是基于原型链来实现,只不过这个new帮我们省去了很多繁杂的过程,以后有时间我会写一篇有关于原型链的文章和大家探讨一下js的原型
下面我就简单的实现new的时候所发生的过程
``

``首先我先实现大体的过程,在逐步讲解
``
```
        function Person (name, age) {
            this.name = name;   //  每个人的名字
            this.age = age; //  每个人的年龄
            this.eye = '人类的共有属性eye'
            this.say = function () {
                console.log('人类共有的say方法')
            }
         const personOne = new Person('张三', 18);
         const personTwo = new Person('李四', 28);
         console.log(personOne);
         console.log(personTwo);
         // 大家可以复制这段代码打开chrome的控制台运行一下,看看结果
```
``其实这段代码非常简单,首先声明了一个Person的构造函数,其中name, age私有属性,say方法和eye属性为共有属性
中间发生了什么过程呢?下面我简单的实现一下
``
```
    function Person(name, age) {
        let temp = {}   //  创造一个临时对象,为了存储私有属性
        temp.__proto__ = Person.prototype;  //  绑定到Person构造函数的原型上
        temp.name = name;
        temp.age = age;
        return temp //  将这个临时对象返回
    }
    Person.prototype = {
        eye: '人类的共有属性eye',
        say: function () {
            console.log('人类共有的say方法')
        }
    }
 
        
```
``其实new大体也是分为以上关键的三个步骤
1,创建临时对象,也就是使用new的时候this所指代的对象
2,将这个临时对象绑定到构造函数的原型上,为了可以访问共有的属性或者共有方法
3,最后将这个临时对象返回
``

``
这个还有一个容易被忽略的细节,我们没有指定new之后可以找到自己爸爸的那个属性---constructor
下面我们完整的实现这个过程
``
```
    function Person(name, age) {
            let temp = {}   //  创造一个临时对象,为了存储私有属性
            temp.__proto__ = Person.prototype;  //  绑定到Person构造函数的原型上
            temp.name = name;
            temp.age = age;
            return temp //  将这个临时对象返回
        }
        Person.prototype = {
            constructor: Person,
            eye: '人类的共有属性eye',
            say: function () {
                console.log('人类共有的say方法')
            }
        }
```
``或者不使用上面的过程,改用下面的过程就不用指定constructor
``
```
    function Person(name, age) {
                let temp = {}   //  创造一个临时对象,为了存储私有属性
                temp.__proto__ = Person.prototype;  //  绑定到Person构造函数的原型上
                temp.name = name;
                temp.age = age;
                return temp //  将这个临时对象返回
            }
    Person.prototype.eye = '人类的共有属性eye';
    Person.prototype.say = function () {
                               console.log('人类共有的say方法')
                           }
```
``相信这个过程大家应该可以明白,因为对象是引用类型,变量存储的是对象的地址,第一种改写了对象的地址,其实就是完全换了一个对象,所以我们需要手动的加上丢失constructor
第二种还是原对象所以没有改变constructor,就不用care它了
``

``以上就是new之后大概发生的过程,睡觉了,明天还要搬砖😝
``