---
layout : life
title : Ruby Array
category : Basic of knowledge
duoshuo: true
date : 2015-02-12
---

>
#####数组(Array)是用来“将多个对象集合在一起的对象”中最基本而且最常用的东西。Ruby数组是无类型且可变的，数组里的元素不必都属于同一个类型，而且它们可以随时的改变。

******

###一、建立数组:
>
```sh
  num = [1,2,3,4,5]
  a = Array.new(5,1) ==> [1,1,1,1,1]
  lang = %w(Ruby,Perl,Java,C#) ==> ["Ruby","Perl","Java","C#"]
```

<!-- more -->

###二、从数组中删除数据:

>
#####(1)a.compact/a.compact!
#####从数组a中删除nill元素。“compact”会建立新的数组,而“compact!”会直接个i啊些改写原来的数组。compact!方法在删除nill以后会直接返回对象a；但什么都没有删除的时候会返回nill。
>
```sh
  a=[1,nil,2,nil,3]
  a.compact! => [1, 2, 3]
  b = [1,2,3]
  b.compact => [1,2,3]
  b.compact! => nil
```
>
#####(2)a.delete(x) 从数组a中删除元素X
>
```sh
  a = [1,2,3,4,2,5]
  a.delete(2) => [1,3,4,5]
```
>
#####(3)a.delete_at(n) 从数组a中删除索引n处的元素
>
```sh
  a = [1,2,3,2,1]
  a.delete_at(2) => [1,2,2,1]
```
>
#####(4)a.delete_if{|item|…… }
#####对数组a的每个元素(item)进行测试，如果区块的执行结果为真,则从a中删除item。(delete_if是破坏性的方法)
>
```sh
  a = [1,2,3,4,5]
  a.delete_if{|item| item>3}; p a => [1,2,3]
```
>
#####(5)a.slice!(n)/s.alice!(n..m)/s.alice!(n,m) 除去数组a指定的部分内容,并换成指定的值
>
```sh
  a = [1,2,3,4,5]
  a.slice(1,2) => [2,3]
  a.slice!(1,2); p a => [1,4,5]
  b = [1,2,3,4,5]
  b.slice!(2..4); p b => [1,2]
```
>
#####(6)a.uniq/a.uniq! 删除数组中重复的元素。uniq!是破坏性方法
>
```sh
  a = [1,2,3,4,3,2,1]
  a.uniq!
  p a => [1,2,3,4]
```
******

