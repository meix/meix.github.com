---
layout : life
title : kaminari Paginage
category : Ruby On Rails
duoshuo: true
date : 2015-03-11
---

##简介
>
Rails 3.x 以前最常用的分页插件是 will_paginate ，这个相信大家都耳熟能详。不过到 Rails 3 以后，有一个更好的选择，那就是 Kaminari;
选择它主要是看中其直观灵活的界面定制功能，它不像 will_paginate 那样需要配置一大堆参数，而是采用 view 文件来进行配置，非常好用。


###安装步骤
******

* 很简单，在 Gemfile 增加即可：

 ```sh
      gem "kaminari", '0.16.1'
      gem 'kaminari-bootstrap', '3.0.1'
 ```
<!-- more -->

* 装了插件以后，你可以在 model 对象上使用 .page() 和 .per() 这两个方法:

 ```
      User.page(1).per(10) {每页10个，取第1页数据}
 ```

 >
* PS：
 * 1.   配合查询条件语句，取第三页数据，默认每页为25个
 ```
      User.where(:status=>1).includes(:topics).page(3)
 ```
 * 2.   如果传给 .page() 方法的参数是 nil，则默认为1

 ```
     User.page(params[:page])
 ```


* 在View上，要显示分页的话，用它提供的 helper 方法即可:

 ```
      <%= paginate @users %>
 ```

******
******

* 在正在使用的后台管理中查询XXX结果使用Ajax显示页面,随之而来的问题是paginate的分页连接中所带的信息并不是我所需要的：

![页面样式](/res/img/blog/paginate.png)

 ```
      "http://localhost:3000/admin/XXXX/XXXX?token=%23%3CActionDispatch%3A%3AHttp%3A%3AUploadedFile%3A0x007fad60849030%3E&page=3&talk_id=2&utf8=%E2%9C%93&page=3&user_id=2"
 ```

 >
* 可以看出paginate params 所获取的信息并不是我想要的信息,一个比较Hacker的方法是重新改造params信息:
 * 1. 在XxxController中新增paginate_params方法:

 ```
      def paginate_params
        @paginate_params = { controller: "admin/xxxx",
          action: "xxxx",
          talk_id: params[:talk_id],
          authenticity_token: nil,
          user_id: nil,
          description: nil
        }
      end
 ```
 * 2. 在对应View李修改可以看出paginate:

 ```
     <%= paginate records, params: @paginate_params %>
 ```


* 现在下面的分页连接信息就是我们需要的:

 ```
     "http://localhost:3000/admin/XXXX/XXXX?page=3&talk_id=2"
 ```



