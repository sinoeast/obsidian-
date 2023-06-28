## 常用管理命令

## 1、启动Redis

```text
> redis-server [--port 6379]
```

如果命令参数过多，建议通过配置文件来启动Redis。

```text
> redis-server [xx/xx/redis.conf]
```

6379是Redis默认端口号。

## 2、连接Redis

```text
> ./redis-cli [-h 127.0.0.1 -p 6379]
```

## 3、停止Redis

```text
> redis-cli shutdown

> kill redis-pid
```

以上两条停止Redis命令效果一样。

## 4、发送命令

给Redis发送命令有两种方式：

1、redis-cli带参数运行，如：

```text
> redis-cli shutdown
not connected>
```

这样默认是发送到本地的6379端口。

2、redis-cli不带参数运行，如：

```text
> ./redis-cli

127.0.0.1:6379> shutdown
not connected>
```

## 5、测试连通性

```text
127.0.0.1:6379> ping
PONG
```

# key操作命令
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623210238.png)
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623163914.png)

## 获取所有键

> 语法：keys pattern  

```text
127.0.0.1:6379> keys *
1) "javastack"
```

-   *表示通配符，表示任意字符，会遍历所有键显示所有的键列表，时间复杂度O(n)，在生产环境不建议使用。

## 获取键总数

> 语法：dbsize  

```text
127.0.0.1:6379> dbsize
(integer) 6
```

获取键总数时不会遍历所有的键，直接获取内部变量，时间复杂度O(1)。

## 查询键是否存在

> 语法：exists key [key ...]  

```text
127.0.0.1:6379> exists javastack java
(integer) 2
```

查询查询多个，返回存在的个数。

## 删除键

> 语法：del key [key ...]  

```text
127.0.0.1:6379> del java javastack
(integer) 1
```

可以删除多个，返回删除成功的个数。

## 查询键类型

> 语法： type key  

```text
127.0.0.1:6379> type javastack
string
```

## 移动键

> 语法：move key db  

如把javastack移到2号数据库。

```text
127.0.0.1:6379> move javastack 2
(integer) 1
127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]> keys *
1) "javastack"
```

## 查询key的生命周期（秒）

> 秒语法：ttl key  
> 毫秒语法：pttl key  

```text
127.0.0.1:6379[2]> ttl javastack
(integer) -1
```

-1：永远不过期。

## 设置过期时间

> 秒语法：expire key seconds  
> 毫秒语法：pexpire key milliseconds  

```text
127.0.0.1:6379[2]> expire javastack 60
(integer) 1
127.0.0.1:6379[2]> ttl javastack
(integer) 55
```

## 设置永不过期

> 语法：persist key  

```text
127.0.0.1:6379[2]> persist javastack
(integer) 1
```

## 更改键名称

> 语法：rename key newkey  

```text
127.0.0.1:6379[2]> rename javastack javastack123
OK
```

# String操作命令

字符串是Redis中最基本的数据类型，单个数据能存储的最大空间是512M。
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623163842.png)

## 存放键值

> 语法：set key value [EX seconds] [PX milliseconds] [NX|XX]  

	nx：如果key不存在则建立，xx：如果key存在则修改其值，也可以直接使用setnx/setex命令。

```text
127.0.0.1:6379> set javastack 666
OK
```

## 获取键值

> 语法：get key  

```text
127.0.0.1:6379[2]> get javastack
"666"
```

## 值递增/递减

如果字符串中的值是数字类型的，可以使用incr命令每次递增，不是数字类型则报错。

> 语法：incr key  

```text
127.0.0.1:6379[2]> incr javastack
(integer) 667
```

一次想递增N用incrby命令，如果是浮点型数据可以用incrbyfloat命令递增。

同样，递减使用decr、decrby命令。

## 批量存放键值

> 语法：mset key value [key value ...]  

```text
127.0.0.1:6379[2]> mset java1 1 java2 2 java3 3
OK
```

## 获取获取键值

> 语法：mget key [key ...]  

