---
layout : life
title : Action Controller - 控制 HTTP 流程
category : Basic of knowledge
duoshuo: true
date : 2015-04-04
---

>
- _**HTTP通协议定是一种 Request-Response(请求-回应)的流程，客户端(通常是浏览器)向服务器发送出一個HTTP request封包，然後服务器就回应一個response封包。然后Rails使用路由來分派request到Controller的其中一个Action。而每個Action的任务就是根据客戶端传來的资料与Model互动，然后回应结果给客戶端。**_

**[详细链接:Ruby on Rails 實戰聖經][1]**

<!-- more -->

> ## **1.1 Render結果**:
> #### **在根据 request 资讯做好资料处理之后，我们接下來就要回传结果给用户。事实上，就算你什么都不处理，Action方法里面空空如也，甚至不定义Action，Rails预设也还是会执行 render 方法。這個 render 方法法回传预设的 Template，依照Rails惯例就是app/views/{controller_name}/{action_name}。如果找不到对应的模版的话，會出現Template is missing的错误。**
>
- _**当然，有時候我们会需要自定 render，也许是指定不同的 Template，也许是不需要 Template。这时候有以下参数可以使用：**_

- _**直接回传结果**_

```sh
  render :text => "Hello" 直接回传字串內容，不使用任何模板。
  render :xml => @event.to_xml 回传 XML 格式
  render :json => @event.to_json 回传 JSON 格式(再加上:callback就会是JSONP)
  render :nothing => true 空空如也
```

- _**指定Template**_

```sh
  :template 指定Template，这也是预设采用的参数，例如 render "/events/index.html.erb"
  :action 指定同一个 Controller 中另一个 Action的Template
  (注意到只是使用它的Template，而不会执行该Action內的程式)
```

- _**其他参数**_

```sh
  :status 设定HTTP status，预设是200，也就是正常。其他常用代码包括401权限不足、404找不到页面、500服务器错误等。
  :layout 可以指定这个Action的Layout，设成false即关掉Layout
  补充一下，在特定情況想把 render 的结果存成一个字串，例如拿到局部模板Partials成为一个字串，
  这时候可以改使用render_to_string :partial => "foobar"
```

> ## **1.2 Redirect**:
>
>如果Action不需要render任何结果，而是要使用者转向到別的页面，可以使用redirect_to
>

```sh
  redirect_to events_url
  redirect_to :back 回到上一頁。
  (注意，一個Action中只能有一個render或一個redirect_to。
  不然你會得到一個DoubleRenderError例外錯誤)
```

> ##**1.3 respond_to**:
>
>respond_to可以用來回应不同的质料样式。Rails內建支援格式包括有的:html, :text, :js, :css, :ics, xml, :rss, :atom, :yaml, :json等。如果需要扩充，可以编辑config/initializers/mime_types.rb这个文件。
>

- _**如果你想要设定一個else的情況，你可以用:any：**_

```sh
  respond_to do |format|
    format.html
    format.xml { render :xml => @event.to_xml }
    format.any { render :text => "WTF" }
  end
```

[1]:https://ihower.tw/rails4/actioncontroller.html
