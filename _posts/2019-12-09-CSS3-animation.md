---
layout:     post
title:      CSS3-animation
subtitle:   CSS3-animation
date:       2019-12-09
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - CSS
---


# CSS3-animation


## 简介
虽然transition也可以实现一些属性的过渡动画效果, 但是transition的功能有限,  css3新增了一个animation, 可以通过关键帧的控制来制作一个像Flash样的动画

两者的主要区别是
- transition只能控制开始和结束的状态, 然后在两个状态之间进行平滑过度的方式来实现动画
- animation通过关键帧来声明一个动画, 在animation中调用关键帧声明的动画, 从而实现了一个更复杂的动画


## animation
animation总计有8个子属性
- animation-name 主要用来指定一个关键帧动画的名字, 这个名字必须对应一个keyframes规则, css加载的时候就会应用指定的这个名字, 从而执行动画
- animation-duration 主要用来设置动画播放的时间
- animation-timing-function 主要用来设置动画的播放方式 
- animation-delay 指定动画开始时间
- animation-iteration-count 指定动画播放循环的次数
- animation-direction 指定动画播放方向
- animation-play-state 用来控制动画的播放状态
- animation-fill-mode 用来设置动画的时间外属性

示例
```html
animation-name: pop;
animation-duration: 1s;
animation-timing-function: linear;
animation-iteration-count: infinite;
animation-direction: alternate;
animation-delay: 3s;
animation-fill-mode: backwards;
```
也可以简写成这样
`animation: pop 1s linear infinite alternate 3s backwards`

也可以将多个动画应用在一个元素之上
`animation: nameone 1s ease .5s backwards, nametwo 2s ease 1s backwards`

## @keyframes

CSSanimation制作动画主要包括两部分
- 首先用关键帧声明一个动画
- 然后是在animation调用关键帧声明的动画即可

@keyframes的主要作用就是声明一个阶段执行什么动作

声明格式
```html
@keyframes name {
	from {
		//	css样式
	}
	percentage {
		//	css样式
	}
	to {
		//	css样式
	}
}
```
上面的from和to相当于0% 100%其实也就是动画的开始和结束
中间的percentage可以是任意的百分比

## 触发动画
animation动画类似于transition动画, 都是随着时间改变元素的属性值
主要区别就是transition需要触发一个事件才会随着时间改变其css熟悉
animation不需要触发任何事件的情况下可以显示的随着时间变化来改变css属性值
其实也就是通过animation调用声明的@keyframes动画

值得一提的是:**如果未经特殊设置, 元素动画改变的属性值动画执行完毕之后会回到最初的默认状态**

### animation-fill-mode
animation-fill-mode主要有四个值: none forwards backwards both
默认值为none表示动画将按照预期进行和结束, 在动画最后一帧时, 动画会翻转到初始帧处
当取值为forwards时, 动画结束后会继续应用最后关键帧的位置
当取值为backwards时, 会在元素应用动画样式时迅速的应用动画的初始帧
当取值为both时, 动画同时具有forwards和backwards两种效果

**上面的简单理解就是**
**告诉动画在第一个关键帧等待动画的开始**
**或者在动画结束时的停在最后一帧而不回到第一帧上**
**或者同时具有这两个效果**

### animation-direction
animation-direction主要有两个值 normal alternate

normal每次循环都是向前播放
alternate动画播放为偶次则向前播放, 奇数次数则是反向播放
小球反复弹跳可以应用这个属性


github代码

[blog-source-code/index.html at master · echoheart/blog-source-code · GitHub](https://github.com/echoheart/blog-source-code/blob/master/src/css-animation/index.html)


