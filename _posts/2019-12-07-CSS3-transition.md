---
layout:     post
title:      CSS3变形-transition
subtitle:   CSS3变形-transition
date:       2019-12-07
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - CSS
---


# CSS3变形-transition

## transition
transition允许CSS的属性值在一定时间内平滑的由开始状态值过渡到结束状态值
可以通过CSS的伪类或者JS来触发

## 过渡创建步骤
- 在默认样式中声明元素的初始样式
- 在默认样式中添加过渡函数
- 声明过渡元素的最终状态

## transition属性值
`transition: transition-property transtion-duration transtion-timing-function transtion-delay`
- `transtion-property`指定需要过渡的属性
- `transition-duration`指定过渡需要的时间
- `transition-timing-function`过渡函数
- `transition-delay`过渡触发后延迟执行的时间


代码示例

github地址

[blog-source-code/index.html at master · echoheart/blog-source-code · GitHub](https://github.com/echoheart/blog-source-code/blob/master/src/css-transtion/index.html)

```html
<!DOCTYPE html>
<html lang=“en”>
<head>
    <meta charset=“UTF-8”>
    <meta name=“viewport” content=“width=device-width, initial-scale=1.0”>
    <meta http-equiv=“X-UA-Compatible” content=“ie=edge”>
    <title>transition</title>
    <style>
        .box {
            margin: 150px;
        }
        .item {
            height: 100px;
            width: 100px;
            background: gold;
            box-sizing: border-box;
            padding: 30px 0;
            text-align: center;
        }
        .one {
            
            
            font-size: 13px;
            transition: opacity .5s linear, background-color .5s linear, font-size .5s linear;
        }
        .one:hover {
            opacity: 0.8;
            font-size: 18px;
            background: green;
        }
        .two {
            transition: all .5s linear;
        }
        .two:hover {
            width: 200px;
            height: 200px;
        }
        .three {
            transition: all .5s linear;
        }
        .three:hover {
            transform: translateX(50px) rotate(360deg);
        }
        .stage {
            perspective: 300px;
            width: 100px;
            height: 100px;
        }
        .container {
            transform-style: preserve-3d;
            height: 100%;
            width: 100%;
        }
        .four {
            transition: all .5s linear;
        }
        .four:hover {
            transform: rotate3d(.1, .1, .1, 360deg);
        }
        .five {
            transition: all .5s linear;
        }
        .five.active {
            transform: translateX(100px) translateY(100px);
            opacity: 0.2;
        }
    </style>
</head>
<body>
    <div class=“box”>
        <div class="item one”>one</div>
    </div>
    <div class=“box">
        <div class=“item two”>two</div>
    </div>
    <div class="box">
        <div class="item three">three</div>
    </div>
    <div class="box">
        <div class="stage">
            <div class="container">
                <div class="item four">four</div>
            </div>
        </div>
    </div>
    <div class="box">
        <div class="item five">five</div>
    </div>

    <script>
        const el = document.querySelector('.five');
        let flag = true;
        el.onclick = (e) => {
            if (flag) {
                e.target.className = 'item five' + ' active';
                flag = false;
            } else {
                e.target.className = 'item five';
                flag = true;
            }
            
        }
    </script>
</body>
</html>
```




