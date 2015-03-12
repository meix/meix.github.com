---
layout : life
title : Rails 使用 Ajax
category : Ruby On Rails
duoshuo: true
date : 2015-03-10
---

##简介
>
Ajax是Rails开发项目时候十分常用的方法。


###使用方法
******
* 例: 在文件夹 app/views/user_mailer/ 中新建文件 welcome_email.html.erb 这个视图是邮件的模板 。

 ```
        <script type="text/javascript">
          $.ajax({
            url: '/admin/xxxx/xxxx', {请求地址}
            type: 'POST',
            data:$(‘#logn_form').serialize(),  {当前的form表单params参数}
            success: function(result) {
              { 请求成功后执行的方法! }
            },
            error:function(response){
              var error_message = (response.status == 403) ? response.responseText : "程序处理异常，请稍后重试";
              alert(error_message);
            }
          });
        </script >
 ```
<!-- more -->

* 问题:
 * 1.在使用Ajax进行form_tag/form_for表单提交时,使用$("XX").serialize()方法将表单中数据POST到create方法,会与到图片无法无法提交。

 ```
      Parameters: {"utf8"=>"✓", "authenticity_token"=>"xX3IIJZlN71/GMZ1zJOj5SuQrWdEUmO0eO0Z+uNg9is=",
        "talk_reply_id"=>"xxx", "user_id"=>"xxx"}
 ```

* 解决方案:
**[原文链接:jQuery AJAX Form Submit Example][1]**

* **修改上述Ajax内容:**

 ```
      var formData = new FormData($("XXXXX")[0]);
      $.ajax({
        url: '/admin/XXXX/XXXXX',
        type: 'POST',
        contentType: false,
        cache: false,
        processData:false,
        mimeType:"multipart/form-data",
        data: formData,
        success: function(result) {
          { 请求成功后执行的方法! }
        },
        error:function(response){
          var error_message = (response.status == 403) ? response.responseText : "程序处理异常，请稍后重试";
          alert(error_message);
        }
      });
 ```

******
###表单提交成功后Parameters参数截图:
![发送邮件成功](/res/img/blog/params.png)
******

[1]:http://hayageek.com/jquery-ajax-form-submit/



