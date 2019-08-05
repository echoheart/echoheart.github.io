---
layout:     post
title:      函数式编程
subtitle:   函数式编程
date:       2019-08-05
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - React
    - 函数式
---

# 函数式编程(Functional Programming)

1. 函数式编程语言的鼻祖是Lisp语言

   简单了解一下Lisp基本语法

   ```tsx
   /**
   表达式
   */
   (+ 1 2)
   // => 3
   (+ 1 2 3)
   // => 5
   (+ (* 3 3) (* 4 4))
   // => 25
   
   /**
   命名
   */
   (define size 5)
   (* size 2)
   // => 10
   
   /**
   复合过程(函数)
   */
   
   (define (add a b) 
    				(+ a b))
   (add 1 2)
   // => 3
   (define (suq x)
   				(* x x))
   (suq 4)
   // => 16
   
   ```

2. 递归求值

   还是用Lisp语言举例,了解一下递归求解值得过程

   ```tsx
   (+ (+ 1 2) // v1
      (* 2 3) // v2
   )	//	v3
   ```

   以上求值的过程就是递归求值的过程

   想要求出最终的结果要经过两步

   - 求出操作符两边的值
   - 将第一步求出的值作用于操作符

   首先求出v1值为3, 其操作符两边的值为1 2 操作符为+

   ​			  v2值为6, 其操作符两边的值为2  3操作符为*

   递归上面的过程v3的求解过程为知道了v1 v2的值分别为 3 6,操作符为+. 所以最终值为9

3. 带入求值

   看下面代码

   ```tsx
   (define (squ x) (* x x))
   (define (squsum a b)
   				(+ (* a a)
           	 (* b b)))
   (define (f z)
   				(squsum z
   								(+ z 1)))
   ```

   上面f函数改写为数学表达式为

   f(z) = z^2 + (z + 1)^2

   假设z = 5 求(f 5)

   如果知道数学表达式那么只需要按照数学表达式计算出值即可

   但是如果不知道数学表达式, 就需要将z = 5一步一步的带入得出结果

   这个过程就是带入求值的过程

   带入求值最重要的一点是输入会确定输出

   但是并不是所有的(f 5)都会得到确定的输出, 必须满足一定条件才可以(下面会说要满足的条件)

4. 递归

   ```tsx
   (define (f n)
   	(if (= n 1)
		1
    		(* n (f (- n 1)))
   	)
   )
   ```
   
   Lisp求斐波那契
   
   带入求值
   
   先递进(展开)
   
   再回归(求值)
   
   逆向思维(倒着想)

5. 迭代

   ```tsx
   (define (fa n)
   	(iterator 1 1 n)
   )
   (define (iterator result n n-max)
     (if (> n n-max)
   		result
    		(iterator (* n result)
   							(+ n 1)
   							n-max
   		)
     )
   )
   ```

   迭代: 从一个状态到下一个状态(正向计算, 状态变量的数目固定)—>尾递归

   

   在代码层面上看着都是自身调用自身但是, 自身调用自身不一定是递归

6. 高阶函数

   函数作为参数

   或者返回一个函数就是高阶函数

   高阶函数在函数式中是为了作为函数的抽象

7. 函数式推荐书籍—> SICP

8. 函数存储状态

   ```tsx
   (define money 100)
   (define (take n)
     (set! money (- money n))
     money
   )
   (take 25)
   // 75
   (take 25)
   // 50
   ```

   define 定义money为100

   set是赋值

   将money改为局部变量

   ```tsx
   (define taker
     (
       let (money 100)
       (
         lambda (n)
         (set! money (- money n))
         money
       )
     )
   )
   (taker 25)
   // 75
   (taker 25)
   // 50
   ```

   转为JS

   ```tsx
   const taker = function () {
     let money = 100;
     return (n) => {
       money = money - n
       return money
     }
   }()
   ```

   改为消息传递

   ```tsx
   const makeAccount = (money) => {
     const take = (n) => {
       money -= n;
       return money;
     }
     const save = (n) => {
       monry += n;
       return money;
     }
     const dispatch = (m) => {
       retrun(
         m === 'take' ? take :
         m === 'save' ? save :
         new Error('error')
       )
     }
   }
   ```

