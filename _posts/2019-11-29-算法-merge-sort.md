---
layout:     post
title:      算法-merge-sort
subtitle:   算法-merge-sort
date:       2019-11-29
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 算法
---


# 算法-merge-sort


归并排序是排序算法中比较简单的排序算法
归并排序的核心操作是两个有序数组的合并为一个有序数组

看一个最简单的两个数组合并
`[1]`和`[2]`合并成`[1, 2]`
`[4]`和`[3]`合并成`[3, 4]`

## 人类思维merge
使用循环来merge

```js

/**
 * merge
 * @param {array} a 
 * @param {array} b 
 */

 /**
  * a       b
  * [2, 5], [3, 4]
  */

const merge = (a, b) => {
  const result = [];
  let i = 0, j = 0;
  while(i < a.length || j < b.length) {
    if (i >= a.length) {
      result.push(b[j++]);
    } else if (j >= b.length) {
      result.push(a[i++])
    } else if (a[i] < b[j]) {
      result.push(a[i++])
    } else {
      result.push(b[j++])
    }
  }
  return result;
}
console.log(merge([2, 5, 7 ,10, 11], [3, 4, 5,6, 9]))
```

## 数学思维merge
使用递归来merge

```js

/**
 * merge
 * @param {array} a 
 * @param {array} b 
 */

 /**
  * a       b
  * [2, 5], [3, 4]
  */

const merge = (a, b) => {
  return a.length === 0 ? b :
        b.length === 0 ? a :
        a[0] < b[0] ? [a[0]].concat(merge(a.slice(1), b)) :
                      [b[0]].concat(merge(a, b.slice(1)))
}

console.log(merge([2, 5, 7 ,10, 11], [3, 4, 5,6, 9]))
```

以上就是merge函数的实现
对于上面两种做法的核心都是比较两个有序数组的第一个元素大小

## sort
实现sort
```js
const sort = (a) => {
  const l = a.length;
  if (l === 1) return a;
  const left = a.slice(0, parseInt(l/2));
  const right = a.slice(parseInt(l/2));
  return merge(sort(left), sort(right))
}
```

完整版
```js

/**
 * merge
 * @param {array} a 
 * @param {array} b 
 */

 /**
  * a       b
  * [2, 5], [3, 4]
  */

const merge = (a, b) => {
  return a.length === 0 ? b :
        b.length === 0 ? a :
        a[0] < b[0] ? [a[0]].concat(merge(a.slice(1), b)) :
                      [b[0]].concat(merge(a, b.slice(1)))
}

const sort = (a) => {
  const l = a.length;
  if (l === 1) return a;
  const left = a.slice(0, parseInt(l/2));
  const right = a.slice(parseInt(l/2));
  return merge(sort(left), sort(right))
}
console.log(sort([99,888,777,666,5,4,3,2,1,0]));
```

虽说sort这个函数叫做sort,
其实sort的作用是不停地将数组分割为更小的两个数组, 最终会把整个数组分割为长度为1的数组
然后交给merge函数不停地合并为有序数组
其实排序的功能是merge函数实现的

这个sort存在一些缺点
由于sort函数用到了slice, 为了分割数组复制了很多的小数组
递归过程中sort,merge函数重复调用很多次

## 优化merge函数, 改为原地merge
```js
const merge = (a, middle) => {
  let i = 0, j = middle;
  const end = a.length;
  while(i < middle && j < end) {
    let w = 0;
    if(a[i] <= a[j]) {i++}
    if(a[i] > a[j]) {j++;w++}
    const part = a.splice(j - w, w);
    a.splice(i, 0, ...part);
    i += w;
    middle += w;
  }
  return a;
}

console.log(merge([1, 5, 7 ,10, 11,  3, 4, 5,6, 9, 22], 5))
```

## 原地sort
```js
 const inplace_sort = (arr, start, end) => {
  if (end - start <= 1) return arr;
  const middle = parseInt((start + end) / 2);
  inplace_sort(arr, start, middle);
  inplace_sort(arr, middle, end);
  merge(arr, start, middle, end);
  return arr;
 }
```

适应原地sort的原地merge
```js

const merge = (a, start, middle, end) => {
  let i = start, j = middle;
  while(i < middle && j < end) {
    let w = 0;
    if(a[i] <= a[j]) {i++}
    if(a[i] > a[j]) {j++;w++}
    const part = a.splice(j - w, w);
    a.splice(i, 0, ...part);
    i += w;
    middle += w;
  }
  return a;
}
```

完整代码

```js
/**
 * 原地sort
 */

const inplace_sort = (arr, start, end) => {
  if (end - start <= 1) return arr;
  const middle = parseInt((start + end) / 2);
  inplace_sort(arr, start, middle);
  inplace_sort(arr, middle, end);
  return merge(arr, start, middle, end);
}
 /**
  * 改写merge适应原地sort
  */

const merge = (a, start, middle, end) => {
  let i = start, j = middle;
  while(i < middle && j < end) {
    let w = 0;
    if(a[i] <= a[j]) {i++}
    if(a[i] > a[j]) {j++;w++}
    const part = a.splice(j - w, w);
    a.splice(i, 0, ...part);
    i += w;
    middle += w;
  }
  return a;
}

 const sort = (arr) => inplace_sort(arr, 0, arr.length);
 console.log(sort([2, 5, 7 ,10, 11, 3, 4, 5,6, 9]));

```

**源码地址**

[math-blog-code/merge-sort at master · neo-to/math-blog-code · GitHub](https://github.com/neo-to/math-blog-code/tree/master/merge-sort)