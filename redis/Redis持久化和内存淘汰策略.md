# redis进阶

参考：

​	<https://blog.csdn.net/liqingtx/article/details/60330555>  ==好文==

​	<https://www.cnblogs.com/PatrickLiu/p/8341951.html>  ==语法==

​	<https://blog.csdn.net/hjm4702192/article/details/80518856>  ==好文:缓存==

##单线程的redis为什么这么快

```
1.纯内存操作
2.单线程操作，避免了频繁的上下文切换 
3.采用了非阻塞I/O多路复用机制 
```

`扩展：`

​	**I/O多路复用机制** 



##redis存在的问题：

```
1.缓存和数据库双写一致性问题

2.缓存雪崩问题

3.缓存击穿问题

4.缓存的并发竞争问题
```







##过期策略

Redis采用的是  **定期删除+惰性删除策略** 

###定期删除：

​	redis        `默认1次/100ms`，  `随机`检查 `部分key`，删除过期的key。

​	`Tips`

​		1、因为是随机删除部分过期的key，所以会导致许多已经到期的key，由于未被扫描而继续存在。

 ### 惰性删除

​	在获取某个key时，redis会检查该key是否设置了过期时间？设置了且过期了就删除。 

**如果完美的错过上述策略，redis还提供一个内存淘汰机制：**

***

##内存淘汰机制：

在==redis.conf==配置文件中：

```
# 默认淘汰策略： noeviction
# maxmemory-policy noeviction
```

**redis共有6种淘汰策略：**

```
1）noeviction：当内存不足时，新写入操作会报错。。
2）allkeys-lru：当内存不足时，移除最近最少使用的key。推荐使用
3）allkeys-random：当内存不足时，随机移除某个key。
4）volatile-lru：当内存不足时，在设置了过期时间的键空间中，移除最近最少使用的key。这种情况一般 把 
   redis既当缓存，又做持久化存储的时候才用。不推荐
5）volatile-random：当内存不足时，在设置了过期时间的键空间中，随机移除某个key。依然不推荐
6）volatile-ttl：当内存不足时，在设置了过期时间的键空间中，有更早过期时间的key优先移除。不推荐
```

`Tips:`

​	**LRU算法:least RecentlyUsed，最近最少使用算法。也就是说默认删除最近最少使用的键。**

```
1.redis中并不会准确的删除所有键中最近最少使用的键，而是随机抽取5个键，删除这5个键中最近最少使用的键。
2.可通过maxmeory-samples设置随机抽取的键数:
	# maxmemory-samples 5
```



***

##持久化 

​	  redis是一个内存数据库，当redis服务器重启，获取电脑重启，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中。

###RDB：

​	RDB方式，是将redis某一时刻的数据持久化到磁盘中，是一种快照式的持久化方法。

**配置：**

​	在==redis.conf==配置文件中：

```
save 900 1     900秒内有超过1个的key值被修改了就发起快照保存

save 300 30    300秒内有超过30个的key值被修改了就发起快照保存

save 60 10000  60秒内有超过10000个的key值被修改了就发起快照保存
```

**原理：**

* redis在进行数据持久化的过程中，会先将数据写入到一个==临时文件==中，待持久化过程都结束了，才会用这个

		时文件替换上次持久化好的文件。正是这种特性，让我们可以随时来进行备份，因为快照文件总是完整可用

* RDB方式，redis会单独创建（fork）一个==子进程==来进行持久化，而主进程是不会进行任何IO操作的，这样就确保了redis极高的性能。
* 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。

**优点：**

​	效率高

**缺点：**

​	不适合数据完整性要求高的数据，出错时，至少会损失一次时间间隔内的数据。

###AOF：

​	AOF方式是将执行过的==写指令记录下来==，在数据==恢复时==按照==从前到后==的顺序再将指令==再执行一遍==。

```
# 默认的AOF持久化策略是每秒钟fsync一次
appendonly yes                       #启用AOF持久化，默认关闭，RDB模式就关闭了，两者是二选一的情况
appendfilename "appendonly.aof"      #AOF持久化的文件名称（默认）
appendfsync no                       #AOF持久化的策略
auto-aof-rewrite-percentage 100      #触发AOF文件重写的条件（默认）
auto-aof-rewrite-min-size 64mb       #触发AOF文件重写的条件（默认）
```

如果在追加日志时，恰好遇到磁盘空间满、inode满或断电等情况导致日志写入不完整，也没有关系，redis提供了redis-check-aof工具，可以用来进行日志修复。 

优点：

​	数据完整性高

​	数据恢复能力强

缺点：

​	同等数据规模下，AOF文件比RDB文件更占==空间==，恢复==速度==也跟慢

`Tips:`

	只要redis配置了AOF持久化方式，且AOF文件还没有被重写（rewrite），我们就可以用最快的速度暂停redis并编辑AOF文件，将最后一行的FLUSHALL命令删除，然后重启redis，就可以恢复redis的所有数据到FLUSHALL之前的状态了。

**rewrite机制：**

因为AOF采取追加方式，文件会越来越大。因此Redis提供了AOF 重写机制，将对`.aof`文件进行压缩，只保留可以恢复数据的 ==最小指令集==。

* 重写开始前，redis会创建（fork）一个“重写子进程”，这个子进程会首先读取现有的AOF文件，并将其包含的指令进行分析压缩并写入到一个临时文件中。
* 与此同时，主工作进程会将新接收到的写指令一边累积到内存缓冲区中，一边继续写入到原有的AOF文件中，这样做是保证原有的AOF文件的可用性，避免在重写过程中出现意外。
* 当“重写子进程”完成重写工作后，它会给父进程发一个信号，父进程收到信号后就会将内存中缓存的写指令追加到新AOF文件中。
* 当追加结束后，redis就会用新AOF文件来代替旧AOF文件，之后再有新的写指令，就都会追加到新的AOF文件中了。

