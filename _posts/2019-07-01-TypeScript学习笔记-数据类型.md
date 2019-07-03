---
layout:     post
title:      TypeScript学习笔记-数据类型
subtitle:   TypeScript学习笔记-数据类型
date:       2019-07-01
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - TypeScript
---


# TypeScript学习笔记-数据类型

1. ts支持js的7种数据类型

   ```tsx
   const a: number = 1;
   const b: string = 'str';
   const c: boolean = true;
   const d: undefined = undefined;
   const e: null = null;
   const obj: object = {};
   const g: symbol = Symbol('key');
   ```

   其中所有类型的声明都是小写字母开头

   大写字母开头的可能会被ts认为是接口,而不是7种数据类型中的一种

   undefined和null可以作为其他类型的子类型存在

2. ts额外支持any, 枚举类型, void类型 Tuple元组类型 never类型额外五种

   ```tsx
   const a: any = 1;
   const a: any = 'any';

   function a(): void {
     // code
   }

   enum Gender {
     man,
     woman
   }
   const g: Gender = Gender.man;

   const x: [string, number] = ['str', 123];
   x = ['str1', 1234]; //	ok
   x = [1234, 'str1']	//	error
   ```

   任何类型都可以作为any类型的子类型

   void一般用于当函数没有返回值时作为函数的返回值类型

   enum枚举类型一般用于限制变量是几个值当中得一个值时采用的类型
   元组可以表示一个已知类型的数组

3. 类型断言

   ```tsx
   const a: number = 1;
   console.log((a as any).toString());

   const b: any = 2;
   console.log((<number>b).toString());
   ```

   类型断言是不允许两个具体类型之间的转换

   比如number转string  string装number 等类似的情况

   但是兼容的类型是可以互相转换的

   比如 any转number  string转any