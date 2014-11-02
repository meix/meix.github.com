---
layout : life
title : Public and Private Method in Ruby
category : Basic of  knowledge
duoshuo: true
date : 2014-04-11
---
>
在Ruby的类别里，方法只要没有特别的限制预设就是**public**方法，除了一个例外：**initialize**方法，他永远是private。

<!-- more -->

###怎麼做?
******

 ```sh
        class Test

          def method_a
              puts "I am METHOD A in #{self.class}"
          end

          def method_b
              puts "I am METHOD B in #{self.class}"
          end

          protected
          def method_c
              puts "I am METHOD C in #{self.class}"
          end

          private
          def method_d
              puts "I am METHODr D in #{self.class}"
          end

        end
 ```

 >
* 把控制放在前面的这种写法，只要在它设定之后的方法定义都会受到影响，除非又遇到另一个存取控制设定。在上面的程序代码中，method_a，method_b没有特别的限制，所以是public方法，而method_c是protected方法，method_secret則属于private方法。
 * 让我们来看看终端运行结果：

 ```sh
        test = Test.new
        test.method_a        # I am METHOD A in Test
        test.method_b        # I am METHOD B in Test
        test.method_c        # NoMethodError
        test.method_secret   # NoMethodError
 ```


 **test是Test类的实例，而实例的public方法如我们预期那样输出结果，但protected跟private方法输出的确实错误“NoMethodError”，到底是怎么回事呢？**


###再让我们看下面两端代码：

* 定义一个Son类继承自Test类：

  ```sh
        class Son < Test

          def son_method_c
              method_c
          end

          def son_method_secret
              method_secret
          end

        end
 ```

* 然后给Son类添加两个方法，分别调用了Test的protected跟private方法，再來看程序：

  ```sh
        son = Son.new
        son.method_a           # I am METHOD A in Son
        son.method_b           # I am METHOD B in Son
        son.method_c           # NoMethodError
        son.method_secret      # NoMethodError
        son.son_method_c       # I am METHOD C in Son
        son.son_method_secret  # I am METHOD D in Son
 ```

>
**但你注意到了吗? 在子类里面可以直呼叫父类的private方法!**

* 先來看看这个例1：

  ```sh
       class A

          def method_a
              self.method_b
          end

          private
          def method_b
              puts "Hello, This is Private B!"
          end

        end

        the_a = A.new
        the_a.method_a       # private method `method_b' called for #<A:0x832778(NoMethodError)
 ```

 * 例2：

  ```sh
      class A

        def method_a
          method_b
        end

        private
        def method_b
            puts "Hello, This is Private B!"
        end

      end

      the_a = A.new
      the_a.method_a        #  Hello, This is Private B!
 ```

 >
这是为什么呢？因为在Ruby里的private方法，只要沒有明确的指出recevier(接受者)的话就都可以调用。所以在上面例子里的Son类，即使是调用父类的private方法，只要没有recevier，它就不会产生错误。比如在例(1)中A类里面的method_a方法调用类内部的method_b方法，看起来合理，但实际上出现“NoMethodError”的错误以为你在调用方法method_b的时候加上乐**self**，前面有提到“在调用Ruby的private方法时，不能明确的指定recevier，在这里确指出了self，所以出先错误信息了。沒错，连self也不行。”

******