```text
127.0.0.1:6379[2]> mget java1 java2
1) "1"
2) "2"
```

Redis接收的是UTF-8的编码，如果是中文一个汉字将占3位返回。

## 获取值长度

> 语法：strlen key  
> 127.0.0.1:6379[2]> strlen javastack (integer) 3  

## 追加内容

> 语法：append key value  

```text
127.0.0.1:6379[2]> append javastack hi
(integer) 5
```

向键值尾部添加，如上命令执行后由666变成666hi

## 获取部分字符

> 语法：getrange key start end  

```text
> 127.0.0.1:6379[2]> getrange javastack 0 4
"javas"
```

# set操作命令

集合类型和列表类型相似，只不过是集合是无序且不可重复的。
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623164552.png)

## 集合

## 存储值

> 语法：sadd key member [member ...]  

```text
// 这里有8个值（2个java），只存了7个
127.0.0.1:6379> sadd langs java php c++ go ruby python kotlin java
(integer) 7
```

## 获取元素

> 获取所有元素语法：smembers key  

```text
127.0.0.1:6379> smembers langs
1) "php"
2) "kotlin"
3) "c++"
4) "go"
5) "ruby"
6) "python"
7) "java"
```

> 随机获取语法：srandmember langs count  

```text
127.0.0.1:6379> srandmember langs 3
1) "c++"
2) "java"
3) "php"
```

## 判断集合是否存在元素

> 语法：sismember key member  

```text
127.0.0.1:6379> sismember langs go
(integer) 1
```

## 获取集合元素个数

> 语法：scard key  

```text
127.0.0.1:6379> scard langs
(integer) 7
```

## 删除集合元素

> 语法：srem key member [member ...]  

```text
127.0.0.1:6379> srem langs ruby kotlin
(integer) 2
```

## 弹出元素

> 语法：spop key [count]  

```text
127.0.0.1:6379> spop langs 2
1) "go"
2) "java"
```

# SortedSet有序集合

和列表的区别：

1、列表使用链表实现，两头快，中间慢。有序集合是散列表和跳跃表实现的，即使读取中间的元素也比较快。

2、列表不能调整元素位置，有序集合能。

3、有序集合比列表更占内存。
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623164507.png)

## 存储值

> 语法：zadd key [NX|XX] [CH] [INCR] score member [score member ...]  

```text
127.0.0.1:6379> zadd footCounts 16011 tid 20082 huny 2893 nosy
(integer) 3
```

## 获取元素分数

> 语法：zscore key member  

```text
127.0.0.1:6379> zscore footCounts tid
"16011"
```

> 获取排名范围排名语法：zrange key start stop [WITHSCORES]  

```text
// 获取所有，没有分数
127.0.0.1:6379> zrange footCounts 0 -1
1) "nosy"
2) "tid"
3) "huny"

// 获取所有及分数
127.0.0.1:6379> zrange footCounts 0 -1 Withscores
1) "nosy"
2) "2893"
3) "tid"
4) "16011"
5) "huny"
6) "20082"
```

> 获取指定分数范围排名语法：zrangebyscore key min max [WITHSCORES] [LIMIT offset count]  

```text
127.0.0.1:6379> zrangebyscore footCounts 3000 30000 withscores limit 0 1
1) "tid"
2) "16011"
```

## 增加指定元素分数

> 语法：zincrby key increment member  

```text
127.0.0.1:6379> zincrby footCounts 2000 tid
"18011"
```

## 获取集合元素个数

> 语法：zcard key  

```text
127.0.0.1:6379> zcard footCounts
(integer) 3
```

## 获取指定范围分数个数

> 语法：zcount key min max  

```text
127.0.0.1:6379> zcount footCounts 2000 20000
(integer) 2
```

## 删除指定元素

> 语法：zrem key member [member ...]  

```text
127.0.0.1:6379> zrem footCounts huny
(integer) 1
```

## 获取元素排名

> 语法：zrank key member  

