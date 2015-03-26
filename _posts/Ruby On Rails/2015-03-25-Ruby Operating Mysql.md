---
layout : life
title : Ruby Operating Mysql
category : Ruby On Rails
duoshuo: true
date : 2015-03-25
---

##功能
>
使用Ruby连接Mysql数据库;
使用Ruby解析纯真IP库-qqwry.dat。

******

> ##  **ruby连接Mysql数据**:
>
> 创建一个xxx.ruby文件写入下面代码:
>
```sh
  require "mysql2"
  begin
    db = Mysql2::Client.new(
      host: "127.0.0.1",
      username: 'root',
      password: 'mysql',
      database: 'weixin_dev',
      )
    results = db.query('select * from users limit 3')  -----> (user:前三条记录)
    results.each do |user|
    p user
    end
    rescue Mysql2::Error=>e
      puts "Error code:#{e.errno}"
      puts "Error message:#{e.error}"
    ensure
    db.close if db
  end
```
> **运行结果**
>![图片](/res/img/blog/mysql_user.png)

---------------------------------------
---------------------------------------
> ##  **把IP转换为长整形**:
>
>使用**"8.8.8.9  8.8.8.255  美国 加利福尼亚州圣克拉拉县山景市谷歌公司"**进行测试
>
```sh
  def ip2long(ip)
    long = 0
    ip.split(/\./).each_with_index do |b, i|
      long += b.to_i << 8*(3-i)
    end
    long
  end
```
**执行文件结果:**

```sh
  ~ ruby ip2long.rb
  134744073
  134744319
  ~
```

---------------------------------------
---------------------------------------
> ## **打开下载的纯真IP库文件**:
>
> **[纯真IP库下载链接:][1]**
>
```sh
  def open_file(file)
    line_count = 0
    File.readlines(file).each_with_index do |value, index|
      line_count += 1
      if value.present?
        data = value.split(' ')
      end
      puts "finished, 查询到: #{line_count}记录"
    end
  end
  open_file "/Users/MeiX/testip.txt"
```
> **运行结果**
>![图片](/res/img/blog/open_file.png)

---------------------------------------
---------------------------------------
> ## **完整代码**:
>
> **(取其中的61条记录打印结果)**
>
```sh
class IpSearch
  def open_file(file='/Users/MeiX/testip.txt')
    line_count = 0
    total_count = 0
    File.readlines(file).each_with_index do |value, index|
      line_count += 1
      if value.length>1
        data = value.split(' ')
        id = index +1
        star_ip = data[0]
        end_ip = data[1]
        country = data[2]
        area = data[3]
        insert_star_ip = ip2long(star_ip)
        insert_end_ip = ip2long(end_ip)
        if saveScore(id,insert_star_ip,insert_end_ip,country,area)
          total_count+=1
        end
      end
      if line_count % 5 == 0
        puts "sleep:成功插入: #{total_count}条"
        sleep(1)
      end
    end
    puts "finished, total_count: #{total_count}"
    puts "end parse txt file."
  end
  def ip2long(ip)
    long = 0
    ip.split(/\./).each_with_index do |b, i|
      long += b.to_i << 8*(3-i)
    end
    long
  end
  def saveScore(id,insert_star_ip,insert_end_ip,country,area)
    begin
      db = Mysql2::Client.new(
        host: "127.0.0.1",
        username: 'root',
        password: 'mysql',
        database: 'weixin_dev',
        )
      db.query("INSERT INTO ip_adress (id,star_ip,end_ip,country,area) VALUES(#{id},'#{insert_star_ip}','#{insert_end_ip}','#{country}','#{area}')")
      results = db.query('select * from ip_adress')
      rescue Mysql2::Error=>e
        puts "Error code:#{e.errno}"
        puts "Error message:#{e.error}"
      ensure
      db.close if db
    end
  end
end
ip = IpSearch.new
ip.open_file
```

> **执行之前数据库**
![图片](/res/img/blog/old_ip_adress.png)

> **运行结果**
![图片](/res/img/blog/results.png)

> **执行之后数据库**
![图片](/res/img/blog/new_ip_adress.png)

<!-- more -->


[1]:http://www.cz88.net/