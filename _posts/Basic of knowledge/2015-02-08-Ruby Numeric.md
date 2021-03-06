---
layout : life
title : Ruby Numeric
category : Basic of knowledge
duoshuo: true
date : 2015-02-08
---

>
#####数值类有用来处理-1,0,1等正数的Integer类，也有用来处理0.1,3.1415等浮点数的Float类等。

******

###一、算术运算
>
#####数值对象除了有/与%运算符外，还有一些关于除法的方法:
>
#####x.divmod(y)。
>
#####以数组返回x除以y的商和余数,“商”是“x除以y”的结果往小的方向取最接近的整数;余数则是与y同号的值。余数的结果等于“x%y”。
>
```sh
  5.divmod(3) ==> [1,2]
  -5.divmod(3) ==> [-2,1]
  5.divmod(-3) ==> [-2,-1]
  -5.divmod(3) ==> [1,-2]
```

<!-- more -->

###二、数值类型的转换:

>
#####Float#to_i会去掉小数点以下的值。想要进行四舍五入，应该使用round方法：。
>
```sh
  1.2.to_i ==> 1
  1.2.round ==> 1
  1.8.to_i ==>1
  1.8.round ==> 2
```
>
```sh
  -2.0.abs  ==> 2.0
  -2.0 <=>0.0 ==> -1
```
>

###三、Float还定义了一些有用的方法和常量:
>
```sh
  -7/3
```
>
#####Ruby采取的是向负无穷大圆整，得到的结果为-3，按照Ruby对整数除法的定义，可以得到一个很重要的推论，即-a/b等于a/-b，但是可能不等于-(a/b)。
>

###四、Integer类除了可以用来计算数值外，还可以用来循环处理次数或数组的元素数）
```sh
  5.time{|i| puts i} ==> 1,2,3,4,5
```
>
#####(2) from.upto(to){|i|……} 从from到to为止不断相加，如果from大于to则一次也不数。
>
```sh
  ary = []
  2.upto(10){|i| ary<<i} ; p ary ==> [2,3,4,5,6,7,8,9,10]
```
>
#####(3) from.step(to, step){|i|……} 从from开始,每次加上step，直到数到to为止。
>
```sh
  ary = []
  2.step(10,3){|i| ary<<i }; p ary ==> [2,5,8]
```
******

