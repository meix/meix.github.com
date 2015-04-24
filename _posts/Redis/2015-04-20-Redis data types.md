---
layout : life
title : Redis data types
category : Redis
duoshuo: true
date : 2015-04-20
---

>
- _**Redis**_:_是一个由Salvatore Sanfilippo写的key-value存储系统。_

**[详细链接:][1]**

---------------------------------------

###1.2 Redis的优点
>
- _**性能极高 – Redis能支持超过 100K+ 每秒的读写频率**_
- _**丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作**_
- _**原子 – Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行**_
- _**丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性**_

---------------------------------------

<!-- more -->

---------------------------------------
> ## **2.1 String 类型**:
>
>Strings 数据结构是简单的key-value类型，value其实不仅是String，也可以是数字。
使用Strings类型，你可以完全实现目前 Memcached 的功能，并且效率更高。还可以享受Redis的定时持久化，操作日志及 Replication等功能。
除了提供与 Memcached 一样的get、set、incr、decr 等操作外，Redis还提供了下面一些操作:
>
- _**获取字符串长度**_
- _**往字符串append内容**_
- _**设置和获取字符串的某一段内容**_
- _**设置及获取字符串的某一位（bit）**_
- _**批量设置一系列字符串的内容**_

- _**Redis能存储二进制安全的字符串，最大长度为1GB**_

```sh
redis 127.0.0.1:6379> SET name "John Doe"
OK
redis 127.0.0.1:6379> GET name
"John Doe"
```

- _**String类型支持批量的读写操作**_

```sh
redis 127.0.0.1:6379> MSET age 30 sex "male"
OK
redis 127.0.0.1:6379> MGET age sex
1) "30"
2) "male"
```

- _**String类型其实也可以用来存储数字，并支持对数字的加减操作**_

```sh
redis 127.0.0.1:6379> INCR age
(integer) 31
redis 127.0.0.1:6379> INCRBY age 4
(integer) 35
redis 127.0.0.1:6379> GET age
"35"
redis 127.0.0.1:6379> DECR age
(integer) 34
redis 127.0.0.1:6379> DECRBY age 4
(integer) 30
redis 127.0.0.1:6379> GET age
"30"
```

- _**String类型还支持对其部分的修改和获取操作**_

```sh
redis 127.0.0.1:6379> APPEND name " Mr."
(integer) 12
redis 127.0.0.1:6379> GET name
"John Doe Mr."
redis 127.0.0.1:6379> STRLEN name
(integer) 12
redis 127.0.0.1:6379> SUBSTR name 0 3
"John"
```

> ## **2.2 List类型**:
>
>Redis能够将数据存储成一个链表，使用Lists结构，我们可以轻松地实现最新消息排行等功能。
Lists的另一个应用就是消息队列，可以利用Lists的PUSH操作，将任务存在Lists中，然后工作线程再用POP操作将任务取出进行执行。
Redis还提供了操作Lists中某一段的api，你可以直接查询，删除Lists中某一段的元素
>

```sh
redis 127.0.0.1:6379> LPUSH students "John Doe"
(integer) 1
redis 127.0.0.1:6379> LPUSH students "Captain Kirk"
(integer) 2
redis 127.0.0.1:6379> LPUSH students "Sheldon Cooper"
(integer) 3
redis 127.0.0.1:6379> LLEN students
(integer) 3
redis 127.0.0.1:6379> LRANGE students 0 2
1) "Sheldon Cooper"
2) "Captain Kirk"
3) "John Doe"
redis 127.0.0.1:6379> LPOP students
"Sheldon Cooper"
redis 127.0.0.1:6379> LLEN students
(integer) 2
redis 127.0.0.1:6379> LRANGE students 0 1
1) "Captain Kirk"
2) "John Doe"
redis 127.0.0.1:6379> LREM students 1 "John Doe"
(integer) 1
redis 127.0.0.1:6379> LLEN students
(integer) 1
redis 127.0.0.1:6379> LRANGE students 0 0
1) "Captain Kirk"
```

- _**Redis也支持很多修改操作**_