```text
127.0.0.1:6379> zrank footCounts tid
(integer) 1
```

# List操作命令

列表类型是一个有序的字段串列表，内部是使用双向链表实现，所有可以向两端操作元素，获取两端的数据速度快，通过索引到具体的行数比较慢。

列表类型的元素是有序且可以重复的。
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623164003.png)

## 存储值

> 左端存值语法：lpush key value [value ...]  

```text
127.0.0.1:6379> lpush list lily sandy
(integer) 2
```

> 右端存值语法：rpush key value [value ...]  

```text
127.0.0.1:6379> rpush list tom kitty
(integer) 4
```

> 索引存值语法：lset key index value  

```text
127.0.0.1:6379> lset list 3 uto
OK
```

## 弹出元素

> 左端弹出语法：lpop key  

```text
127.0.0.1:6379> lpop list
"sandy"
```

> 右端弹出语法：rpop key  

```text
127.0.0.1:6379> rpop list
"kitty"
```

## 获取元素个数

> 语法：llen key  

```text
127.0.0.1:6379> llen list
(integer) 2
```

## 获取列表元素

> 两边获取语法：lrange key start stop  

```text
127.0.0.1:6379> lpush users tom kitty land pony jack maddy
(integer) 6

127.0.0.1:6379> lrange users 0 3
1) "maddy"
2) "jack"
3) "pony"
4) "land"

// 获取所有
127.0.0.1:6379> lrange users 0 -1
1) "maddy"
2) "jack"
3) "pony"
4) "land"
5) "kitty"
6) "tom"

// 从右端索引
127.0.0.1:6379> lrange users -3 -1
1) "land"
2) "kitty"
3) "tom"
```

> 索引获取语法：lindex key index  

```text
127.0.0.1:6379> lindex list 2
"ketty"

// 从右端获取
127.0.0.1:6379> lindex list -5
"sady"
```

## 删除元素

> 根据值删除语法：lrem key count value  

```text
127.0.0.1:6379> lpush userids 111 222 111 222 222 333 222 222
(integer) 8

// count=0 删除所有
127.0.0.1:6379> lrem userids 0 111
(integer) 2

// count > 0 从左端删除前count个
127.0.0.1:6379> lrem userids 3 222
(integer) 3

// count < 0 从右端删除前count个
127.0.0.1:6379> lrem userids -3 222
(integer) 2
```

> 范围删除语法：ltrim key start stop  

```text
// 只保留2-4之间的元素
127.0.0.1:6379> ltrim list 2 4
OK
```

# hash操作命令

redis字符串类型键和值是字典结构形式，这里的散列类型其值也可以是字典结构。
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623163824.png)

## 存放键值

> 单个语法：hset key field value  

```text
127.0.0.1:6379> hset user name javastack
(integer) 1
```

> 多个语法：hmset key field value [field value ...]  

```text
127.0.0.1:6379> hmset user name javastack age 20 address china
OK
```

> 不存在时语法：hsetnx key field value  

```text
127.0.0.1:6379> hsetnx user tall 180
(integer) 0
```

## 获取字段值

> 单个语法：hget key field  

```text
127.0.0.1:6379> hget user age
"20"
```

> 多个语法：hmget key field [field ...]  

```text
127.0.0.1:6379> hmget user name age address
1) "javastack"
2) "20"
3) "china"
```

> 获取所有键与值语法：hgetall key  

```text
127.0.0.1:6379> hgetall user
1) "name"
2) "javastack"
3) "age"
4) "20"
5) "address"
6) "china"
```

> 获取所有字段语法：hkeys key  

```text
127.0.0.1:6379> hkeys user
1) "name"
2) "address"
3) "tall"
4) "age"
```

> 获取所有值语法：hvals key  

```text
127.0.0.1:6379> hvals user
1) "javastack"
2) "china"
3) "170"
4) "20"
```

## 判断字段是否存在

> 语法：hexists key field  

```text
127.0.0.1:6379> hexists user address
(integer) 1
```