9. 函数式通俗理解

   不使用赋值就是函数式

   带入求值是数学的基本概念

   我们都知道f(x) = y 固定x返回得到固定y

   如果出现赋值就会出现上面存钱取钱的问题, 相同的函数得到不同的结果

   赋值导致代入法不能使用, 所以就不是函数式

10. 不使用赋值编码例子(Paper组件)

    ```tsx
    function Pager(props) {
      const buttons = Array.apply(null, { length: props.total })
        .map((item, index) => {
          return index + 1;
        })
        .filter((item, index) => {
          if (item === 1) {
            return true;
          } else if(item === props.total) {
            return true;
          } else if (Math.abs(item - props.current) <= 2) {
            return true;
          }
          return false;
        })
        .reduce((prev, current) => {
          const last = prev[prev.length - 1] || current;
          return prev.concat(current - last > 1 ? [-1, current] : [current]);
        }, [])
        .map((item, index) => {
          (item === -1 ?
                     <span>...</span>
                       :
                       <button>{item}</button>)
        })
    
        return (
          <div>
            {buttons}
          </div>
        )
    }
    ```

11. 函数式优缺点及特点

    - 函数编程都是垃圾(大量中间无用变量GC)

    - 不是有等于号就是赋值, 第一次的等于号是定义, 第二次是赋值

    - 以let n = 1为例, 如果没有赋值 只是给1取个名字叫n, 如果存在赋值 n更像是个容器 可以保存不同的值

    - 广泛采用赋值的程序设计叫做 命令式/指令式程序设计

    - 赋值带来的问题很多, 命令式遇到克隆很让人头疼

      ```tsx
      var a = {name: 'a'}
      var b = a;
      b.name = 'b'
      ```

      这个赋值过程就会导致克隆的对象是共享一个数据源的问题

    - 函数式强调不可变对象

    - 函数式编程还是会存在赋值的,在改变外界状态时还是会赋值,这样的赋值操作需要单独写在专门处理副作用的函数当中

    - 函数式特点

      - 可证明公理化

      - 更加强调执行结果而不是过程

        ```tsx
        // 斐波那切数列
        // 函数式
        f(n) n = 1 : 1
             n > 1 : f(n -1) * n
          
        // 过程式
        result
        for(i ~ n) {
        	result *= i
        }
        ```

      - 函数是一等公民(函数可以作为参数), 高阶函数
      - 纯函数, 拒绝副作用(赋值就是副作用)
      - 不可变数据(不可以赋值)
      - 数据即代码
      - 引用透明

12. map&forEach&filter&reduce区别和联系

    forEach模拟实现

    ```tsx
    Array.prototype._forEach = function (fn) {
      for(let i = 0;i < this.length; i++) {
        if (i in this) {	//	判断不存在的项
          fn.call(undefined, this[i], i, this);
        }
      }
    }
    ```

    for和forEach区别

    forEach用到了函数, 每次循环都是生成新的作用域

    forEach无法break

    

    map模拟实现

    ```tsx
    Array.prototype._map = function (fn) {
      const result = [];
      for(let i = 0;i < this.length; i++) {
        if (i in this) {
          result[i] = fn.call(undefined, this[i], i, this);
        }
      }
      return result;
    }
    ```

    filter模拟实现

    ```tsx
    Array.prototype._filter = function (fn) {
      const result = [];
      let temp = false;
      for(let i = 0;i < this.length; i++) {
        if (i in this) {
          if (temp = fn.call(undefined, this[i], i, this)) {
            result.push(temp);
          }
        }
      }
      return result;
    }
    ```

    reduce模拟实现

    ```tsx
    Array.prototype._reduce = function (fn, init) {
      let result = init;
      for(let i = 0;i < this.length; i++) {
        if (i in this) {
          result = fn.call(undefined, result, this[i], i, this);
        }
      }
      return result;
    }
    ```

    reduce表示map

    ```tsx
    arr1 = array.map((v) =>v + 1);
    
    arr2 = array.reduce((result, item) => {
      result.puhs(item + 1);
      return result;
    }, []);
    ```

    reduce表示filter

    ```tsx
    arr1 = array.filter((v) => v % 2 === 0);
    
    arr2 = array.reduce((result, item) => {
      if(item % 2 === 0) {
        result.push(item);
      }
      return result;
    }, [])
    ```

    数组操作一定要学会reduce

    redux和reduce之间的联系


