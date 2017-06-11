---
layout: post
title: Day two
description: 快速入门
---
## 疑问
那我后端传给前端一个String a = 1
前端接收到是正数还是字符串？
如果是Python这种弱类型语言 传给 a = 1 呢？

### 基本数据类型 和 运算符  //这里的基本是指 “各个语言中都会有的” 的基本 与Java中的“基本数据类型”的概念完全不同

+ Number
123
0.123
1.234e2 //1.23*10^2 = 123 科学技术法
-123
NaN //表示这不是一个数字，无法计算结果的时候会出现NaN
Infinity //无穷大 ∞
0x000f //十六进制，15

+ 运算符

> + - * ／

+ 字符串

'hello' 或者 "hello"

+ boolean
true / false

+ 与或非
&& || !

+ 比较运算符
> >= == ===
> 区分 == 和 ===
> 使用 == 做比较会先自动转化类型再比较；
> 使用 === 比较不会自动转换类型，比如‘1’ === 1 会返回false

+ NaN
他不等于任何值包括他自己，判断相等只能使用isNaN(NaN)

+ 浮点数做相等判断
1/3 === （1 - 2/3） //false 原因与Java中的double float类型一样，此类运算不求精度，只求大概和速度。

+ null & undefined
null 表示空值，跟其他语言类似，比如python中是None
然而JavaScript多了一个undefined ，表示这个值未定义过，我见过的大部分时候是前端接受不到后端返回的数据时出现。

### 数组
[]包裹， “,”号隔开 || new Array({value1},{value2});

[1 , 2, '3', 'abc', '', null, false];
new Array(1, 2, '3', '', null, false);

### 对象

> Json

var user = {
id : 1 ,
name : somebody ,
age : 18 ,
mobile : 158xxxxyyyy
}

取对象属性跟Java类似: user.id

### 变量

+ 变量名：可以大小写字母、数字、$、_ ;不能以数字开通 ，不能是JavaScript的关键字，声明使用var

var a;
var a1 = 1;
var a2 = '2';
var a3 = false ;

+ 变量赋值
除变量的类型不固定之外，略。

+ strict模式

JavaScript在设计之初，为了方便初学者学习，并不强制要求用var申明变量。这个设计错误带来了严重的后果：如果一个变量没有通过var申明就被使用，那么该变量就自动被申明为全局变量

在JavaScript代码的第一行写上 'use strict'; 可以启用strict模式 ，这时未使用var 声明的变量会导致运行错误。

个人观点，基于“约定大于配置”的理念 ，还是小心使用var就好了，反正他的生命周期出不了所在的函数体。
