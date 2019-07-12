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

   ```tsx
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

   ```tsx
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

3. 为什么需要await

   它会使你的语法和结构更像是标准的同步函数...

   await必须返回一个Promise对象

   个人觉得其实await还是蛮丑的

   不如then方法, Promise写法还是蛮好看的

   但是对于大脑负担,await还是减少了大脑的思考🤔