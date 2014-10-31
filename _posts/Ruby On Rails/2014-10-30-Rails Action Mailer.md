---
layout : life
title : Action Mailer
category : Ruby On Rails
duoshuo: true
date : 2014-10-30
---

##简介
>
Rails通过ActionMail组件来为rails程序提供邮件发送功能。例如：用户注册时成功后会发送一条短信通知用户注册成功。


###搭建步骤
******

* 创建邮件代码：

 ```sh
        $ rails generate mailer UserMailer
 ```
<!-- more -->

* 然后打开文件 app/mailers/user_mailer.rb 中添加 :

 ```sh
        class UserMailer < ActionMailer::Base
          default from: "ggmeixiang@gmail.com"
          def welcome_email(user)
            @user = user
            @url  = 'http://localhost:3000/sign_in'
            mail(to: @user.email,
               subject: 'Welcome to My Awesome Site',
               template_path: 'user_mailer/notifications',
               template_name: 'welcome_email')
          end
        end
 ```

 >
* PS：
 * 1. mail：用于发送邮件的方法，我们传入了 :to 和 :subject 邮件头。
 * 2. 此时，邮件程序会在文件夹 app/views/user_mailer/notifications中寻找名为welcome_email的视图 。

* 创建邮件程序的视图:
 * 1. 在文件夹 app/views/user_mailer/ 中新建文件 welcome_email.html.erb 这个视图是邮件的模板 。

 ```sh
        <!DOCTYPE html>
        <html>
          <head>
            <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
          </head>
          <body>
            <h1>恭喜, <%= @user.name %>注册成功</h1>
            <p>
              You have successfully signed up to example.com,
              your username is: <%= @user.email %>.<br>
            </p>
            <p>
              To login to the site, just follow this link: <%= @url %>.
            </p>
            <p>Thanks for joining and have a great day!</p>
          </body>
        </html>
 ```

* 修改用户模型:
 * 1. 然后需要编辑的是文件 app/controllers/users_controller.rb，修改 create 动作，成功保存用户后调用 UserMailer.welcome_email 方法，向刚注册的用户发送邮件：

 ```sh
                                              .
                                              .
                                              .
        if @user.save
          # Tell the UserMailer to send a welcome email after save
          UserMailer.welcome_email(@user).deliver
                                              .
                                              .
                                              .
        end
 ```

##设置Action Mailer使用 Gmail

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
###发送邮件成功截图
![发送邮件成功](/res/img/blog/email.png)
******