```sh
redis 127.0.0.1:6379> LINSERT students BEFORE "Captain Kirk" "Dexter Morgan"
(integer) 3
redis 127.0.0.1:6379> LRANGE students 0 2
1) "Dexter Morgan"
2) "Captain Kirk"
3) "John Doe"
redis 127.0.0.1:6379> LPUSH students "Peter Parker"
(integer) 4
redis 127.0.0.1:6379> LRANGE students 0 3
1) "Peter Parker"
2) "Dexter Morgan"
3) "Captain Kirk"
4) "John Doe"
redis 127.0.0.1:6379> LTRIM students 1 3
OK
redis 127.0.0.1:6379> LLEN students
(integer) 3
redis 127.0.0.1:6379> LRANGE students 0 2
1) "Dexter Morgan"
2) "Captain Kirk"
3) "John Doe"
redis 127.0.0.1:6379> LREM students 1 "John Doe"
(integer) 1
redis 127.0.0.1:6379> LLEN students
(integer) 1
redis 127.0.0.1:6379> LRANGE students 0 1
1) "Captain Kirk"
```

> ##**2.3 集合（Sets）类型**:
>
>Redis能够将一系列不重复的值存储成一个集合。
利用Redis提供的Sets数据结构，可以存储一些集合性的数据，比如在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。
Redis还为集合提供了求交集、并集、差集等操作，可以非常方便的实现如共同关注、共同喜好、二度好友等功能，对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。
>

- _**Redis能够将一系列不重复的值存储成一个集合**_

```sh
redis 127.0.0.1:6379> SADD birds crow
(integer) 1
redis 127.0.0.1:6379> SADD birds pigeon
(integer) 1
redis 127.0.0.1:6379> SADD birds bat
(integer) 1
redis 127.0.0.1:6379> SADD mammals dog
(integer) 1
redis 127.0.0.1:6379> SADD mammals cat
(integer) 1
redis 127.0.0.1:6379> SADD mammals bat
(integer) 1
redis 127.0.0.1:6379> SMEMBERS birds
1) "bat"
2) "crow"
3) "pigeon"
redis 127.0.0.1:6379> SMEMBERS mammals
1) "bat"
2) "cat"
3) "dog"
```

- _**Sets结构也支持相应的修改操作**_

```sh
redis 127.0.0.1:6379> SREM mammals cat
(integer) 1
redis 127.0.0.1:6379> SMEMBERS mammals
1) "bat"
2) "dog"
redis 127.0.0.1:6379> SADD mammals human
(integer) 1
redis 127.0.0.1:6379> SMEMBERS mammals
1) "bat"
2) "human"
3) "dog"
```

> ##**2.4 Hash 类型**:
>
>在Memcached中，我们经常将一些结构化的信息打包成hashmap，在客户端序列化后存储为一个字符串的值，比如用户的昵称、年龄、性别、积分等，这时候在需要修改其中某一项时，
通常需要将所有值取出反序列化后，修改某一项的值，再序列化存储回去。这样不仅增大了开销，也不适用于一些可能并发操作的场合（比如两个并发的操作都需要修改积分）。
而Redis的Hash结构可以使你像在数据库中Update一个属性一样只修改某一项属性值。
>

- _**Redis能够存储key对多个属性的数据（比如user1.uname user1.passwd)**_

```sh
redis 127.0.0.1:6379> HKEYS student
1) "name"
2) "age"
3) "sex"
redis 127.0.0.1:6379> HVALS student
1) "Ganesh"
2) "30"
3) "Male"
redis 127.0.0.1:6379> HGETALL student
1) "name"
2) "Ganesh"
3) "age"
4) "30"
5) "sex"
6) "Male"
redis 127.0.0.1:6379> HDEL student sex
(integer) 1
redis 127.0.0.1:6379> HGETALL student
1) "name"
2) "Ganesh"
3) "age"
4) "30"
```

- _**Hash数据结构能够批量修改和获取**_

```sh
redis 127.0.0.1:6379> HMSET kid name Akshi age 2 sex Female
OK
redis 127.0.0.1:6379> HMGET kid name age sex
1) "Akshi"
2) "2"
3) "Female"
```

> ##**2.4 Publish/Subscribe 类型**:
>
>Pub/Sub 从字面上理解就是发布（Publish）与订阅（Subscribe），在Redis中，你可以设定对某一个key值进行消息发布及消息订阅，
当一个key值上进行了消息发布后，所有订阅它的客户端都会收到相应的消息。这一功能最明显的用法就是用作实时消息系统，比如普通的即时聊天，群聊等功能。
>

