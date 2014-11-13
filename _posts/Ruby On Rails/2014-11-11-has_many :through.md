---
layout : life
title : has_many :through
category : Ruby On Rails
duoshuo: true
date : 2014-11-11
---

###简介
>
has_many :through 关联经常用来建立两个模型之间的多对多关联。这种关联表示一个模型的实例可以借由第三个模型，拥有零个和多个另一个模型的实例。例如一个商城系统中的后台:每个商品(costume)有着许多的分类(品牌;版型;风格;袖长………)，而且互相之间是多对多的关系。

******
>
例如我的项目后台商品管理模块：(商品——版型)
版型表(clothing_types: 修身型 常规型 宽松型 直筒型 斗篷型 蛋糕型 高腰型 低腰型 紧身型 ),一件商品可能有多个版型,而版型又可以有不同的商品。

******

###搭建步骤

* 设计关联声明如下：

 ```sh
        $ rails g model costume
        $ rails g model clothing_type
        $ rails g model costumes_clothing_type
 ```
<!-- more -->

* 然后打开文件 app/model/xxxxxx.rb 中分别添加 :

 ```sh
        class Costume < ActiveRecord::Base
          has_many :costumes_clothing_types
          has_many :clothing_types, through: :costumes_clothing_types
        end

        class ClothingType < ActiveRecord::Base
          has_many :costumes_clothing_types
          has_many :costumes, through: :costumes_clothing_types
        end

        class CostumesClothingType < ActiveRecord::Base
          belongs_to :costume
          belongs_to :clothing_type
        end
 ```

* 然后进入app/views/admin/costumes/new.html.erb :
 * 1. 添加复选框(列出clothing_type表中字段)

    ![复选框信息](/res/img/blog/has_many/04.png)

* 进入终端可以看见(Started POST "/admin/costumes" )信息 :
    ![终端信息](/res/img/blog/has_many/01.png)
    ![终端信息](/res/img/blog/has_many/02.png)
    ![终端信息](/res/img/blog/has_many/03.png)

 >
* PS：使用Pry进行调试可以看见所传入的信息; 。

###修改model/costume.rb

* **从传入的params中抽出所需要的信息分别存入(costume;costumes_clothing_type)两张表：**

 ```sh
          def build!(params)
            record = Costume.new(params)
            Costume.transaction do
               record.save
               # binding.pry
               CostumesClothingType.build(record, params[:clothing_type_ids])
            end
            record
          end
 ```
 >
* PS：costumes_clothing_type表信息 :

![costumes_clothing_type表](/res/img/blog/has_many/05.png)

******