## 获取字段数量

> 语法：hlen key  

```text
127.0.0.1:6379> hlen user
(integer) 4
```

## 递增/减

> 语法：hincrby key field increment  

```text
127.0.0.1:6379> hincrby user tall -10
(integer) 170
```

## 删除字段

> 语法：hdel key field [field ...]  

```text
127.0.0.1:6379> hdel user age
(integer) 1
```

都是基本的命令用法，不会用了就来翻一下吧！

# Value设计
![image.png](https://gitee.com/sinoeast/imgs/raw/master/20230623210103.png)
# Redis事务[拓展]

一个事务从开始到执行会经历以下三个阶段：

- 开始事务。
    
- 命令入队。
    
- 执行事务。
    

借用：菜鸟教程中例子

它先以 **MULTI** 开始一个事务， 然后将多个命令入队到事务中， 最后由 **EXEC** 命令触发事务， 一并执行事务中的所有命令：

redis 127.0.0.1:6379> MULTI  
OK  
​  
redis 127.0.0.1:6379> SET book-name "Mastering C++ in 21 days"  
QUEUED  
​  
redis 127.0.0.1:6379> GET book-name  
QUEUED  
​  
redis 127.0.0.1:6379> SADD tag "C++" "Programming" "Mastering Series"  
QUEUED  
​  
redis 127.0.0.1:6379> SMEMBERS tag  
QUEUED  
​  
redis 127.0.0.1:6379> EXEC  
1) OK  
2) "Mastering C++ in 21 days"  
3) (integer) 3  
4) 1) "Mastering Series"  
   2) "C++"  
   3) "Programming"

单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加任何维持原子性的机制，所以 Redis 事务的执行并不是原子性的。

Redis事务可以理解为一个**打包的批量执行脚本**，但批量指令并非原子化的操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做。

127.0.0.1:6379> multi  
OK  
127.0.0.1:6379(TX)> set name dafei  
QUEUED  
127.0.0.1:6379(TX)> set age 18  
QUEUED  
127.0.0.1:6379(TX)> incr age   
QUEUED  
127.0.0.1:6379(TX)> incr name  
QUEUED  
127.0.0.1:6379(TX)> get age  
QUEUED  
127.0.0.1:6379(TX)> get name  
QUEUED  
127.0.0.1:6379(TX)> exec  
1) OK  
2) OK  
3) (integer) 19  
4) (error) ERR value is not an integer or out of range  
5) "19"  
6) "dafei"  
127.0.0.1:6379> 

Redis 事务可以一次执行多个命令， 并且带有以下三个重要的保证：

- 批量操作在发送 EXEC 命令前被放入队列缓存。
    
- 收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
    
- 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。
    

# Redis持久化机制[拓展]

## 引言

先来一个小实验，大家可以一起实现一下

步骤1：在Redis中添加2个key-value对

127.0.0.1:6379> set aa aa  
OK  
127.0.0.1:6379> set bb bb  
OK  
127.0.0.1:6379> keys *

步骤2：重启Redis 服务器，在执行keys * 观察数据

步骤3：分析结果

会出现一下几种结果：

- 之前的key在，aa bb 都在（最理想的结果）
    
- 之前的key在，aa也在，bb不见了
    
- 之前的key在，aa， bb 不在
    
- 之前的key， aa， bb 都不在了（最坏的结果）
    

思考：

为啥会这样？以我们对内存的操作理解，按道理重启之后数据应该全丢失了，为啥Redis 可能丢失，也可能不丢失，为何？

这里就涉及到Redis的持久化机制了。

