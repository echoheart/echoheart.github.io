---
layout:     post
title:      TypeScript学习笔记-类
subtitle:   TypeScript学习笔记-类
date:       2019-07-05
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - TypeScript
---

#  TypeScript学习笔记-类

    1. 类的声明
    
       类是高级版的接口
    
       接口是低级版的类
    
       类是对象生产的工厂
    
       在Java中对象只能通过类来产生
    
    2. constructor 是类的构造器 , 作用是为了类的初始化传递参数
    
    3. 子类继承父类时, 必须在子类的构造函数内部开始调用super,
    
       必须这样做,这样做相当于在调用父类的构造函数
    
    4. get set 拦截器
    
       在取值赋值前进行一次拦截
    
    5. 抽象类
    
       抽象类必须有抽象方法, 抽象类必须作为其他类的父类, 抽象类不能实现抽象方法
    
       继承抽象类的类必须实现抽象方法
    
