---
layout : life
title : Sorting Algorithm For Ruby
category : Basic of knowledge
duoshuo: true
date : 2014-06-12
---

> ##**(1) 插入排序**:
>
```sh
  def insert_sort(*test)
    (0...test.length).to_a.each do |i|
      key = test[i]
      j=i-1
      while j>=0 && test[j] < key
        test[j+1] = test[j]
        j-= 1
      end
      test[j+1] = key
    end
    puts test
  end
  insert_sort "6", "8", "2", "1", "5"  --------> 1,2,5,6,8
```

<!-- more -->

---------------------------------------
---------------------------------------
> ##**(2) 冒泡排序**:
>
```sh
  def bubble_sort(array)
    return array if array.length<2
    n=1
    while n<array.length
      (0..(array.length-2)).each do|i|
        array[i],array[i+1] = array[i+1],array[i] if array[i] >= array[i+1]
      end
      n+=1
    end
    puts array
  end
  bubble_sort [5,3,7,2,4,8,1,6] -------------> 1,2,3,4,5,6,7,8
```
---------------------------------------
---------------------------------------
> ##**(3) 完整代码**:
>
```sh
  class Array
    # 插入排序
    def insert_sort!
      (0...self.length).to_a.each do |j|
        key = self[j]
        i = j - 1;
        while i >= 0 and self[i] > key
          self[i+1] = self[i]
          i = i-1
        end
        self[i+1] = key
      end
      self
    end
    # 冒泡排序
    def bubble_sort!
      return self if self.length<2
      (self.length-1).downto(0)do|i|
        (0...i).each do|j|
          self[j],self[j+1] = self[j+1],self[j] if self[j] >= self[j+1]
        end
      end
      self
    end
  end
  shuffle_num = ((1..100).to_a).shuffle.sample(15)
  p shuffle_num.dup.send("insert_sort!")
  p shuffle_num.dup.send("bubble_sort!")
```
>
```sh
~ ruby algorithm.rb
[4, 6, 13, 32, 36, 43, 62, 63, 68, 83, 85, 91, 92, 98, 100]
[4, 6, 13, 32, 36, 43, 62, 63, 68, 83, 85, 91, 92, 98, 100]
```
**[更多链接:RubyChina ][1]**
---------------------------------------
---------------------------------------

[1]:https://ruby-china.org/topics/20569
