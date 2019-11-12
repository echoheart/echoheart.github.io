---
layout:     post
title:      Async-await学习笔记
subtitle:   Async-await学习笔记
date:       2019-07-12
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ES6
---
# async await学习笔记

1. await必须放在async函数里面(语法规定)

   ```typescript
       function fn() {
         return new Promise((resolve, reject) => {
           setTimeout(() => {
             let n = parseInt(String(Math.random() * 6 + 1), 10);
             // console.log(n,'#############');
             resolve(n);
             // console.log(n,'______________');
             // reject(n);
           }, 1000)
         })
       }
       // const result = fn();
       // result.then((m) => {
       //   console.log(`打印的数字是${m}`)
       // }, (e) => {
       //   console.log(`失败了?${e}`)
       // })
       
       async function test() {
         let n = await fn();
         console.log(n);
       }
       test();
   ```

2. 失败的处理 使用try catch捕获错误

   ```typescript
       function fn() {
         return new Promise((resolve, reject) => {
           setTimeout(() => {
             let n = parseInt(String(Math.random() * 6 + 1), 10);
             if (n >= 3) {
               resolve(n);
             } else {
               reject(n)
             }
           }, 1000)
         })
       }
       async function test() {
         try {
           let n = await fn();
            console.log(n);
         } catch(error) {
           console.log(error, '捕获到错误');
         }
       }
       test();
   ```

3. 同时两个异步可以用await处理吗

    ```typescript
        function fn() {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              let n = parseInt(String(Math.random() * 6 + 1), 10);
              if (n >= 3) {
                resolve(n);
              } else {
                reject(n)
              }
            }, 1000)
          })
        }
        Promise.all([fn(), fn()])
          .then((n) => {
            //	两个都成功执行这里
        }, (n) => {
          //	失败其一执行这里
        })
        //	这也是Promise的用法, 接受一个数组,数组的每一项都是一个Promise对象
    ```
    
    如果要想改写成await的形式, 需要记住一点await必须返回一个Promise对象
    
    ```typescript
        function fn() {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              let n = parseInt(String(Math.random() * 6 + 1), 10);
              if (n >= 3) {
                resolve(n);
              } else {
                reject(n)
              }
            }, 1000)
          })
        }
        async function test() {
          try {
            let n = await Promise.all([fn(), fn()]);
            console.log(n);
            //	如果两个异步函数都成功 n的结果是个数组
          } catch(error) {
            console.log(error, '捕获到错误');
          }
        }
        test();
    ```

4. 为什么需要await

   它会使你的语法和结构更像是标准的同步函数...

   await必须返回一个Promise对象

   个人觉得其实await还是蛮丑的

   不如then方法, Promise写法还是蛮好看的

   但是对于大脑负担,await还是减少了大脑的思考🤔

5. async函数的结果也是一个Promise对象