`Tips:`

```
1.重写时采用先写临时文件，全部完成后再替换的流程，所以断电、磁盘满等问题不会影响AOF文件可用性。

2.AOF文件出问题：
	a.备份被写坏的AOF文件 
	b.运行redis-check-aof –fix进行修复 
	c.用diff -u来看下两个文件的差异，确认问题点 
	d.重启redis，加载修复后的AOF文件 
```

`对比：`



***

##主从





***

##集群

>1. 所有的`redis`节点彼此互联(`PING`-`PONG`机制),内部使用二进制协议优化传输速度和带宽。
>2. 节点的`fail`是通过集群中超过半数的节点检测失效时才生效。
>3. 客户端与`redis`节点直连,不需要中间`proxy`层.客户端不需要连接集群所有节点,连接集群中任何一个可用节点即可。
>4. `redis-cluster`把所有的物理节点映射到[`0-16383`]`slot`上,`cluster` 负责维护`node`<->`slot`<->`value`
>
>`Redis`集群中内置了 `16384` 个哈希槽，当需要在 `Redis` 集群中放置一个 `key-value` 时，`redis` 先对`key` 使用 `crc16` 算法算出一个结果，然后把结果对 `16384` 求余数，这样每个 `key` 都会对应一个编号在 `0-16383` 之间的哈希槽，`redis` 会根据节点数量大致均等的将哈希槽映射到不同的节点



***



##双写一致性



***

<https://blog.csdn.net/lby0307/article/details/79680326> 

##缓存穿透：

请求缓存和数据库中都不存在的数据。

>1、缓存层缓存空值。 
>–缓存太多空值，占用更多空间。（优化：给个空值过期时间） 
>–存储层更新代码了，缓存层还是空值。（优化：后台设置时主动删除空值，并缓存把值进去）
>
>2、将数据库中所有的查询条件，放到布隆过滤器中。当一个查询请求来临的时候，先经过布隆过滤器进行检查，如果请求存在这个条件中，那么继续执行，如果不在，直接丢弃。

```
void process3(String id){
        Goods goods = null;
        boolean b =false;
        try {
            goods = (Goods) redisTemplate.opsForHash().get("goods", id);

            if (goods == null){
                b = lock.tryLock();
                System.out.println(Thread.currentThread().getName()+"缓存无数据");
                System.out.println(Thread.currentThread().getName()+"是否成功获取锁:"+b);

                if (b){
                    // query db
                    goods = goodsMapper.findOneById(Integer.valueOf(id));
                    if (goods == null){
                        // data of db is null, save to cache
                        redisTemplate.opsForHash().put("goods", id, null);
                        System.out.println(Thread.currentThread().getName()+"数据库为null，缓存空值");
                    }else {
                        redisTemplate.opsForHash().put("goods", id, goods);
                        System.out.println(Thread.currentThread().getName()+"数据库有数据，存入缓存");
                    }
                }else {
                    System.out.println(Thread.currentThread().getName()+"获取锁失败");
                    Thread.sleep(1000);
                    process3(id);
                }
            }else {
                System.out.println(Thread.currentThread().getName()+"缓存存在");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            if (b){
                lock.unlock();
                System.out.println(Thread.currentThread().getName()+"释放锁");
            }
        }

        System.out.println(Thread.currentThread().getName()+goods);
    }
```





***

## 缓存雪崩

​        如果缓存集中在一段时间内失效，发生大量的缓存穿透，所有的查询都落在数据库上，造成了缓存雪崩。

>​    解决方法：
>
>1. 在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。
>2. 可以通过缓存reload机制，预先去更新缓存，再即将发生大并发访问前手动触发加载缓存
>3. 不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀
>4. 做二级缓存，或者双缓存策略。A1为原始缓存，A2为拷贝缓存，A1失效时，可以访问A2，A1缓存失效时间设置为短期，A2设置为长期。



 

***

##缓存击穿(热点key)

>1) 这个key是一个热点key（例如一个重要的新闻，一个热门的八卦新闻等等），所以这种key访问量可能非常大。
>
>​      (2) 缓存的构建是需要一定时间的。（可能是一个复杂计算，例如复杂的sql、多次IO、多个依赖(各种接口)等等）
>
>​       于是就会出现一个致命问题：在缓存失效的瞬间，有大量线程来构建缓存（见下图），造成后端负载加大，甚至可能会让系统崩溃 。



***

##并发竞争





problems:

​        \1. redis内存满了，如何解决？

​        a. 增加内存。

​        b.使用内存淘汰策略。

​        c.Redis集群。

Redis仅支持单实例，内存一般最多10~20GB。对于内存动辄100~200GB的系统，就需要通过集群来支持了。

Redis集群有三种方式：客户端分片、代理分片、RedisCluster(在之后一篇文章详细说一下。)

·        客户端分片

通过业务代码自己实现路由

优势：可以自己控制分片算法、性能比代理的好

劣势：维护成本高、扩容/缩容等运维操作都需要自己研发

·        代理分片

代理程序接收到来自业务程序的数据请求，根据路由规则，将这些请求分发给正确的Redis实例并返回给业务程序。使用类似Twemproxy、Codis等中间件实现。

优势：运维方便、程序不用关心如何链接Redis实例

劣势：会带来性能消耗（大概20%）、无法平滑扩容/缩容，需要执行脚本迁移数据，不方便(Codis在Twemproxy基础上优化并实现了预分片来达到Auto Rebalance)。

·        Redis Cluster

优势：官方集群解决方案、无中心节点，和客户端直连，性能较好

劣势：方案太重、无法平滑扩容/缩容，需要执行相应的脚本，不方便、太新，没有相应成熟的解决案例