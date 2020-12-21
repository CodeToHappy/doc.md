![Redis脑图](https://gitee.com/lemonsoldout/images/raw/master/Redis.jpg)

[TOC]

## 网络来源
[网络来源 1](https://www.jianshu.com/p/6cde8a5d1095)

[网络来源 2](https://blog.csdn.net/qq_27588415/article/details/107708826?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control)

[网络来源 3](www.runoob.com/redis)

[网络来源 4 ^](http://doc.redisfans.com/index.html)

## 简介
> REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。 
>
> Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。 
>
> 它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Hash), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

- Redis 是完全开源的，遵守 BSD 协议，是一个高性能的 key-value 数据库。
- Redis 与其他 key - value 缓存产品有以下三个特点：
	1. Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
	2. Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
	3. Redis支持数据的备份，即master-slave模式的数据备份。

### Redis 优势
1. 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
2. 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
3. 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

### Redis 与其他 key-value 存储有什么不同？
- Redis有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。Redis的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
- Redis运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，因为数据量不能大于硬件内存。在内存数据库方面的另一个优点是，相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样Redis可以做很多内部复杂性很强的事情。同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。

## 下载与安装
- 因为懒，发现一个宝藏网页[Try Redis!](https://try.redis.io/)

## 基本数据类型
### 1. String 字符串
#### 简介
- string 是 redis 最基本的类型，你可以理解成与 Memcached 一模一样的类型，一个 key 对应一个 value。
- string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。
- string 类型是 Redis 最基本的数据类型，string 类型的值最大能存储 512MB。 

#### 简单尝试
- 所有 Redis 命令基于这条进行运作 `redis ip_addr:port> command key_value`
```redis
> SET myKey "Hello World"
OK
> GET myKey
"Hello World"
```
- 我发现即使调用`set`命令输入的字符串没有被“”引号环绕，Redis `get` 命令返回的时候，仍然返回带有“”引号的字符串。
- 注意：一个键最大能存储 512MB。

### 2. List 列表
- Redis 列表是简单的字符串列表，按照插入顺序排序。
- 可以添加一个元素到列表的头部（左边）或者尾部（右边）。
```redis
> DEL key
(integer) 1

> lpush key hi
(integer) 1
> lpush key hello
(integer) 2
> lpush key halo
(integer) 3
```
列表最多可存储 232 - 1 元素 (4294967295, 每个列表可存储40多亿)。

### 3. Hash 哈希
- Redis hash 是一个键值(key=>value)对集合。
- Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。
由于刚刚将`myKey`与`"Hello World"`对应，如果使用`HMSET myKey field1 "Hello" field2 "World"`就会报错
```redis
(error) WRONGTYPE Operation against a key holding the wrong kind of value
```
- 使用`DEL myKey`进行删除，即可重新使用`myKey`键
```redis
> DEL myKey
(integer) 1
> HMSET key field1 "Hello" field2 "World"
OK
> HGET key field1
"Hello"
> HGET key field2
"World"
```
- 实例中我们使用了 Redis HMSET, HGET 命令，HMSET 设置了两个 field=>value 对, HGET 获取对应 field 对应的 value。
- 每个 hash 可以存储 2^32 -1 键值对（40多亿）。

### 4. Set 集合
- Redis 的 Set 是 string 类型的无序集合。
- 集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

#### sadd 命令
- 添加一个 string 元素到 key 对应的 set 集合中，成功返回 1，如果元素已经在集合中返回 0。
```redis
sadd key member
```
```redis
> sadd key hello
(integer) 1
> sadd key hi
(integer) 1
> sadd key halo
(integer) 1
> sadd key halo
(integer) 0
```
- 以上实例中 `halo` 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。
- 集合中最大的成员数为 2^32 - 1(4294967295, 每个集合可存储40多亿个成员)。

### 5. Zset(Sorted Set) 有序集合
- Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
- 不同的是每个元素都会关联一个double类型的分数。
- redis正是通过分数来为集合中的成员进行从小到大的排序。
- zset的成员是唯一的,但分数(score)却可以重复。

#### zadd 命令
- 添加元素到集合，元素在集合中存在则更新对应score
```redis
zadd key score member
```

---

```redis
> del key
(integer) 1

> zadd key 0 hi
(integer) 1
> zadd key 0 halo
(integer) 1
> zadd key 0 hello
(integer) 1
> zadd key 0 hello
(integer) 0

> ZRANGEBYSCORE key 0 1000
1) "halo"
2) "hello"
3) "hi"
```
- 无法重复加入相同元素在同一位置上
- Z-RANGE-BY-SCORE

### 5 种Redis支持的基本数据类型总结
![Redis 数据类型总结](https://gitee.com/lemonsoldout/images/raw/master/Redis数据类型.png)


### Redis 数据库方面的注意事项
- Redis支持多个数据库，并且每个数据库的数据是隔离的不能共享，并且基于单机才有，如果是集群就没有数据库的概念。
- Redis是一个字典结构的存储服务器，而实际上一个Redis实例提供了多个用来存储数据的字典，客户端可以指定将数据存储在哪个字典中。这与我们熟知的在一个关系数据库实例中可以创建多个数据库类似，所以可以将其中的每个字典都理解成一个独立的数据库。
- 每个数据库对外都是一个从0开始的递增数字命名，Redis默认支持16个数据库（可以通过配置文件支持更多，无上限），可以通过配置databases来修改这一数字。客户端与Redis建立连接后会自动选择0号数据库，不过可以随时使用SELECT命令更换数据库，如要选择1号数据库：
```redis
redis> SELECT 1
OK
redis [1] > GET foo
(nil)
```
> 然而这些以数字命名的数据库又与我们理解的数据库有所区别。首先Redis不支持自定义数据库的名字，每个数据库都以编号命名，开发者必须自己记录哪些数据库存储了哪些数据。另外Redis也不支持为每个数据库设置不同的访问密码，所以一个客户端要么可以访问全部数据库，要么连一个数据库也没有权限访问。最重要的一点是多个数据库之间并不是完全隔离的，比如FLUSHALL命令可以清空一个Redis实例中所有数据库中的数据。综上所述，这些数据库更像是一种命名空间，而不适宜存储不同应用程序的数据。比如可以使用0号数据库存储某个应用生产环境中的数据，使用1号数据库存储测试环境中的数据，但不适宜使用0号数据库存储A应用的数据而使用1号数据库B应用的数据，不同的应用应该使用不同的Redis实例存储数据。由于Redis非常轻量级，一个空Redis实例占用的内在只有1M左右，所以不用担心多个Redis实例会额外占用很多内存。

## Redis 命令
- Redis 命令用于在 redis 服务上执行操作。
- Redis 客户端的基本启动语法
```redis
redis-cli
```

### 连接 Redis 服务
1. 本地 Redis 服务连接
- 启动 redis 服务器，打开终端并输入命令 redis-cli，该命令会连接本地的 redis 服务。
```redis
redis ip_addr:port> ping
PONG
```

2. 远程 Redis 服务链接
```redis
redis-cli -h host -p port -a password
```

举例：连接到主机为 127.0.0.1，端口为 6379 ，密码为 mypass 的 redis 服务上
```redis
redis-cli -h 127.0.0.1 -p 6379 -a "mypass"
redis 127.0.0.1:6379> PING
PONG
```

## Redis 键（key）
- Redis 键命令用于管理 redis 的键。

### 语法
```redis
redis 127.0.0.1:6379> COMMAND KEY_NAME
```

### 例子
```redis
redis ip_addr:port> SET key redis
OK
redis ip_addr:port> DEL key
(integer) 1
```

- 在以上实例中 DEL 是一个命令， key 是一个键。 如果键被删除成功，命令执行后输出 (integer) 1，否则将输出 (integer) 0

## Redis 基本使用命令
[Redis 基本命令官方参考](https://redis.io/commands)
[Redis 基本命令 1](https://www.cnblogs.com/little-fly/p/10317122.html)
[Redis 基本命令 2](https://www.cnblogs.com/cxxjohnson/p/9072383.html)

### Key
1. 获取所有key：`keys *`
2. 选择第n个库：`select n-1`
3. 将当前的数据库key移动到第n个数据库,目标库有，则不能移动：`move myString n-1`
4. 清除指定库：`flush db`
5. 随机key：`randomkey`
6. 类型：`type key`
7. 设置key：`set key value`
8. 获取key：`get key`
9. 设置多个key：`mset key1 value1 key2 value2 key3 value3`
10. 获得多个key：`mget key1 key2 key3`
11. 删除key：`del key`
12. 判断key是否存在：`exists key`
13. key n 秒之后过期：`expire key n`
14. key n 毫秒之后过期：`pexpire key n`
15. key 永久存在，不过期：`persist key`

### String
1. 设置字符串类型的key：`set key  “Hello World!”`
2. 得到设置好的字符串类型的key：`get key`
3. 截取得到的字符串：`get range start_number end_number`
4. 设置新的key值，并返回旧的key值：`getset key "Nice!"`
5. 批量设置：`mset key1 "1" key2 "2"`
6. 批量获取：`mget key1 key2`
7. 不存在就插入：`setnx key value`(若不存在返回integer 1；存在返回 integer 0)
8. 设置 key n 秒过期：`setex key n redis`
9. 查询key还剩多久过期：`TTL key`
	- 当 key 不存在时，返回 -2 。 当 key 存在但没有设置剩余生存时间时，返回 -1 。 否则，以秒为单位，返回 key 的剩余生存时间。
10. 从 index 为 n 开始替换后边所有值：`setrange key n "something"`
```redis
> setrange key 2 "0 0 0 0 0 0"
15 (?)
```

11. 递增：`incr key`/递减`decr key`
- Redis Incr 命令将 key 中储存的数字值增 1。
- 如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCR 操作。
- 如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。
- 本操作的值限制在 64 位(bit)有符号数字表示之内。

12. 增加 n：`incrby key n`/减少 n：`decrby key n`
13. 增加浮点数：`incrbyfloat n`
14. 增加字符串（若原字符串不存在，相当于新建一个新的key；返回字符串长度）：`append key "ohhhh"`
15. 返回字符串长度：`strlen key`(若原字符串不存在，返回 0)
16. 位操作
	- :`setbit`:

	- :`getbit`: 字符串值指定偏移量上的位(bit)。 当偏移量 OFFSET 比字符串值的长度大，或者 key 不存在时，返回 0 。

	- :`bitcount`

	- :`bitop`

### Hash
1. hset 创建/hget 得到
> Redis Hset 命令用于为哈希表中的字段赋值 。
> 如果哈希表不存在，一个新的哈希表被创建并进行 HSET 操作。
> 如果字段已经存在于哈希表中，旧值将被覆盖。
```redis
> HSET KEY_NAME FIELD VALUE
```

- 如果字段是哈希表中的一个新建字段，并且值设置成功，返回 1 。 如果哈希表中域字段已经存在且旧值已被新值覆盖，返回 0 。
```redis
> hset key name "hello"
1
> hget key name
"hello"
> hset key name "hello1"
0
> hget key name
"hello1"
```

2. hmset 
> 创建多个字段-值对到哈希表中/hmget 得到相应的字段的值
```redis
> hmset key name cxk age 18 note "I love playing basketball!"
OK
> hmget key name age note
1) "cxk"
2) "18"
3) "I love playing basketball!"
```

3. hgetall 
> 得到所有的创建的字段的值
```redis
> hgetall key
1) "name"
2) "cxk"
3) "age"
4) "18"
5) "note"
6) "I love playing basketball!"
```

4. hexists key xxx 检查xxx是否存在
> 如果哈希表含有给定字段，返回 1 。 如果哈希表不含有给定字段，或 key 不存在，返回 0 。
```redis
> hexists key name
(integer) 1
> hexists key age
(integer) 1
> hexists key ageee
(integer) 0
```

5. hsetnx 为不存在的赋值
> Redis Hsetnx 命令用于为哈希表中不存在的的字段赋值 。
> 如果哈希表不存在，一个新的哈希表被创建并进行 HSET 操作。
> 如果字段已经存在于哈希表中，操作无效。
> 如果 key 不存在，一个新哈希表被创建并执行 HSETNX 命令。
```redis
> hsetnx key name "idiot"
(integer) 1
> hsetnx key name "idio"
(integer) 0
> hget key name
"idiot"
```

6. hincrby 增加 value 值
> Redis Hincrby 命令用于为哈希表中的字段值加上指定增量值。
> 增量也可以为负数，相当于对指定字段进行减法操作。
> 如果哈希表的 key 不存在，一个新的哈希表被创建并执行 HINCRBY 命令。
> 如果指定的字段不存在，那么在执行命令前，字段的值被初始化为 0 。
> 对一个储存字符串值的字段执行 HINCRBY 命令将造成一个错误。
> 本操作的值被限制在 64 位(bit)有符号数字表示之内。
```redis
> hset key id 1
1
> hincrby key id 5
(integer) 6
> hincrby key id -8
(integer) -2
```

7. hdel key id
> 删除key下的 id 键，并不会删除key
```redis
> hdel key id
(integer) 1
> hget key id
(nil)
```

8. hkeys
> 包含哈希表中所有域（field）列表。 当 key 不存在时，返回一个空列表。
```redis
> hset key first_name “Youn"
1
> hset key last_name “Lee"
1
> hkeys key
1) "first_name"
2) "last_name"
> hkeys key1
(empty list or set)
```

9. hvals
> 命令返回哈希表所有域(field)的值。
> 一个包含哈希表中所有域(field)值的列表。 当 key 不存在时，返回一个空表。
```redis
> hvals key
1) "“Youn\""
2) "“Lee\""
```
(疑问？)

10. hlen
> 命令用于获取哈希表中字段的数量。
> 哈希表中字段的数量。 当 key 不存在时，返回 0 。
```redis
> hset key name1 "Lee Youn"
1
> hset key name2 "Lee Yang"
1
> hlen key
2
> hlen key1
0
```

### List
1. lpush
2. rpush
3. lrange
4. lpop
5. rpop
6. llen
7. lrem
8. lindex
9. lset
10. ltrim
11. linsert
12. linsert
13. rpoplpush

### Set
1. sadd
2. smembers
3. srem
4. sismember
5. scard
6. sdiff
7. sinter
8. sunion
9. srandmember
10. spop

### Zset
1. zadd
2. zincrby
3. zscore
4. zrange
5. zrangebyscore
6. zcard
7. Zcount
8. Zrem
9. Zremrangebyrank
10. Zremrangebyscore
11. Zrank
12. Zrevrank
13. Zinterstore
14. zunionstore

### 排序
1. sort key 普通排序
2. sort key alpha desc limit 0 2 字母排序
3. sort list by it:* desc
4. sort list by it:* desc get it:*
5. sort list by it:* desc get it:* store sorc:result
> sort命令之store参数：表示把sort查询的结果集保存起来


## Redis 事务
### Redis 事务的概念
- Redis 事务的本质是一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。
- 总结说：redis事务就是一次性、顺序性、排他性的执行一个队列中的一系列命令。

### Redis 事务没有隔离级别的概念
- 批量操作在发送 EXEC 命令前被放入队列缓存，并不会被实际执行，也就不存在事务内的查询要看到事务里的更新，事务外查询不能看到。

### Redis 不保证原子性
- Redis中，单条命令是原子性执行的，但事务不保证原子性，且没有回滚。事务中任意命令执行失败，其余的命令仍会被执行。

### Redis 事务的三个阶段
1. 开始事务
2. 命令入队
3. 执行事务

### Redis 事务相关命令
- watch key1 key2 ... : 监视一或多个key,如果在事务执行之前，被监视的key被其他命令改动，则事务被打断 （ 类似乐观锁 ）
- multi : 标记一个事务块的开始（ queued ）
- exec : 执行所有事务块的命令 （ 一旦执行exec后，之前加的监控锁都会被取消掉 ）　
- discard : 取消事务，放弃事务块中的所有命令
- unwatch : 取消watch对所有key的监控

#### 1. 
- Redis 事务正常执行
<img src="../image/Redis/Redis1.png">

#### 2. 
- 放弃 Redis 事务
<img src="../image/Redis/Redis2.png">

#### 3. 
- 若在事务队列中存在命令性错误（类似于java编译性错误），则执行EXEC命令时，所有命令都不会执行
<img src="../image/Redis/Redis3.png">

#### 4. 
- 若在事务队列中存在语法性错误（类似于java的1/0的运行时异常），则执行EXEC命令时，其他正确命令会被执行，错误命令抛出异常。
<img src="../image/Redis/Redis4.png">

#### 5. 
- 使用 watch
<img src="../image/Redis/Redis5.png">

#### 6. 
- 使用watch检测balance，在开启事务后（标注1处），在新窗口执行标注2中的操作，更改balance的值，模拟其他客户端在事务执行期间更改watch监控的数据，然后再执行标注1后命令，执行EXEC后，事务未成功执行。
<img src="../image/Redis/Redis6.png">

- 一但执行 EXEC 开启事务的执行后，无论事务使用执行成功， WARCH 对变量的监控都将被取消。
- 故当事务执行失败后，需重新执行WATCH命令对变量进行监控，并开启新的事务进行操作。

#### 总结
- watch指令类似于乐观锁，在事务提交时，如果watch监控的多个KEY中任何KEY的值已经被其他客户端更改，则使用EXEC执行事务时，事务队列将不会被执行，同时返回Nullmulti-bulk应答以通知调用者事务执行失败。

## 服务器管理
### 1. 检查连接是否正常
- PING
	- 使用客户端向 Redis 服务器发送一个 PING ，如果客户端与服务器之间的连接正常，并且服务器的运作也正常的话，那么命令将返回一个PONG 。
	- 通常用于测试网络连接和服务器状态，或者用于测量延迟值。
```redis
redis> PING
PONG
redis> PING
Could not connect to Redis at 127.0.0.1:6379: Connection refused
```

### 2. 查看服务器信息
- INFO [section]
	- 查看 Redis 服务器的各种信息和统计数值。 通过给定可选的 section 参数，可以指定命令要返回的信息内容。

```redis
 redis> INFO
 # Server
 redis_version:2.9.11
 redis_git_sha1:937384d0
 redis_git_dirty:0
 redis_build_id:8e9509442863f22
 redis_mode:standalone
```

### 3. SECTION 参数的值
| 值              | 展示的信息                                                   |
| --------------- | ------------------------------------------------------------ |
| all             | 所有服务器信息                                               |
| default(默认值) | 默认被选中的，最常见也最重要的一些服务器信息                 |
| server          | 服务器本身的信息，比如版本号,监听端口号，服务器ID等          |
| clients         | 已连接的客户端的信息，比如已连接客户端的数量，正在被阻塞的客户端数量等 |
| memory          | 内存信息，比如内存占用数量，使用的内存分配器等等             |
| persistence     | 和 RDB 持久化以及 AOF 持久化有关的信息，比如 RDB 持久化是否正在进行，AOF 文件重写是否正在进行等等 |
| stats           | 服务器的统计信息，比如已处理的命令请求数量，每秒钟处理的命令请求数量等等 |
| replication     | 和主从复制有关信息，比如服务器的角色，主从服务器的连接状态是否正常等等。 |
| CPU             | 服务器的系统CPU占用量和用户CPU占用量                         |
| commandstats    | 命令执行的统计信息，比如命令执行的次数，命令耗费的CPU时间，执行每个命令耗费的平均CPU时间等等 |
| cluster         | 集群功能相关信息                                             |
| keyspace        | 和数据库键空间有关的信息，比如数据库的键数量，数据库已经被删除的过期键数量等等 |

### 4. 查看服务器正在执行的命令
- 实时地打印出 Redis 服务器接收到的命令，格式 为“时间戳 [数据库号码 IP地址和端口号] 被执行的命令”。
```redis
 redis> MONITOR
 OK
 1378822099.421623 [0 127.0.0.1:56604] "PING"
 1378822105.089572 [0 127.0.0.1:56604] "SET" "msg" "hello world"
 1378822109.036925 [0 127.0.0.1:56604] "SET" "number" "123"
 1378822140.649496 [0 127.0.0.1:56604] "SADD" "fruits" "Apple" "Banana" "Cherry"
 1378822154.117160 [0 127.0.0.1:56604] "EXPIRE" "msg" "10086"
 1378822257.329412 [0 127.0.0.1:56604] "KEYS" "*"
 1378822258.690131 [0 127.0.0.1:56604] "DBSIZE"
```

### 5. 发现执行缓慢的命令
- 慢查询功能

#### 记录慢查询日志
- Redis 的慢查询功能用于将执行时间超过指定时长的命令记录起来，并向用户展示那些被记录的命令，方便用户发现运行缓慢的命令，并进行针对性的优化。
- 慢查询功能可以通过以下两个配置选项来进行设置：
	1. slowlog-log-slower-than
		- 执行时长超过 microseconds 微秒的命令将被记录，将这个值设置为负数表示关闭慢查询功能。
		- 默认值为 10000 ，也即是 1/100 秒，因为 1 秒 = 1 百万微秒。
	2. slowlog-max-len
		- 慢查询日志的最大数量，当记录的日志超过这个数量时，新日志会覆盖旧日志（ FIFO）。
		- 默认值为 128 。

#### 查看慢查询日志
- SLOWLOG GET [number]
	- 返回服务器目前记录的慢查询日志。
	- 如果给定可选的 number 参数，那么只返回最多 number 条日志；否则的话，返回所有慢查询日志。
```unknown
# 为测试需要，将 slowlog-log-slower-than 设成了 10 微秒
 redis> SLOWLOG GET
 1) 1) (integer) 12 # 唯一的日志标识符
 2) (integer) 1324097834 # 被记录命令的执行时间，以 UNIX 时间戳格式表示
 3) (integer) 16 # 命令执行耗费的时长，以微秒为单位
 4) 1) "CONFIG" # 被执行的命令，以数组的形式排列
 2) "GET" # 这里完整的命令是 CONFIG GET slowlog-log-slower-than
 3) "slowlog-log-slower-than"
```

### 8. 慢查询日志的其他命令
| 命令          | 作用                         |
| ------------- | ---------------------------- |
| slowlog len   | 查看目前已有的慢查询日志数量 |
| slowlog reset | 删除所有慢查询日志           |

```redis
redis> SLOWLOG LEN
 (integer) 14
 redis> SLOWLOG RESET
 OK
 redis> SLOWLOG LEN
 (integer) 0
```

### 9. 服务器的枷锁与解锁
- 通过配置选项 requirepass ，用户可以为服务器设置密码。当客户端连接一个带密码的服务器时，它必须执行 AUTH 命令来进行解锁，否则这个客户端就不能执行除 AUTH 以外的其他命令。

举个例子，如果我们在服务器启动时，用以下方式给服务器设置了密码：
```redis
$ redis-server --requirepass helloworld
 那么连接服务器的客户端必须在解锁之后才能执行其他命令：
 redis> PING
 (error) NOAUTH Authentication required.
 redis> AUTH helloworld
 OK
 redis> PING
 PONG
```

### 10. 关闭服务器
- SHUTDOWN [option]
在不给定 option 参数的情况下，服务器会先执行持久化操作：
	- 如果打开了 AOF 持久化，那么调用 fdatasync ，确保之前执行的命令能够被写入到硬盘。
	- 如果打开了 RDB 持久化并且数据库已经发生了变化，那么执行 SAVE 命令。
	- 在以上操作都完成之后，服务器关闭。

- 在打开了持久化功能的情况下，使用 SHUTDOWN 命令关闭服务器不会丢失任何数据。
	- option 选项的值可以是 save 或者 nosave
	- SHUTDOWN save 在关闭之前总是执行 SAVE 命令，用于在没有开启 RDB 持久化的情况下，创建一个 RDB 文件来保存数据；
	- SHUTDOWN nosave 在关闭之前不执行 SAVE 命令，用于在数据库可能已经出错的情况下，避免将错误的数据保存到 RDB 文件里面。

## Redis 实现异步队列
[异步队列 延时队列 1](https://zhuanlan.zhihu.com/p/237999295)
[异步队列 延时队列 2](https://blog.csdn.net/ruanhao1203/article/details/88679900)
## Redis 实现发布订阅
[发布订阅的几种方式](https://www.cnblogs.com/kellynic/p/9952386.html)
### 1. Subscribe + Publish
订阅预发布
1. 订阅频道：subscribe chat1
2. 发布消息：publish chat1 "hell0 ni hao"
3. 查看频道：pubsub channels
4. 查看某个频道的订阅者数量: pubsub numsub chat1
5. 退订指定频道： unsubscrible chat1 , punsubscribe java.*
6. 订阅一组频道： psubscribe java.*
<img src="../image/Redis/Redis13.png">
```java
//程序1：使用代码实现订阅端
var sub = RedisHelper.Subscribe(("chan1", msg => Console.WriteLine(msg.Body)));
//sub.Disponse(); //停止订阅

//程序2：使用代码实现发布端
RedisHelper.Publish("chan1", "111");
```
- 优势：支持多端订阅、简单、性能高；
- 缺点：数据会丢失；

### 2. Blpop + Lpush(争抢)
<img src="../image/Redis/Redis14.png">

```java
//程序1：使用代码实现订阅端
while (running) {
	try {
		var msg = RedisHelper.BLPop(5, "list1");
		if (string.IsNullOrEmpty(msg) == false) {
			Console.WriteLine(msg);
		}
	} catch (Exception ex) {
		Console.WriteLine(ex.Message);
	}
}

//程序2：使用代码实现发布端
RedisHelper.LPush("list1", "111");
```
- 优势：数据不会丢失、简单、性能高；
- 缺点：不支持多端（存在资源争抢）；

- 总结：为了解决方法一的痛点，我们实现了本方法，并且很漂亮的制造了一个新问题（不支持多端订阅）。

#### 学习使用Blpop
[Blpop知识](http://doc.redisfans.com/list/blpop.html)
> BLPOP key [key ...] timeout
- BLPOP 是列表的阻塞式(blocking)弹出原语。
- 它是 LPOP 命令的阻塞版本，当给定列表内没有任何元素可供弹出的时候，连接将被 BLPOP 命令阻塞，直到等待超时或发现可弹出元素为止。
- 当给定多个 key 参数时，按参数 key 的先后顺序依次检查各个列表，弹出第一个非空列表的头元素。

##### 非阻塞行为
- 当 BLPOP 被调用时，如果给定 key 内至少有一个非空列表，那么弹出遇到的第一个非空列表的头元素，并和被弹出元素所属的列表的名字一起，组成结果返回给调用者。
- 当存在多个给定 key 时， BLPOP 按给定 key 参数排列的先后顺序，依次检查各个列表。
- 假设现在有 job 、 command 和 request 三个列表，其中 job 不存在， command 和 request 都持有非空列表。考虑以下命令：
```redis
BLPOP job command request 0
```
- BLPOP 保证返回的元素来自 command ，因为它是按”查找 job -> 查找 command -> 查找 request “这样的顺序，第一个找到的非空列表。
```redis
redis> DEL job command request           # 确保key都被删除
(integer) 0

redis> LPUSH command "update system..."  # 为command列表增加一个值
(integer) 1

redis> LPUSH request "visit page"        # 为request列表增加一个值
(integer) 1

redis> BLPOP job command request 0       # job 列表为空，被跳过，紧接着 command 列表的第一个元素被弹出。
1) "command"                             # 弹出元素所属的列表
2) "update system..."                    # 弹出元素所属的值
```

##### 阻塞行为
- 如果所有给定 key 都不存在或包含空列表，那么 BLPOP 命令将阻塞连接，直到等待超时，或有另一个客户端对给定 key 的任意一个执行 LPUSH 或 RPUSH 命令为止。
- 超时参数 timeout 接受一个以秒为单位的数字作为值。超时参数设为 0 表示阻塞时间可以无限期延长(block indefinitely) 。
```redis
redis> EXISTS job                # 确保两个 key 都不存在
(integer) 0
redis> EXISTS command
(integer) 0

redis> BLPOP job command 300     # 因为key一开始不存在，所以操作会被阻塞，直到另一客户端对 job 或者 command 列表进行 PUSH 操作。
1) "job"                         # 这里被 push 的是 job
2) "do my home work"             # 被弹出的值
(26.26s)                         # 等待的秒数

redis> BLPOP job command 5       # 等待超时的情况
(nil)
(5.66s)                          # 等待的秒数
```

### 3. Bloop + Lpush(非争抢)
- 本方法根据方法二演变而来，设计图如下
<img src="../image/Redis/Redis15.png">

如何实现三端订阅，都可收到消息，三端分别为 sub3, sub4, sub5：
1. sub3, sub4, sub5 使用【方法二】订阅 listkey：list1_sub3，list1_sub4，list1_sub5；
2. 总订阅端订阅 listkey：list1，总订阅端收到消息后，执行 lpush list1_sub1 msg， lpush list1_sub2 msg， lpush list1_sub3 msg；
- 总订阅端订阅原始消息，随后将消息分发给其他订阅端，从而解决 方法二 不支持多端同时订阅的缺点。
- 最终实现的逻辑为：多端先争抢 list1 消息，抢到者再向其他端转发消息。

#### 测试代码
```redis
var rds = new CSRedis.CSRedisClient("127.0.0.1:6379,password=,poolsize=50,ssl=false,writeBuffer=10240");

//sub1, sub2 争抢订阅（只可一端收到消息）
var sub1 = rds.SubscribeList("list1", msg => Console.WriteLine($"sub1 -> list1 : {msg}"));
var sub2 = rds.SubscribeList("list1", msg => Console.WriteLine($"sub2 -> list1 : {msg}"));

//sub3, sub4, sub5 非争抢订阅（多端都可收到消息）
var sub3 = rds.SubscribeListBroadcast("list2", "sub3", msg => Console.WriteLine($"sub3 -> list2 : {msg}"));
var sub4 = rds.SubscribeListBroadcast("list2", "sub4", msg => Console.WriteLine($"sub4 -> list2 : {msg}"));
var sub5 = rds.SubscribeListBroadcast("list2", "sub5", msg => Console.WriteLine($"sub5 -> list2 : {msg}"));

//sub6 是redis自带的普通订阅
var sub6 = rds.Subscribe(("chan1", msg => Console.WriteLine(msg.Body)));

Console.ReadKey();
sub1.Dispose();
sub2.Dispose();
sub3.Dispose();
sub4.Dispose();
sub5.Dispose();
sub6.Dispose();

rds.Dispose();
return;
```
<img src="../image/Redis/Redis16.png">

## Redis 持久化
- Redis（Remote Dictionary Server）是一个可持久化的内存、Key-Value数据库。
- 作为内存数据库，为了防止因服务器断电或系统宕机而引起的数据丢失问题，Redis自带了持久化功能，就是将内存中的数据同步到磁盘来保证持久化（防止数据丢失）。
- Redis支持两种持久化方式：RDB（redis database）和AOF（append only file）。

### 1. RDB
#### 概念
- 在指定的时间间隔内将内存中的数据集快照（Snapshot）写入磁盘，待服务重启时，将快照文件读入内存中。

#### 原理
- 在执行持久化时，redis使用Fork的方式单独创建一个进程来进程持久化，该进程会先将内存中的数据写入到一个临时文件中，待持久化结束时，用此临时文件替换掉上次持久化保存的文件（默认的文件名为dump.rdb）。

#### Fork原理
- 复制一个与当前进程一样的进程，新进程的所有数据都与原进程一样，但是一个全新的进程，并作为原进程的子进程。

#### 触发条件
- 可以通过 redis命令 或者配置 redis.conf 文件来指定redis持久化的时机。
1. 使用命令
	- save ( 立刻redis数据持久化，其他全部阻塞 )
	- bgsave ( redis会在后台异步进行快照操作，同时还可响应客户端的请求，可用命令 lastsave  获取最后一次成功执行快照的时间 )
	- flushall  ( 清空命令也会触发持久化操作，但dump.rdb文件中是空的，无意义)
	- shutdown  ( 关闭数据库命令也会触发redis持久化操作 )

2. 使用 redis.conf 配置文件
	- 相关配置位于 redis.conf 文件中的 "SNAPSHOTTING" 配置部分：save <seconds> <changes> 指定在 seconds 秒内有 changes 次跟新操作，就进行一次持久化。
<img src="../image/Redis/Redis7.png">

#### 禁用RDB持久化
- 可以通过 redis命令 或者配置 redis.conf 文件来禁用 RDB持久化
1. 使用命令
	- redis-cli config set save ""
2. 使用 redis.conf 配置文件
	- 相关配置位于 redis.conf 文件中的 "SNAPSHOTTING" 配置部分，设置 save "" 配置时，代表禁用RDB持久化。
<img src="../image/Redis/Redis8.png">
	- 持久化文件：位于 redis.conf 文件中的 "SNAPSHOTTING" 配置部分：
	- dir  参数配置持久化文件的目录，dbfilename 参数配置持久化参数的名称。
<img src="../image/Redis/Redis9.png">
	- 恢复持久化文件：将备份文件移动至  dir  参数配置持久化文件的目录下，并将文件名改为 dbfilename 参数配置持久化参数的文件名，然后启动数据库即可恢复数据。
	- 若 dump.rdb 文件存在异常，数据恢复将报错。可使用命令 redis-check-dump --fix <filename> 命令对 dump.rdb 持久化文件进行修复，然后重启redis服务即可。

- 优点：持久化过程中，主进程不进行任何IO操作，对于大规模的数据恢复，具有极高的性能。
- 缺点：RDB最后一次持久化后的数据可能会因为redis意外down掉而丢失最后一次快照后修改的所有数据；
- Fork时，内存数据被克隆，大致2倍的膨胀性，会对服务器造成一定的压力，影响性能。　　　
- 应用：适合大规模数据恢复，且对恢复数据的完整性、一致性不是很严格，这种情况下RDB比AOF（append only file）持久化 更加高效。

### 2. AOF
#### 原理
- 以日志的形式来记录每个写操作（读操作不记录），只许追加文件，不可改写文件，redis启动时，将根据日志文件内容将写指令从前到后执行一次以完成数据的恢复。

#### 触发条件
- 可以通过配置 redis.conf 文件来指定redis持久化的时机。

- 默认AOF持久化时关闭的，将参数值设置为 yes 即开启AOF持久化。
- appendfsync 参数用于指定同步频率，具体使用如下图。
<img src="../image/Redis/Redis10.png">

#### 持久化文件
- 位于 redis.conf 文件中的 "SNAPSHOTTING" 配置部分
- dir  参数同时也配置 AOF持久化文件的目录；
- appendfilename 参数（上图中）用来指定 AOF持久化文件的文件名。

##### 恢复持久化文件
- 保证 redis.conf 文件中 dir  参数 指定的目录下存在与 appendfilename 参数配置的文件名相同的.aof文件，重启服务时即可恢复。
- 当同时开启 AOF 和 RDB 两种持久化时，启动服务时，会优加载 AOF 持久化文件进行数据的恢复。　　　　　　　
- 若 AOF文件存在异常，数据恢复将报错。可使用命令 `redis-check-aof --fix <filename>` 命令对 .aof 持久化文件进行修复，然后重启redis服务即可。

##### AOF持久化文件Rewrite重写机制
- 引入重写机制背景
	
- AOF采用文件追加的方式，将导致文件越来越大，故新增重写机制。当AOF文件大小超过所设置的阈值时，Redis将启动AOF内容压缩，只保留可以恢复数据的最小指令集。
	
- 重写原理
	
- 当AOF文件大小持续增长超过配置参数设置的阈值时，redis将fork出一条新进程来将文件重写（也是先将内容写入临时文件，最后再rename）。重写AOF文件时，并不是参照旧的AOF文件，而是遍历新进程内存中的数据，每条记录都添加一条set语句，从而重写一个新的AOF文件，只保留可以恢复数据的最小指令集，类似于快照。
	
- 触发机制
	- 可以通过 redis命令 或者配置 redis.conf 文件来配置Rewrite时机
	1. 使用命令 bgrewriteeaof
	2. 使用配置文件：redis会记录上一次重写AOF文件的大小。默认配置是当AOF文件大小是上次重写后大小的一倍且文件大于64M触发重写操作。具体配置如下图
<img src="../image/Redis/Redis11.png">

	- 优点
		- 可以设置appendfsync 为 每秒同步 每次修改同步 和不同步，最恶劣情况下，丢失不超过两秒的数据
	- 缺点
		- 相同数据集的数据，AOF文件远大于RBD文件，且恢复速度慢于RBD；AOF运行效率慢于RBD，每秒同步策略效率较好，不同步效率和RDB相同；AOF在rewrite过程中将新数据写到新文件中造成的阻塞是不可避免的。

### RDB 和 AOF 使用指南
1. 建议AOF和RDB同时开启；
2. RDB作为后备用途,备份频率设置为 ： save 900 1；
3. 减少AOF文件的rewrite频率, 设置重写的基准值： auto-aof-rewrite-min-size 5G auto-aof-rewrite-percentage 100。

#### 生产使用策略
1. 使用 Master-Slave 主从复制策略；
2. Slaver上使用RDB方式持久化，策略为 save  900 1 ；
3. Master上依据实际情况决定是否使用AOF，如使用，建议重写基础大小设置为5G，重写百分比依据实际确定。

## Redis 主从复制
### 概念
- 主从复制就是主机数据更新后，根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主。

### 配置
#### 1. "一主二仆"策略
- 准备三台redis服务器：主服务器A，从服务器B1、从服务器B2。服务器B1、B2同步A数据，A1负责写操作，B1、B2负责读操作。
	- A服务器IP：168.7.5.74，端口6379
	- B1服务器IP：168.7.5.75，端口6379
	- B2服务器IP：168.7.5.76，端口6379

##### 使用命令
- 在服务器B1、B2上分别执行如下命令，成为A服务器的从库：
```redis
slaveof 168.7.5.74 6379
```
- 使用命令可查看当前库role、master_host、manter_port、connected_slaves相关配置信息：
```redis
info replication
```

##### 注意事项
1. 当主库宕机后，从库仍是slave角色，仍负责接受读操作；
2. 当主库恢复服务后，从库B1、B2继续同步主库A的数据，服务照旧；
3. 当从库与master断开连接后，如果使用的是命令配置，需要重新使用命令才能连接到主库；
4. 当从库连接master后，master会先一次性将当前数据全量同步到salve上，后续增量同步。

##### 使用配置文件
- 在需要成为从库的redis配置文件中添加配置"replicaof 10.7.5.74 6379"即可。与命令配置不同的是，从库重启服务后将自动同步主库，无需额外操作。
<img src="../image/Redis/Redis12.png">

#### 2. "薪火相传"策略
- 准备三台redis服务器：服务器A，服务器B、服务器C。将服务器B设置为服务器A的从库，将服务器C设置为服务器B的从库：masterA <-- slaverB <-- slaverC。　　
	- A服务器IP：168.7.5.74，端口6379
	- B服务器IP：168.7.5.75，端口6379
	- C服务器IP：168.7.5.76，端口6379
- 执行如下命令进行该策略配置：
	- 在服务器B上执行：slaveof 168.7.5.74 6379
	- 在服务器C上执行：slaveof 168.7.5.75 6379

##### 注意事项
1. 服务器A的角色为 master，服务器B和C的角色为 slaver；
2. 当主库A宕机后，在从库B上执行命令 slaverof no one 将从库B角色变为 salve ，从库C无需操作，将继续使用同步B；
3. 当A重启服务器后，与BC库组成的体系毫无关系，需重新执行命令加入；
4. salverof no one ：是当前数据库停止与其他数据库的同步，并转换为主数据库。

- 优点：去除“一主二仆”策略的中心化，减轻master库写的压力。
- 缺点：从库存在备份延迟

#### 3. 哨兵模式
##### 原理
- 在后台自动监控主机是否故障，如果故障了，根据投票自动将从库转为主库。

##### 配置
1. 创建哨兵模式配置文件。在redis.conf目录下创建文件 sentinel.conf (名称固定)，内容如下：
	- sentinel monitor hostname 127.0.0.1 6379 1  
	- hostname 127.0.0.1 6379 ：分别是被监控主机的主机名、IP及端口号
	- 1指定策略：主机宕机后，slave投票决定谁接替主机
2. 启动哨兵模式
	- redis-sentinel /usr/redis/sentinel.conf

##### 注意事项
1. 若当前master宕机后，服务将在从库中选出新的master；
2. 若旧master重启后，它将自动成为新的master的slave；
3. 一个哨兵(sentinel)可以监控多个master。

##### 应用
1. 读写分离
2. 容灾备份

### 主从复制的缺点
- 复制的延迟，写操作都在master上，然后同步到slave上，所以会有一定的延迟，同时slave机器数量的增加也会使延迟问题更加严重。

## Redis 实现 Dubbo 注册中心


## Redis 分布式锁


## Redis 缓存问题
### 1. 缓存雪崩

### 2. 缓存穿透

### 3. 缓存击穿


## LRU 算法
### 1. no eviction

### 2. allkeys-random

### 3. allkey-lru

### 4. volatile-random

### 5. volatile-lru

### 6. volatile-ttl


## Redis 高可用
### 1. Redis Cluster

### 2. Redis sentinel

### 3. Redis 主从复制


## Redis 线程模型


## 其他
### BloomFilter

## aop 备份处理
appendonly yes 开启持久化
appendfsync everysec 每秒备份一次

## 其他
bgsave异步保存数据到磁盘（快照保存）
lastsave返回上次成功保存到磁盘的unix的时间戳
shutdown同步保存到服务器并关闭redis服务器
bgrewriteaof文件压缩处理（命令）



-------------------------------------
## 伟大的分割线
-------------------------------------



## Redis conf配置文件
```bash
# Redis must be started with the file path as first argument:
# ./redis-server /path/to/redis.conf

## redis中的度量单位只支持bytes，不支持bit，大小写不敏感，且 k/kb、m/mb、g/gb 代表的单位大小有所不同。
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes

################################## INCLUDES ###################################

# 用于引入其他配置文件，和本配置文件中配置共同配置redis服务
# include /path/to/local.conf
# include /path/to/other.conf

################################## MODULES #####################################

# Load modules at startup. If the server is not able to load modules
# it will abort. It is possible to use multiple loadmodule directives.
#
# loadmodule /path/to/my_module.so
# loadmodule /path/to/other_module.so

################################## NETWORK #####################################

# By default, if no "bind" configuration directive is specified, Redis listens
# for connections from all the network interfaces available on the server.
# It is possible to listen to just one or multiple selected interfaces using
# the "bind" configuration directive, followed by one or more IP addresses.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
#
# ~~~ WARNING ~~~ If the computer running Redis is directly exposed to the
# internet, binding to all the interfaces is dangerous and will expose the
# instance to everybody on the internet. So by default we uncomment the
# following bind directive, that will force Redis to listen only into
# the IPv4 loopback interface address (this means Redis will be able to
# accept connections only from clients running into the same computer it
# is running).
#
# IF YOU ARE SURE YOU WANT YOUR INSTANCE TO LISTEN TO ALL THE INTERFACES
# JUST COMMENT THE FOLLOWING LINE.
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
bind 127.0.0.1

# Protected mode is a layer of security protection, in order to avoid that
# Redis instances left open on the internet are accessed and exploited.
#
# When protected mode is on and if:
#
# 1) The server is not binding explicitly to a set of addresses using the
#    "bind" directive.
# 2) No password is configured.
#
# The server only accepts connections from clients connecting from the
# IPv4 and IPv6 loopback addresses 127.0.0.1 and ::1, and from Unix domain
# sockets.
#
# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured, nor a specific set of interfaces
# are explicitly listed using the "bind" directive.
protected-mode yes

# 服务启动端口，默认为 6379 ，如果设置端口为 0 ，redis 服务将不会监听任何TCP连接
port 6379

# 设置TCP的连接队列 backlog ，默认为 511 ，backlog 队列总和 = 未完成三次握手队列 + 已完成三次握手队列
# 作用：在高并发环境中，设置高 backlog 值，来避免慢客户端的连接# 设置方式：（1）设置 /proc/sys/net/core/somaxconn （2）设置 tcp_max_syn_backlog# Linux内核会将 tcp_max_syn_backlog 的值减小到 somaxconn ，故需要同时设置# tcp_max_syn_backlog 1024 tcp-backlog 511

# Unix socket.
#
# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /tmp/redis.sock
# unixsocketperm 700

# 当客户端超过N秒空闲后，服务器主动断开连接，设置为 0 表示不主动断开连接
timeout 0

# 检测 TCP 连接alive状态的频率，设置为0表示不检测，建议设置为60
tcp-keepalive 300

################################# GENERAL #####################################

# 设置redis服务以后台守护进程启动，默认为 no非daemon
# 后台启动后服务的 pid 位于文件 /var/run/redis 中
daemonize no

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
supervised no

# If a pid file is specified, Redis writes it where specified at startup
# and removes it at exit.
#
# When the server runs non daemonized, no pid file is created if none is
# specified in the configuration. When the server is daemonized, the pid file
# is used even if not specified, defaulting to "/var/run/redis.pid".
#
# Creating a pid file is best effort: if Redis is not able to create it
# nothing bad happens, the server will start and run normally.
pidfile /var/run/redis_6379.pid

# 设置输出日志级别，包括 debug、verbos、notice、warning
loglevel notice

# 设置日志输出文件，若为空字符串"" 或者 stdout ，则将日志从定位到 /dev/null
logfile ""

# 若要将redis日志记录到系统日志，将此参数设置为 yes
# syslog-enabled no

# 用于指定日志标识：若开启系统日志，指定日志以 redis 开头
# syslog-ident redis

# 设置系统日志输出设备，值可为 USER 或者 LOCAL0-LOCAL7，默认为local0
# syslog-facility local0

# 设置 redis 服务启动数据库的个数，默认 16 个，默认数据库为 DB 0 ，可使用 select <dbid> 进行切库，dbid为 0 至 databases-1
databases 16

# 是否总是显示redis那个"蛋糕"logo
always-show-logo yes

################################ SNAPSHOTTING  ################################
# 指定在规定时间内，有多少此更新操作，就将数据同步至数据文件，可多个条件配合使用
#   save <seconds> <changes>
#　　save ""
# 默认配置如下：15分钟内有一个更改，5分钟内有10个更改，1分钟内有10000个更改
save 900 1 
save 300 10
save 60 10000

# 当RDB在后台持久化出错后，是否依然进行数据库写操作，yes:停止写操作，no:继续写操作
stop-writes-on-bgsave-error yes

# 指定存储至本地数据库时是否压缩数据，默认为yes：使用压缩，redis采用LZF压缩算法，使用会消耗CPU，不使用占内存
rdbcompression yes

# 是否校验压缩后的rdb文件，默认为yes：进行校验，开启大概有10%性能损耗
rdbchecksum yes

# 指定本地数据库文件名，默认为dump.rdb
dbfilename dump.rdb

# 指定本地数据文件存放目录
dir ./

################################# REPLICATION #################################

# 将当前服务器作为从库，同步备份主库的数据，并提供读操作
# replicaof <masterip> <masterport># replicaof 10.7.5.74 6379

# 当master服务设置了密码保护时，slave服务连接master的密码
# masterauth <master-password>

# When a replica loses its connection with the master, or when the replication
# is still in progress, the replica can act in two different ways:
#
# 1) if replica-serve-stale-data is set to 'yes' (the default) the replica will
#    still reply to client requests, possibly with out of date data, or the
#    data set may just be empty if this is the first synchronization.
#
# 2) if replica-serve-stale-data is set to 'no' the replica will reply with
#    an error "SYNC with master in progress" to all the kind of commands
#    but to INFO, replicaOF, AUTH, PING, SHUTDOWN, REPLCONF, ROLE, CONFIG,
#    SUBSCRIBE, UNSUBSCRIBE, PSUBSCRIBE, PUNSUBSCRIBE, PUBLISH, PUBSUB,
#    COMMAND, POST, HOST: and LATENCY.
#
replica-serve-stale-data yes

# You can configure a replica instance to accept writes or not. Writing against
# a replica instance may be useful to store some ephemeral data (because data
# written on a replica will be easily deleted after resync with the master) but
# may also cause problems if clients are writing to it because of a
# misconfiguration.
#
# Since Redis 2.6 by default replicas are read-only.
#
# Note: read only replicas are not designed to be exposed to untrusted clients
# on the internet. It's just a protection layer against misuse of the instance.
# Still a read only replica exports by default all the administrative commands
# such as CONFIG, DEBUG, and so forth. To a limited extent you can improve
# security of read only replicas using 'rename-command' to shadow all the
# administrative / dangerous commands.
replica-read-only yes

# Replication SYNC strategy: disk or socket.
#
# -------------------------------------------------------
# WARNING: DISKLESS REPLICATION IS EXPERIMENTAL CURRENTLY
# -------------------------------------------------------
#
# New replicas and reconnecting replicas that are not able to continue the replication
# process just receiving differences, need to do what is called a "full
# synchronization". An RDB file is transmitted from the master to the replicas.
# The transmission can happen in two different ways:
#
# 1) Disk-backed: The Redis master creates a new process that writes the RDB
#                 file on disk. Later the file is transferred by the parent
#                 process to the replicas incrementally.
# 2) Diskless: The Redis master creates a new process that directly writes the
#              RDB file to replica sockets, without touching the disk at all.
#
# With disk-backed replication, while the RDB file is generated, more replicas
# can be queued and served with the RDB file as soon as the current child producing
# the RDB file finishes its work. With diskless replication instead once
# the transfer starts, new replicas arriving will be queued and a new transfer
# will start when the current one terminates.
#
# When diskless replication is used, the master waits a configurable amount of
# time (in seconds) before starting the transfer in the hope that multiple replicas
# will arrive and the transfer can be parallelized.
#
# With slow disks and fast (large bandwidth) networks, diskless replication
# works better.
repl-diskless-sync no

# When diskless replication is enabled, it is possible to configure the delay
# the server waits in order to spawn the child that transfers the RDB via socket
# to the replicas.
#
# This is important since once the transfer starts, it is not possible to serve
# new replicas arriving, that will be queued for the next RDB transfer, so the server
# waits a delay in order to let more replicas arrive.
#
# The delay is specified in seconds, and by default is 5 seconds. To disable
# it entirely just set it to 0 seconds and the transfer will start ASAP.
repl-diskless-sync-delay 5

# Replicas send PINGs to server in a predefined interval. It's possible to change
# this interval with the repl_ping_replica_period option. The default value is 10
# seconds.
#
# repl-ping-replica-period 10

# The following option sets the replication timeout for:
#
# 1) Bulk transfer I/O during SYNC, from the point of view of replica.
# 2) Master timeout from the point of view of replicas (data, pings).
# 3) Replica timeout from the point of view of masters (REPLCONF ACK pings).
#
# It is important to make sure that this value is greater than the value
# specified for repl-ping-replica-period otherwise a timeout will be detected
# every time there is low traffic between the master and the replica.
#
# repl-timeout 60

# Disable TCP_NODELAY on the replica socket after SYNC?
#
# If you select "yes" Redis will use a smaller number of TCP packets and
# less bandwidth to send data to replicas. But this can add a delay for
# the data to appear on the replica side, up to 40 milliseconds with
# Linux kernels using a default configuration.
#
# If you select "no" the delay for data to appear on the replica side will
# be reduced but more bandwidth will be used for replication.
#
# By default we optimize for low latency, but in very high traffic conditions
# or when the master and replicas are many hops away, turning this to "yes" may
# be a good idea.
repl-disable-tcp-nodelay no

# Set the replication backlog size. The backlog is a buffer that accumulates
# replica data when replicas are disconnected for some time, so that when a replica
# wants to reconnect again, often a full resync is not needed, but a partial
# resync is enough, just passing the portion of data the replica missed while
# disconnected.
#
# The bigger the replication backlog, the longer the time the replica can be
# disconnected and later be able to perform a partial resynchronization.
#
# The backlog is only allocated once there is at least a replica connected.
#
# repl-backlog-size 1mb

# After a master has no longer connected replicas for some time, the backlog
# will be freed. The following option configures the amount of seconds that
# need to elapse, starting from the time the last replica disconnected, for
# the backlog buffer to be freed.
#
# Note that replicas never free the backlog for timeout, since they may be
# promoted to masters later, and should be able to correctly "partially
# resynchronize" with the replicas: hence they should always accumulate backlog.
#
# A value of 0 means to never release the backlog.
#
# repl-backlog-ttl 3600

# The replica priority is an integer number published by Redis in the INFO output.
# It is used by Redis Sentinel in order to select a replica to promote into a
# master if the master is no longer working correctly.
#
# A replica with a low priority number is considered better for promotion, so
# for instance if there are three replicas with priority 10, 100, 25 Sentinel will
# pick the one with priority 10, that is the lowest.
#
# However a special priority of 0 marks the replica as not able to perform the
# role of master, so a replica with priority of 0 will never be selected by
# Redis Sentinel for promotion.
#
# By default the priority is 100.
replica-priority 100

# It is possible for a master to stop accepting writes if there are less than
# N replicas connected, having a lag less or equal than M seconds.
#
# The N replicas need to be in "online" state.
#
# The lag in seconds, that must be <= the specified value, is calculated from
# the last ping received from the replica, that is usually sent every second.
#
# This option does not GUARANTEE that N replicas will accept the write, but
# will limit the window of exposure for lost writes in case not enough replicas
# are available, to the specified number of seconds.
#
# For example to require at least 3 replicas with a lag <= 10 seconds use:
#
# min-replicas-to-write 3
# min-replicas-max-lag 10
#
# Setting one or the other to 0 disables the feature.
#
# By default min-replicas-to-write is set to 0 (feature disabled) and
# min-replicas-max-lag is set to 10.

# A Redis master is able to list the address and port of the attached
# replicas in different ways. For example the "INFO replication" section
# offers this information, which is used, among other tools, by
# Redis Sentinel in order to discover replica instances.
# Another place where this info is available is in the output of the
# "ROLE" command of a master.
#
# The listed IP and address normally reported by a replica is obtained
# in the following way:
#
#   IP: The address is auto detected by checking the peer address
#   of the socket used by the replica to connect with the master.
#
#   Port: The port is communicated by the replica during the replication
#   handshake, and is normally the port that the replica is using to
#   listen for connections.
#
# However when port forwarding or Network Address Translation (NAT) is
# used, the replica may be actually reachable via different IP and port
# pairs. The following two options can be used by a replica in order to
# report to its master a specific set of IP and port, so that both INFO
# and ROLE will report those values.
#
# There is no need to use both the options if you need to override just
# the port or the IP address.
#
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234

################################## SECURITY ###################################

# 登录 redis 数据库密码认证问题# 执行命令 config get requirepass ，获取配置文件中默认认证密码，默认密码为 foobared# requirepass foobared
# 执行命令 config set requirepass "redis" ，设置认证密码为 redis ，设置为空 "" 表示不认证# 在执行命令前使用 auth <password> 命令进行认证

# 禁止远程修改 DB 文件地址，就是对命令进行权限控制
# 将命令重命名为空 ""，表示禁用该命令
# rename-command FLASHALL ""
# 也可将命令重命名为 qazwsx741852edc ，然后将此名授权给特定用户使用即可# rename-command CONFIG "qazwsx741852edc"
################################### CLIENTS ####################################

# 设置客户端默认最大连接数，设置为0表示不限制，默认为10000
# maxclients 10000

############################## MEMORY MANAGEMENT ################################

# 设置 redis 最大内存容量
# maxmemory <bytes>

# 内存达到上限的处理策略（lru means Least Recently Used<最近最少>,lfu means Least Frequently Used<最不常>）
# volatile-lru -> 利用LRU算法移除设置过过期时间的key
# allkeys-lru -> 利用LRU算法移除任何key
# volatile-lfu -> 利用LFU算法移除设置过过期时间的key
# allkeys-lfu -> 利用LFU算法移除任何key
# volatile-random -> 随机移除设置过过期时间的key
# allkeys-random -> 随机移除所有key
# volatile-ttl -> 移除即将过期的key
# noeviction -> 不移除key，返回报错就行
# 默认为不移除key策略
# maxmemory-policy noeviction

# 设置每次移除时的样本大小，默认5个：每次移除时选取5个样本，移除其中符合策略的key
# maxmemory-samples 5

# 从节点是否忽略maxmemory设置的值
# replica-ignore-maxmemory yes

############################# LAZY FREEING ####################################

# Redis has two primitives to delete keys. One is called DEL and is a blocking
# deletion of the object. It means that the server stops processing new commands
# in order to reclaim all the memory associated with an object in a synchronous
# way. If the key deleted is associated with a small object, the time needed
# in order to execute the DEL command is very small and comparable to most other
# O(1) or O(log_N) commands in Redis. However if the key is associated with an
# aggregated value containing millions of elements, the server can block for
# a long time (even seconds) in order to complete the operation.
#
# For the above reasons Redis also offers non blocking deletion primitives
# such as UNLINK (non blocking DEL) and the ASYNC option of FLUSHALL and
# FLUSHDB commands, in order to reclaim memory in background. Those commands
# are executed in constant time. Another thread will incrementally free the
# object in the background as fast as possible.
#
# DEL, UNLINK and ASYNC option of FLUSHALL and FLUSHDB are user-controlled.
# It's up to the design of the application to understand when it is a good
# idea to use one or the other. However the Redis server sometimes has to
# delete keys or flush the whole database as a side effect of other operations.
# Specifically Redis deletes objects independently of a user call in the
# following scenarios:
#
# 1) On eviction, because of the maxmemory and maxmemory policy configurations,
#    in order to make room for new data, without going over the specified
#    memory limit.
# 2) Because of expire: when a key with an associated time to live (see the
#    EXPIRE command) must be deleted from memory.
# 3) Because of a side effect of a command that stores data on a key that may
#    already exist. For example the RENAME command may delete the old key
#    content when it is replaced with another one. Similarly SUNIONSTORE
#    or SORT with STORE option may delete existing keys. The SET command
#    itself removes any old content of the specified key in order to replace
#    it with the specified string.
# 4) During replication, when a replica performs a full resynchronization with
#    its master, the content of the whole database is removed in order to
#    load the RDB file just transferred.
#
# In all the above cases the default is to delete objects in a blocking way,
# like if DEL was called. However you can configure each case specifically
# in order to instead release memory in a non-blocking way like if UNLINK
# was called, using the following configuration directives:

lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no

############################## APPEND ONLY MODE ###############################

# 指定是否在每次更新操作后进行日志记录，redis默认异步将数据写入磁盘，不开启会导致断电时一段时间内的数据丢失，默认no
appendonly no

# 指定更新日志文件名，默认为appendonly.aof
appendfilename "appendonly.aof"

# 指定更新日志条件，建议使用 "everysec"
# "always"：同步持久化，每次发生数据更改会立即调用fsync()方法将数据写入磁盘，数据完整性好，性能差# "everysec"：默认值，异步同步，每秒同步一次，若一秒内宕机，则存在数据丢失# "no"：永不
# appendfsync always
appendfsync everysec
# appendfsync no

# 重写时，是否可以使用appendfsync（接受客户端的写操作），默认为 no ，保证数据安全性
no-appendfsync-on-rewrite no

# redis会记录上次重写AOF文件的大小，当AOF文件增加至上次重写文件的 100% 倍，且大小大于64MB时，触发AOF重写# auto-aof-rewrite-percentage 用于设置相对于上次AOF文件百分比，auto-aof-rewrite-min-size 用于设置另一基准值
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# An AOF file may be found to be truncated at the end during the Redis
# startup process, when the AOF data gets loaded back into memory.
# This may happen when the system where Redis is running
# crashes, especially when an ext4 filesystem is mounted without the
# data=ordered option (however this can't happen when Redis itself
# crashes or aborts but the operating system still works correctly).
#
# Redis can either exit with an error when this happens, or load as much
# data as possible (the default now) and start if the AOF file is found
# to be truncated at the end. The following option controls this behavior.
#
# If aof-load-truncated is set to yes, a truncated AOF file is loaded and
# the Redis server starts emitting a log to inform the user of the event.
# Otherwise if the option is set to no, the server aborts with an error
# and refuses to start. When the option is set to no, the user requires
# to fix the AOF file using the "redis-check-aof" utility before to restart
# the server.
#
# Note that if the AOF file will be found to be corrupted in the middle
# the server will still exit with an error. This option only applies when
# Redis will try to read more data from the AOF file but not enough bytes
# will be found.
aof-load-truncated yes

# When rewriting the AOF file, Redis is able to use an RDB preamble in the
# AOF file for faster rewrites and recoveries. When this option is turned
# on the rewritten AOF file is composed of two different stanzas:
#
#   [RDB file][AOF tail]
#
# When loading Redis recognizes that the AOF file starts with the "REDIS"
# string and loads the prefixed RDB file, and continues loading the AOF
# tail.
aof-use-rdb-preamble yes

################################ LUA SCRIPTING  ###############################

# Max execution time of a Lua script in milliseconds.
#
# If the maximum execution time is reached Redis will log that a script is
# still in execution after the maximum allowed time and will start to
# reply to queries with an error.
#
# When a long running script exceeds the maximum execution time only the
# SCRIPT KILL and SHUTDOWN NOSAVE commands are available. The first can be
# used to stop a script that did not yet called write commands. The second
# is the only way to shut down the server in the case a write command was
# already issued by the script but the user doesn't want to wait for the natural
# termination of the script.
#
# Set it to 0 or a negative value for unlimited execution without warnings.
lua-time-limit 5000

################################ REDIS CLUSTER  ###############################
#
# ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
# WARNING EXPERIMENTAL: Redis Cluster is considered to be stable code, however
# in order to mark it as "mature" we need to wait for a non trivial percentage
# of users to deploy it in production.
# ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#
# Normal Redis instances can't be part of a Redis Cluster; only nodes that are
# started as cluster nodes can. In order to start a Redis instance as a
# cluster node enable the cluster support uncommenting the following:
#
# cluster-enabled yes

# Every cluster node has a cluster configuration file. This file is not
# intended to be edited by hand. It is created and updated by Redis nodes.
# Every Redis Cluster node requires a different cluster configuration file.
# Make sure that instances running in the same system do not have
# overlapping cluster configuration file names.
#
# cluster-config-file nodes-6379.conf

# Cluster node timeout is the amount of milliseconds a node must be unreachable
# for it to be considered in failure state.
# Most other internal time limits are multiple of the node timeout.
#
# cluster-node-timeout 15000

# A replica of a failing master will avoid to start a failover if its data
# looks too old.
#
# There is no simple way for a replica to actually have an exact measure of
# its "data age", so the following two checks are performed:
#
# 1) If there are multiple replicas able to failover, they exchange messages
#    in order to try to give an advantage to the replica with the best
#    replication offset (more data from the master processed).
#    Replicas will try to get their rank by offset, and apply to the start
#    of the failover a delay proportional to their rank.
#
# 2) Every single replica computes the time of the last interaction with
#    its master. This can be the last ping or command received (if the master
#    is still in the "connected" state), or the time that elapsed since the
#    disconnection with the master (if the replication link is currently down).
#    If the last interaction is too old, the replica will not try to failover
#    at all.
#
# The point "2" can be tuned by user. Specifically a replica will not perform
# the failover if, since the last interaction with the master, the time
# elapsed is greater than:
#
#   (node-timeout * replica-validity-factor) + repl-ping-replica-period
#
# So for example if node-timeout is 30 seconds, and the replica-validity-factor
# is 10, and assuming a default repl-ping-replica-period of 10 seconds, the
# replica will not try to failover if it was not able to talk with the master
# for longer than 310 seconds.
#
# A large replica-validity-factor may allow replicas with too old data to failover
# a master, while a too small value may prevent the cluster from being able to
# elect a replica at all.
#
# For maximum availability, it is possible to set the replica-validity-factor
# to a value of 0, which means, that replicas will always try to failover the
# master regardless of the last time they interacted with the master.
# (However they'll always try to apply a delay proportional to their
# offset rank).
#
# Zero is the only value able to guarantee that when all the partitions heal
# the cluster will always be able to continue.
#
# cluster-replica-validity-factor 10

# Cluster replicas are able to migrate to orphaned masters, that are masters
# that are left without working replicas. This improves the cluster ability
# to resist to failures as otherwise an orphaned master can't be failed over
# in case of failure if it has no working replicas.
#
# Replicas migrate to orphaned masters only if there are still at least a
# given number of other working replicas for their old master. This number
# is the "migration barrier". A migration barrier of 1 means that a replica
# will migrate only if there is at least 1 other working replica for its master
# and so forth. It usually reflects the number of replicas you want for every
# master in your cluster.
#
# Default is 1 (replicas migrate only if their masters remain with at least
# one replica). To disable migration just set it to a very large value.
# A value of 0 can be set but is useful only for debugging and dangerous
# in production.
#
# cluster-migration-barrier 1

# By default Redis Cluster nodes stop accepting queries if they detect there
# is at least an hash slot uncovered (no available node is serving it).
# This way if the cluster is partially down (for example a range of hash slots
# are no longer covered) all the cluster becomes, eventually, unavailable.
# It automatically returns available as soon as all the slots are covered again.
#
# However sometimes you want the subset of the cluster which is working,
# to continue to accept queries for the part of the key space that is still
# covered. In order to do so, just set the cluster-require-full-coverage
# option to no.
#
# cluster-require-full-coverage yes

# This option, when set to yes, prevents replicas from trying to failover its
# master during master failures. However the master can still perform a
# manual failover, if forced to do so.
#
# This is useful in different scenarios, especially in the case of multiple
# data center operations, where we want one side to never be promoted if not
# in the case of a total DC failure.
#
# cluster-replica-no-failover no

# In order to setup your cluster make sure to read the documentation
# available at http://redis.io web site.

########################## CLUSTER DOCKER/NAT support  ########################

# In certain deployments, Redis Cluster nodes address discovery fails, because
# addresses are NAT-ted or because ports are forwarded (the typical case is
# Docker and other containers).
#
# In order to make Redis Cluster working in such environments, a static
# configuration where each node knows its public address is needed. The
# following two options are used for this scope, and are:
#
# * cluster-announce-ip
# * cluster-announce-port
# * cluster-announce-bus-port
#
# Each instruct the node about its address, client port, and cluster message
# bus port. The information is then published in the header of the bus packets
# so that other nodes will be able to correctly map the address of the node
# publishing the information.
#
# If the above options are not used, the normal Redis Cluster auto-detection
# will be used instead.
#
# Note that when remapped, the bus port may not be at the fixed offset of
# clients port + 10000, so you can specify any port and bus-port depending
# on how they get remapped. If the bus-port is not set, a fixed offset of
# 10000 will be used as usually.
#
# Example:
#
# cluster-announce-ip 10.1.1.5
# cluster-announce-port 6379
# cluster-announce-bus-port 6380

################################## SLOW LOG ###################################

# The Redis Slow Log is a system to log queries that exceeded a specified
# execution time. The execution time does not include the I/O operations
# like talking with the client, sending the reply and so forth,
# but just the time needed to actually execute the command (this is the only
# stage of command execution where the thread is blocked and can not serve
# other requests in the meantime).
#
# You can configure the slow log with two parameters: one tells Redis
# what is the execution time, in microseconds, to exceed in order for the
# command to get logged, and the other parameter is the length of the
# slow log. When a new command is logged the oldest one is removed from the
# queue of logged commands.

# The following time is expressed in microseconds, so 1000000 is equivalent
# to one second. Note that a negative number disables the slow log, while
# a value of zero forces the logging of every command.
slowlog-log-slower-than 10000

# There is no limit to this length. Just be aware that it will consume memory.
# You can reclaim memory used by the slow log with SLOWLOG RESET.
slowlog-max-len 128

################################ LATENCY MONITOR ##############################

# The Redis latency monitoring subsystem samples different operations
# at runtime in order to collect data related to possible sources of
# latency of a Redis instance.
#
# Via the LATENCY command this information is available to the user that can
# print graphs and obtain reports.
#
# The system only logs operations that were performed in a time equal or
# greater than the amount of milliseconds specified via the
# latency-monitor-threshold configuration directive. When its value is set
# to zero, the latency monitor is turned off.
#
# By default latency monitoring is disabled since it is mostly not needed
# if you don't have latency issues, and collecting data has a performance
# impact, that while very small, can be measured under big load. Latency
# monitoring can easily be enabled at runtime using the command
# "CONFIG SET latency-monitor-threshold <milliseconds>" if needed.
latency-monitor-threshold 0

############################# EVENT NOTIFICATION ##############################

# Redis can notify Pub/Sub clients about events happening in the key space.
# This feature is documented at http://redis.io/topics/notifications
#
# For instance if keyspace events notification is enabled, and a client
# performs a DEL operation on key "foo" stored in the Database 0, two
# messages will be published via Pub/Sub:
#
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
#
# It is possible to select the events that Redis will notify among a set
# of classes. Every class is identified by a single character:
#
#  K     Keyspace events, published with __keyspace@<db>__ prefix.
#  E     Keyevent events, published with __keyevent@<db>__ prefix.
#  g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
#  $     String commands
#  l     List commands
#  s     Set commands
#  h     Hash commands
#  z     Sorted set commands
#  x     Expired events (events generated every time a key expires)
#  e     Evicted events (events generated when a key is evicted for maxmemory)
#  A     Alias for g$lshzxe, so that the "AKE" string means all the events.
#
#  The "notify-keyspace-events" takes as argument a string that is composed
#  of zero or multiple characters. The empty string means that notifications
#  are disabled.
#
#  Example: to enable list and generic events, from the point of view of the
#           event name, use:
#
#  notify-keyspace-events Elg
#
#  Example 2: to get the stream of the expired keys subscribing to channel
#             name __keyevent@0__:expired use:
#
#  notify-keyspace-events Ex
#
#  By default all notifications are disabled because most users don't need
#  this feature and the feature has some overhead. Note that if you don't
#  specify at least one of K or E, no events will be delivered.
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################

# 指定在超过一定数据或者最大的元素超过某一临界值时，采用如下特殊哈希算法，未超过则使用ziplist
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

# Lists are also encoded in a special way to save a lot of space.
# The number of entries allowed per internal list node can be specified
# as a fixed maximum size or a maximum number of elements.
# For a fixed maximum size, use -5 through -1, meaning:
# -5: max size: 64 Kb  <-- not recommended for normal workloads
# -4: max size: 32 Kb  <-- not recommended
# -3: max size: 16 Kb  <-- probably not recommended
# -2: max size: 8 Kb   <-- good
# -1: max size: 4 Kb   <-- good
# Positive numbers mean store up to _exactly_ that number of elements
# per list node.
# The highest performing option is usually -2 (8 Kb size) or -1 (4 Kb size),
# but if your use case is unique, adjust the settings as necessary.
list-max-ziplist-size -2

# Lists may also be compressed.
# Compress depth is the number of quicklist ziplist nodes from *each* side of
# the list to *exclude* from compression.  The head and tail of the list
# are always uncompressed for fast push/pop operations.  Settings are:
# 0: disable all list compression
# 1: depth 1 means "don't start compressing until after 1 node into the list,
#    going from either the head or tail"
#    So: [head]->node->node->...->node->[tail]
#    [head], [tail] will always be uncompressed; inner nodes will compress.
# 2: [head]->[next]->node->node->...->node->[prev]->[tail]
#    2 here means: don't compress head or head->next or tail->prev or tail,
#    but compress all nodes between them.
# 3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]
# etc.
list-compress-depth 0

# Sets have a special encoding in just one case: when a set is composed
# of just strings that happen to be integers in radix 10 in the range
# of 64 bit signed integers.
# The following configuration setting sets the limit in the size of the
# set in order to use this special memory saving encoding.
set-max-intset-entries 512

# Similarly to hashes and lists, sorted sets are also specially encoded in
# order to save a lot of space. This encoding is only used when the length and
# elements of a sorted set are below the following limits:
zset-max-ziplist-entries 128
zset-max-ziplist-value 64

# HyperLogLog sparse representation bytes limit. The limit includes the
# 16 bytes header. When an HyperLogLog using the sparse representation crosses
# this limit, it is converted into the dense representation.
#
# A value greater than 16000 is totally useless, since at that point the
# dense representation is more memory efficient.
#
# The suggested value is ~ 3000 in order to have the benefits of
# the space efficient encoding without slowing down too much PFADD,
# which is O(N) with the sparse encoding. The value can be raised to
# ~ 10000 when CPU is not a concern, but space is, and the data set is
# composed of many HyperLogLogs with cardinality in the 0 - 15000 range.
hll-sparse-max-bytes 3000

# Streams macro node max size / items. The stream data structure is a radix
# tree of big nodes that encode multiple items inside. Using this configuration
# it is possible to configure how big a single node can be in bytes, and the
# maximum number of items it may contain before switching to a new node when
# appending new stream entries. If any of the following settings are set to
# zero, the limit is ignored, so for instance it is possible to set just a
# max entires limit by setting max-bytes to 0 and max-entries to the desired
# value.
stream-node-max-bytes 4096
stream-node-max-entries 100

# 指定是否激活重置哈希，默认为开启。对实时性要求高的，可更改为no，不开启
activerehashing yes

# The client output buffer limits can be used to force disconnection of clients
# that are not reading data from the server fast enough for some reason (a
# common reason is that a Pub/Sub client can't consume messages as fast as the
# publisher can produce them).
#
# The limit can be set differently for the three different classes of clients:
#
# normal -> normal clients including MONITOR clients
# replica  -> replica clients
# pubsub -> clients subscribed to at least one pubsub channel or pattern
#
# The syntax of every client-output-buffer-limit directive is the following:
#
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
#
# A client is immediately disconnected once the hard limit is reached, or if
# the soft limit is reached and remains reached for the specified number of
# seconds (continuously).
# So for instance if the hard limit is 32 megabytes and the soft limit is
# 16 megabytes / 10 seconds, the client will get disconnected immediately
# if the size of the output buffers reach 32 megabytes, but will also get
# disconnected if the client reaches 16 megabytes and continuously overcomes
# the limit for 10 seconds.
#
# By default normal clients are not limited because they don't receive data
# without asking (in a push way), but just after a request, so only
# asynchronous clients may create a scenario where data is requested faster
# than it can read.
#
# Instead there is a default limit for pubsub and replica clients, since
# subscribers and replicas receive data in a push fashion.
#
# Both the hard or the soft limit can be disabled by setting them to zero.
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

# Client query buffers accumulate new commands. They are limited to a fixed
# amount by default in order to avoid that a protocol desynchronization (for
# instance due to a bug in the client) will lead to unbound memory usage in
# the query buffer. However you can configure it here if you have very special
# needs, such us huge multi/exec requests or alike.
#
# client-query-buffer-limit 1gb

# In the Redis protocol, bulk requests, that are, elements representing single
# strings, are normally limited ot 512 mb. However you can change this limit
# here.
#
# proto-max-bulk-len 512mb

# Redis calls an internal function to perform many background tasks, like
# closing connections of clients in timeout, purging expired keys that are
# never requested, and so forth.
#
# Not all tasks are performed with the same frequency, but Redis checks for
# tasks to perform according to the specified "hz" value.
#
# By default "hz" is set to 10. Raising the value will use more CPU when
# Redis is idle, but at the same time will make Redis more responsive when
# there are many keys expiring at the same time, and timeouts may be
# handled with more precision.
#
# The range is between 1 and 500, however a value over 100 is usually not
# a good idea. Most users should use the default of 10 and raise this up to
# 100 only in environments where very low latency is required.
hz 10

# Normally it is useful to have an HZ value which is proportional to the
# number of clients connected. This is useful in order, for instance, to
# avoid too many clients are processed for each background task invocation
# in order to avoid latency spikes.
#
# Since the default HZ value by default is conservatively set to 10, Redis
# offers, and enables by default, the ability to use an adaptive HZ value
# which will temporary raise when there are many connected clients.
#
# When dynamic HZ is enabled, the actual configured HZ will be used as
# as a baseline, but multiples of the configured HZ value will be actually
# used as needed once more clients are connected. In this way an idle
# instance will use very little CPU time while a busy instance will be
# more responsive.
dynamic-hz yes

# When a child rewrites the AOF file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
aof-rewrite-incremental-fsync yes

# When redis saves RDB file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
rdb-save-incremental-fsync yes

# Redis LFU eviction (see maxmemory setting) can be tuned. However it is a good
# idea to start with the default settings and only change them after investigating
# how to improve the performances and how the keys LFU change over time, which
# is possible to inspect via the OBJECT FREQ command.
#
# There are two tunable parameters in the Redis LFU implementation: the
# counter logarithm factor and the counter decay time. It is important to
# understand what the two parameters mean before changing them.
#
# The LFU counter is just 8 bits per key, it's maximum value is 255, so Redis
# uses a probabilistic increment with logarithmic behavior. Given the value
# of the old counter, when a key is accessed, the counter is incremented in
# this way:
#
# 1. A random number R between 0 and 1 is extracted.
# 2. A probability P is calculated as 1/(old_value*lfu_log_factor+1).
# 3. The counter is incremented only if R < P.
#
# The default lfu-log-factor is 10. This is a table of how the frequency
# counter changes with a different number of accesses with different
# logarithmic factors:
#
# +--------+------------+------------+------------+------------+------------+
# | factor | 100 hits   | 1000 hits  | 100K hits  | 1M hits    | 10M hits   |
# +--------+------------+------------+------------+------------+------------+
# | 0      | 104        | 255        | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 1      | 18         | 49         | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 10     | 10         | 18         | 142        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 100    | 8          | 11         | 49         | 143        | 255        |
# +--------+------------+------------+------------+------------+------------+
#
# NOTE: The above table was obtained by running the following commands:
#
#   redis-benchmark -n 1000000 incr foo
#   redis-cli object freq foo
#
# NOTE 2: The counter initial value is 5 in order to give new objects a chance
# to accumulate hits.
#
# The counter decay time is the time, in minutes, that must elapse in order
# for the key counter to be divided by two (or decremented if it has a value
# less <= 10).
#
# The default value for the lfu-decay-time is 1. A Special value of 0 means to
# decay the counter every time it happens to be scanned.
#
# lfu-log-factor 10
# lfu-decay-time 1

########################### ACTIVE DEFRAGMENTATION #######################
#
# WARNING THIS FEATURE IS EXPERIMENTAL. However it was stress tested
# even in production and manually tested by multiple engineers for some
# time.
#
# What is active defragmentation?
# -------------------------------
#
# Active (online) defragmentation allows a Redis server to compact the
# spaces left between small allocations and deallocations of data in memory,
# thus allowing to reclaim back memory.
#
# Fragmentation is a natural process that happens with every allocator (but
# less so with Jemalloc, fortunately) and certain workloads. Normally a server
# restart is needed in order to lower the fragmentation, or at least to flush
# away all the data and create it again. However thanks to this feature
# implemented by Oran Agra for Redis 4.0 this process can happen at runtime
# in an "hot" way, while the server is running.
#
# Basically when the fragmentation is over a certain level (see the
# configuration options below) Redis will start to create new copies of the
# values in contiguous memory regions by exploiting certain specific Jemalloc
# features (in order to understand if an allocation is causing fragmentation
# and to allocate it in a better place), and at the same time, will release the
# old copies of the data. This process, repeated incrementally for all the keys
# will cause the fragmentation to drop back to normal values.
#
# Important things to understand:
#
# 1. This feature is disabled by default, and only works if you compiled Redis
#    to use the copy of Jemalloc we ship with the source code of Redis.
#    This is the default with Linux builds.
#
# 2. You never need to enable this feature if you don't have fragmentation
#    issues.
#
# 3. Once you experience fragmentation, you can enable this feature when
#    needed with the command "CONFIG SET activedefrag yes".
#
# The configuration parameters are able to fine tune the behavior of the
# defragmentation process. If you are not sure about what they mean it is
# a good idea to leave the defaults untouched.

# Enabled active defragmentation
# activedefrag yes

# Minimum amount of fragmentation waste to start active defrag
# active-defrag-ignore-bytes 100mb

# Minimum percentage of fragmentation to start active defrag
# active-defrag-threshold-lower 10

# Maximum percentage of fragmentation at which we use maximum effort
# active-defrag-threshold-upper 100

# Minimal effort for defrag in CPU percentage
# active-defrag-cycle-min 5

# Maximal effort for defrag in CPU percentage
# active-defrag-cycle-max 75

# Maximum number of set/hash/zset/list fields that will be processed from
# the main dictionary scan
# active-defrag-max-scan-fields 1000
```