- _**用一个客户端订阅管道**_

```sh
redis 127.0.0.1:6379> SUBSCRIBE channelone
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channelone"
3) (integer) 1
```

- _**另一个客户端往这个管道推送信息**_

```sh
redis 127.0.0.1:6379> PUBLISH channelone hello
(integer) 1
redis 127.0.0.1:6379> PUBLISH channelone world
(integer) 1
```

- _**然后第一个客户端就能获取到推送的信息**_

```sh
redis 127.0.0.1:6379> SUBSCRIBE channelone
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channelone"
3) (integer) 1
1) "message"
2) "channelone"
3) "hello"
1) "message"
2) "channelone"
3) "world"
```

---------------------------------------
---------------------------------------

###_3 Redis数据过期设置_
>
Redis支持按key设置过期时间，过期后值将被删除

- _**用TTL命令可以获取某个key值的过期时间（-1表示永不过期）**_

```sh
redis 127.0.0.1:6379> SET name "John Doe"
OK
redis 127.0.0.1:6379> TTL name
(integer) -1
```

- _**下面命令先用EXISTS命令查看key值是否存在，然后设置了5秒的过期时间**_

```sh
redis 127.0.0.1:6379> SET name "John Doe"
OK
redis 127.0.0.1:6379> EXISTS name
(integer) 1
redis 127.0.0.1:6379> EXPIRE name 5
(integer) 1
```

- _**5秒后再查看**_

```sh
redis 127.0.0.1:6379> EXISTS name
(integer) 0
redis 127.0.0.1:6379> GET name
(nil)
```

- _**上在是直接设置多少秒后过期，你也可以设置在某个时间点过期，下面例子是设置2011-09-24 00:40:00过期**_

```sh
redis 127.0.0.1:6379> SET name "John Doe"
OK
redis 127.0.0.1:6379> EXPIREAT name 1316805000
(integer) 1
redis 127.0.0.1:6379> EXISTS name
(integer) 0
```


---------------------------------------
---------------------------------------

###_4 Redis管理命令_
>

- _**Redis支持多个DB，默认是16个，你可以设置将数据存在哪一个DB中，不同DB间的数据具有隔离性。也可以在多个DB间移动数据。**_

```sh
redis 127.0.0.1:6379> SELECT 0
OK
redis 127.0.0.1:6379> SET name "John Doe"
OK
redis 127.0.0.1:6379> SELECT 1
OK
redis 127.0.0.1:6379[1]> GET name
(nil)
redis 127.0.0.1:6379[1]> SELECT 0
OK
redis 127.0.0.1:6379> MOVE name 1
(integer) 1
redis 127.0.0.1:6379> SELECT 1
OK
redis 127.0.0.1:6379[1]> GET name
"John Doe"
```

- _**Redis还能进行一些如下操作，获取一些运行信息**_

```sh
redis 127.0.0.1:6379[1]> DBSIZE
(integer) 1
redis 127.0.0.1:6379[1]> INFO
redis_version:2.2.13
redis_git_sha1:00000000
redis_git_dirty:0
arch_bits:64
multiplexing_api:kqueue
......
```

- _**Redis还支持对某个DB数据进行清除（当然清空所有数据的操作也是支持的）**_

```sh
redis 127.0.0.1:6379> SET name "John Doe"
OK
redis 127.0.0.1:6379> DBSIZE
(integer) 1
redis 127.0.0.1:6379> SELECT 1
OK
redis 127.0.0.1:6379[1]> SET name "Sheldon Cooper"
OK
redis 127.0.0.1:6379[1]> DBSIZE
(integer) 1
redis 127.0.0.1:6379[1]> SELECT 0
OK
redis 127.0.0.1:6379> FLUSHDB
OK
redis 127.0.0.1:6379> DBSIZE
(integer) 0
redis 127.0.0.1:6379> SELECT 1
OK
redis 127.0.0.1:6379[1]> DBSIZE
(integer) 1
redis 127.0.0.1:6379[1]> FLUSHALL
OK
redis 127.0.0.1:6379[1]> DBSIZE
(integer) 0
```



---------------------------------------
---------------------------------------

> **总结**
>![图片](/res/img/blog/redis/redis.png)



[1]:http://blog.nosqlfan.com/html/3139.html?ref=rediszt