![image-20220902113153504](file://C:\Users\sinoe\Downloads\Redis\课件\images\image-20220902113153504.png?lastModify=1687965926)*

Redis持久化机制目前以后3种，分别为：

1>**快照方式**（RDB, Redis DataBase）

2>**文件追加方式**（AOF, Append Only File）

3>**混合持久化方式**（Redis4版本之后）

## RDB方式

Snapshotting(快照)默认方式，将内存数据中以快照的方式写入到二进制文件中，默认为dump.rdb。触发RDB持久化过程分手动触发与自动触发。

### 触发机制

**手动触发**

使用save命令：会阻塞当前Redis服务器，知道RDB过程完成为主，如果内存数据较多，会造成长时间阻塞，影响其他命令的使用，不建议轻易使用

使用bgsave命令：Redis进程执行fork指令创建子进程，由子进程实现RDB持久化，有需要时建议使用bgsave命令。

**自动触发**

使用save相关配置，格式： save m n 表示m秒内数据集存在n次修改时会自动触发bgsave命令。

save 900 1  #900秒内如果超过1个Key被修改则发起快照保存  
save 300 10 #300秒内如果超过10个key被修改,则发起快照保存  
save 60 10000

![image-20220902151151422](file://C:\Users\sinoe\Downloads\Redis\课件\images\image-20220902151151422.png?lastModify=1687965926)*

### 优点

- RDB快照文件是一个紧凑压缩的二进制文件，非常使用用于备份，全量复制等场景。开发中可以按照每6小时执行一次bgsave备份，用于容灾备份。
    
- Redis加载RDB恢复数据远远快于AOF方式
    

### 缺点

- RDB无法做到实时持久化/秒级持久化，每次bgsave时都需要fork子进程，频繁执行有时间成本。
    
- RDB快照文件不同版本格式不一样，容易引起兼容问题。
    

## AOF方式

AOF与RDB不一样，它是一独立日志的方式记录每次写命令，重启时再重新执行AOF文件中命令达到恢复数据的目的。解决了数据持久化的实时性的问题。

Redis默认是不开启的，需要使用时，需要配置： **appendonly yes**

AOF 有3种文件同步策略

|策略|解释|
|---|---|
|appendfsync always|收到命令就立即写到磁盘,效率最慢.但是能保证完全的持久化|
|appendfsync everysec|每秒写入磁盘一次,在性能和持久化方面做了很好的折中|
|appendfsync no|完全以依赖os，一般同步周期是30秒|

![image-20220902152951385](file://C:\Users\sinoe\Downloads\Redis\课件\images\image-20220902152951385.png?lastModify=1687965926)*

### 优点

- AOF方式数据安全性更高，配置得当，最多损失1秒的数据量
    
- 在不小心执行flushall命令，也可以通过AOF方式恢复(删除最后一个命令即可)
    
- AOF 日志是一个增量日志文件，不会存在断电时出现损坏问题。即使出现问题，redis-check-aof 工具也能够轻松修复它。
    
- 当 AOF 变得太大时，Redis 能够在后台自动重写 AOF
    

### 缺点

- 相同数据量来说，AOF文件体积通常大于RDB文件
    
- 数据持久化性能上来说，AOF 比 RDB 慢
    

## RDB-AOF混合方式

混合持久化是结合了 RDB 和 AOF 的优点，在写入的时候，先把当前的数据以 RDB 的形式写入文件的开头，再将后续的操作命令以 AOF 的格式存入文件。即以 RDB 作为全量备份，AOF 作为增量备份，来提高备份效率。这样既能保证 Redis 重启时的速度，又能防止数据丢失的风险， 这就是 Redis 4.0 之后推出的 **RDB-AOF 混合持久化模式，其作为默认配置来使用**。

## 持久化机制选择

- 如果对数据安全性有非常高的要求，建议 RDB 和 AOF 同时启用。
    
- 如果对数据安全性要求不是很高，能够容忍数据的丢失，建议单独使用 RDB。
    
- 不推荐单独使用 AOF，因为对于进行数据库备份、更快重启以及 AOF 引擎中出现错误的情况来说，RDB 是更好的选择。
    
- 如果没特殊要求，Redis又是4.x版本以上，可以选择RDB-AOF混合方式。
    

如果不是混合模式，而是普通的RDB与AOF一起启动时，Redis加载数据执行流程

![image-20220902153031549](file://C:\Users\sinoe\Downloads\Redis\课件\images\image-20220902153031549.png?lastModify=1687965926)*

# Redis内存淘汰机制[拓展]

## 引言

Redis 启动会加载一个配置：

maxmemory <byte>   //内存上限

默认值为 0 (window版的限制为100M)，表示默认设置Redis内存上限。但是真实开发还是需要提前评估key的体量，提前设置好内容上限。

此时思考一个问题，开发中，在设置完内存上限之后，如果Redis key达到上限了，该怎么办？这就设置到Redis的内存淘汰机制了。

## 内存淘汰算法

Redis内存淘汰机制也可以称之为key内卷机制，当资源不足时，该如何选择？

常见的内存淘汰机制分为四大类：

- **LRU：**LRU是Least recently used，最近最少使用的意思，简单的理解就是从数据库中删除最近最少访问的数据，该算法认为，你长期不用的数据，那么被再次访问的概率也就很小了，淘汰的数据为最长时间没有被使用，仅与时间相关。
    

![image-20220902161050652](file://C:\Users\sinoe\Downloads\Redis\课件\images\image-20220902161050652.png?lastModify=1687965926)

- **LFU：**LFU是Least Frequently Used，最不经常使用的意思，简单的理解就是淘汰一段时间内，使用次数最少的数据，这个与频次和时间相关。
    
    ![image-20220902161057116](file://C:\Users\sinoe\Downloads\Redis\课件\images\image-20220902161057116.png?lastModify=1687965926)
    
- **TTL：**Redis中，有的数据是设置了过期时间的，而设置了过期时间的这部分数据，就是该算法要解决的对象。如果你快过期了，不好意思，我内存现在不够了，反正你也要退休了，提前送你一程，把你干掉吧。
    
- 随机淘汰：生死有命，富贵在天，是否被干掉，全凭天意了。
    

## Redis淘汰策略

Redis 通过配置

maxmemroy-policy  

来配置指定具体的淘汰机制，可供选择的值有：

　通过maxmemroy-policy可以配置具体的淘汰机制，看了网上很多文章说只有6种，其实有8种，可以看Redis5.0的配置文件，上面有说明：

- volatile-lru -> 找出已经设置过期时间的数据集，将最近最少使用（被访问到）的数据干掉。
    
- volatile-ttl -> 找出已经设置过期时间的数据集，将即将过期的数据干掉。
    
- volatile-random -> 找出已经设置过期时间的数据集，进行无差别攻击，随机干掉数据。
    
- volatile-lfu -> 找出已经设置过期时间的数据集，将一段时间内，使用次数最少的数据干掉。
    

- allkeys-lru ->与第1个差不多，数据集从设置过期时间数据变为全体数据。
    
- allkeys-lfu -> 与第4个差不多，数据集从设置过期时间数据变为全体数据。
    
- allkeys-random -> 与第3个差不多，数据集从设置过期时间数据变为全体数据。
    

- no-enviction -> 什么都不干，报错，告诉你内存不足，这样的好处是可以保证数据不丢失
    

**系统默认选择： noenviction**

# 过期Key处理[拓展]

接下讨论一个问题：Redis的key过期了，该如何清理问题。

Redis给出3种实现方案：

**惰性删除**：当访问Key时，才去判断它是否过期，如果过期，直接干掉。这种方式对CPU很友好，但是一个key如果长期不用，一直存在内存里，会造成内存浪费。

**定时删除**：设置键的过期时间的同时，创建一个定时器，当到达过期时间点，立即执行对Key的删除操作，这种方式对CPU不友好，得额外让出CPU维护定时器。

**定期删除**：隔一段时间，对数据进行一次检查，删除里面的过期Key，至于要删除多少过期Key，检查多少数据，则由算法决定。

**Redis服务器实际使用的是惰性删除和定期删除两种策略：通过配合使用这两种删除策略，可以很好地在合理使用CPU和避免浪费内存之间取得平衡。**