---
layout:     post
title:      TypeScript学习笔记-泛型
subtitle:   TypeScript学习笔记-泛型
date:       2019-07-05
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - TypeScript
---

# TypeScript学习笔记泛型

    1. 泛型的常规使用方式

   ```typescript
        function fn<T> (a: T): T {
          return a;
        }
       
        let a = fn(true);
       
        function fn(a: any): any {
          return a;
        }
       
        let a = fn(true);
   ```

   不同于any, 泛型所代表的的类型更有代表性, 更有限制性

   所谓类型可以可理解为广泛的类型

    2. 泛型的复杂类型声明

   ```typescript
       let a: Array<number> = [1,2,3];
       let b: number[] = [1,2,3];
   ```

    3. 泛型和接口结合

   ```typescript
       interface X<T> {
         (a: T, b: t): T
       }
       let fn: X<number> = function (a, b) {
         return a + b
       }
   ```

    4. 泛型约束

   泛型约束表示对于泛型的限制, 如下面的例子类型T可能是没有length属性的

   但是如果假定T类型肯定是有length属性的

   从而去访问他的length属性,就可以用接口来约束类型T

   ```typescript
       interface hasLength {
         length: number;
       }
       function fn<T extends hasLength> (a: T) {
         console.log(a.length);
       }
       fn('str');
   ```

   


