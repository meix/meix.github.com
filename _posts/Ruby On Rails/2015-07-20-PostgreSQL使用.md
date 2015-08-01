---
layout : life
title : PostgreSQL使用
category : Ruby On Rails
duoshuo: true
date : 2015-07-20
---

##简介
>
PostgreSQL是一个功能强大，开源对象关系型数据库系统。它拥有超过15年的持续开发和业经验证的架构，具有良好的可靠性，数据完整性和正确性。
>
PostgreSQL能够运行在所有主流操作系统，包括Linux，UNIX（AIX，BSD，HP-UX，SGI IRIX，Mac OS X上的Solaris，Tru64的），和Windows。



**[详细教程:PostgreSQL教程][1]**

<!-- more -->

> ## 一、安装 PostgreSQL:
>
> 1. 首先，安装PostgreSQL客户端:

```sh
  sudo apt-get install postgresql-client
 ```
> 2. 然后，安装PostgreSQL服务器:

```sh
  sudo apt-get install postgresql
 ```

```sh
 正常情况下，安装完成后，PostgreSQL服务器会自动在本机的5432端口开启。
```

### 二、添加新用户和新数据库
>
>初次安装后，默认生成一个名为postgres的数据库和一个名为postgres的数据库用户。这里需要注意的是，同时还生成了一个名为postgres的Linux系统用户。
>添加新用户和新数据库，除了在PostgreSQL控制台内，还可以在shell命令行下完成。这是因为PostgreSQL提供了命令行程序createuser和createdb。

> _**首先，创建数据库用户 root(和mysql数据库保持一致)，并指定其为超级用户:**_
```
  $ sudo -u postgres createuser --superuser root
```

> _**然后，登录数据库控制台，设置 root 用户的密码，完成后退出控制台:**_

```sh
  $ sudo -u postgres psql
  $ \password root  (会让你输入数据库密码)
  $ \q
```

> _**接着，在shell命令行下，创建数据库binding(和当前项目保持一致，postgres无法使用自动化生成数据库)，并指定所有者为root。**_

> _**添加新用户和新数据库以后，就要以新用户的名义登录数据库，这时使用的是psql命令:**_

```sh
  $ psql -U root -d binding -h 127.0.0.1 -p 5432
```

>
![页面结果](/res/img/blog/ruby_on_rails/create_table.png)

```sh
  上面命令的参数含义如下：-U指定用户，-d指定数据库，-h指定服务器，-p指定端口。
   输入上面命令以后，系统会提示输入dbuser用户的密码。输入正确，就可以登录控制台了。
```

### 三、 在数据库中的一些命令：

```
  postgres=# \l 查看系统中现存的数据库

  postgres=# \q 退出客户端程序psql

  postgres=# \c 从一个数据库中转到另一个数据库中，如template1=# \c sales 从template1转到sales

  postgres=# \dt 查看表

  postgres=# \d 查看表结构

  postgres=# \di 查看索引
```

> _**[基本数据库操作]:**_

```sh

1. *创建数据库： create database [数据库名];

2. *查看数据库列表： \d

3. *删除数据库： . drop database [数据库名];

创建表： create table ([字段名1] [类型1] <references 关联表名(关联的字段名)>;,[字段名2] [类型2],......<,primary key (字段名m,字段名n,...)>;);

*查看表名列表： \d

*查看某个表的状况： \d [表名]

*重命名一个表： alter table [表名A] rename to [表名B];

*删除一个表： drop table [表名];

```

> _**[表内基本操作]**_

```sh
  *在已有的表里添加字段： alter table [表名] add column [字段名] [类型];

  *删除表中的字段： alter table [表名] drop column [字段名];

  *重命名一个字段： alter table [表名] rename column [字段名A] to [字段名B];

  *给一个字段设置缺省值： alter table [表名] alter column [字段名] set default [新的默认值];

  *去除缺省值： alter table [表名] alter column [字段名] drop default;

  在表中插入数据： insert into 表名 ([字段名m],[字段名n],......) values ([列m的值],[列n的值],......);

  修改表中的某行某列的数据： update [表名] set [目标字段名]=[目标值] where [该行特征];

  删除表中某行数据： delete from [表名] where [该行特征];

  delete from [表名];--删空整个表

```

### 四、Rails 中使用 PostgreSQL

* 安装好 PostgreSQL 后在rails中Gemfile中添加

 ```sh
  # PostgreSQL
  gem 'pg', '0.18.2'
 ```

> 执行 **bundle install** 来安装使用的gem
> 执行bundle install 时可能会遇到下面问题:
>
![页面结果](/res/img/blog/ruby_on_rails/pd_error.png)
> 解决方案:

 ```sh
  ~$ sudo apt-get install postgresql-client libpq5 libpq-dev
 ```
> 然后修改databases.yml

 ```sh
  default: &default
    adapter: postgresql
    host: 127.0.0.1
    pool: 5
    username: root
    password: xxxxx
    encoding: utf8

  development:
    <<: *default
    database: bidding
 ```

> 执行 **rake db:migrate** 来向数据库添加表
>
![页面结果](/res/img/blog/ruby_on_rails/postgresql.png)

> 最后为user表添加一条数据来测试数据库
>
![页面结果](/res/img/blog/ruby_on_rails/pos_user.png)


[1]:http://www.yiibai.com/plus/view.php?aid=34

