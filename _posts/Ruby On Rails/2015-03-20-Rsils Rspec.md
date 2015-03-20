---
layout : life
title : Rsils Rspec
category : Ruby On Rails
duoshuo: true
date : 2015-03-20
---

##简介
>
RSpec 是一个 BDD 测试工具。**[原文链接:rspec/rspec-rails][1]**


安装步骤
-------------
******

* 很简单在Rails的配置文件Gemfile配置文件中,配置下面信息：

 ```sh
      group :development, :test do
        gem 'rspec-rails', '~> 3.0'
      end
 ```

> 我们没有必要单独的安装RSpec ,因为它是rspec-rails的依赖件会被自动安装.
> 执行 **bundle install** 来安装使用的gem
> 该命令执行完毕之后,会产生一个文件夹spec,该文件加下面有spec/spec_helper.rb 这个文件,spec_helper.rb用来设置测试的配置信息.

**[原文链接:RSpec Core 3.2][2]**

<!-- more -->

******
******

* 下面是spec的固定的规范,固定的格式:

 ```sh
      describe XXX do
        it XXX do
             ......
        end
      end
 ```
 ```sh
  包含了一个 describe 块以及其中的一个测试用例(sample), 以 it "..." do 开头的代码块就是一个用例.
 ```

> ## 首先是 **Controller** 测试:
>
> 1. 创建对应 spec/controller/admin/xxx_controller_spec.rb。
>
> 2. 给出一些例子代码：
>
```sh
  require 'spec_helper'
  module Admin
    RSpec.describe AlbumsController, :type => :controller do
      before(:each) do
        @record = create(:user)
      end
      describe "GET #index" do
        it "renders the index template" do
          get :index
          expect(response).to render_template("index")
        end
      end
        .
        .
        .
      describe "GET #show" do
        it "should album detail success" do
          get :show, id: @record.id
          expect(response).to be_success
        end
      end
        .
        .
        .
      describe "POST #create" do
        it "should create a new user" do
          expect {
            post :create, user: {id: 2, name: "xxxx", email: "xxxx"}
          }.to change(User, :count).by(1)
        end
        it "render the show template" do
          post :create, user: {id: 2, name: "xxxx", email: "xxxx"}
          expect(response).to render_template("show")
        end
      end
      describe "PATH #update" do
        it "should find the user and return the object" do
          put :update, { :id => @record.id, album: { name: "xxxxxx" } }
          @record.reload
          expect(@record.name).to eq("xxxxxx")
          expect(response).to render_template("show")
        end
      end
      describe "DELETE #destroy" do
        it "delete the user" do
          expect{
            delete :destroy, id: @record.id
          }.to change(User, :count).by(-1)
        end
        it "delete album to success" do
          delete :destroy, id: @record.id
          expect(response).to be_success
        end
      end
    end
  end
 ```

 **before用法:**

```sh
let 和 before(:each)的区别, let不会自动初始化变量,而before(:each)会自动初始化变量.如果我其中的
个测试用力不需要这些变量,依然需要初始化,如初始化变量需要很多时间,对这个测试的初始化就是浪费的时间和资源.
```
**[原文链接:Difference between let and before block][3]**
**[原文链接:When to use rspec let()?][4]**


> ## **Model** 测试:
>
> 1. 创建对应 spec/models/xxxx_spec.rb。
>
> 2. 下面以**Article**为例子的一些方法(跟新,列表,创建)代码：
>
```sh
  require 'rails_helper'
  RSpec.describe Article, :type => :model do
    before(:each) do
      @article = create(:article)
    end
    it ".refresh_article does update articles " do
      params = { title: "我是测试", description: "净化心灵的旅程"}
      @article.refresh_article(params)
      @article.reload
      @article.title.should == "我是测试"
    end
    it "list_articles should count articles" do
      Article.list_articles(Util::DateUtil.remove_timezone(Time.now.to_s)).count.should > 0
    end
    it "build_article should build articles" do
      expect {
        image_path = "#{Rails.root}/spec/data/top_banner.jpg"
        image = {filename: image_path, type: "image/jpg", tempfile: File.open(image_path)}
        article_params = { title: "不一样的世界", description: "净化心灵的旅程", image: image, article_type: "热门"}
        Article.build_article(article_params)
      }.to change(Article, :count).by(1)
    end
                      .
                      .
                      .
  end
 ```
[1]:https://github.com/rspec/rspec-rails
[2]:http://www.relishapp.com/rspec/rspec-core/v/3-2/docs
[3]:http://stackoverflow.com/questions/5974360/rspec-difference-between-let-and-before-block
[4]:http://stackoverflow.com/questions/5359558/when-to-use-rspec-let?lq=1

