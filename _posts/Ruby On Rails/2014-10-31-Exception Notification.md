---
layout : life
title : Exception Notification 
category : Ruby On Rails
duoshuo: true
date : 2014-10-31
---
>
程序在生产环境下，随时有可能会遇到各种离奇的问题，导致运行时，系统抛出异常。但程序猿不能随时呆在电脑前，所以为了能够在发生异常时，及时处理，修复Bug。我们就需要对系统的运行情况进行监听;
**Rails 3.1 or greater------[Exception Notification][1]**

<!-- more -->

###搭建步骤
******

* Add the following line to your application's Gemfile:

 ```sh
        gem 'exception_notification'
 ```

* 然后终端运行命令：

 ```sh
        $ rails g exception_notification:install
 ```
 >
* PS：
 * 在生成的 config/initializers/exception_notification.rb 中配置自己需要接受的错误信息;

###设置Action Mailer使用 Gmail

* **最后针对 Gmail 的设置很简单，对到文件 config/environments/$RAILS_ENV.rb (不同环境)添加以下代码即可：**

 ```sh
        config.action_mailer.delivery_method = :smtp
        config.action_mailer.smtp_settings = {
          address:              'smtp.gmail.com',
          port:                 587,
          domain:               'example.com',
          user_name:            '<username>',
          password:             '<password>',
          authentication:       'plain',
          enable_starttls_auto: true  }
 ```

******
####错误信息邮件成功截图
![发送错误信息](/res/img/blog/error_email.png)
******


[1]:https://github.com/smartinez87/exception_notification
