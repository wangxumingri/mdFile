#spring：

​	

[TOC]

@RequestParam 注解：在设置defaultValue时，name属性，失效，因为有默认值了，除非required=true

**controller层，如果设置响应结果是json数据而又没有提供，相关的json转换，仍然会报错：**

```HTML
HTTP Status 500 - No converter found for return value of type: class java.util.ArrayList

org.springframework.http.converter.HttpMessageNotWritableException: No converter found for return value of type: class java.util.ArrayList

```

**解决方法：**

​		**方法1：**

​			**导入阿里巴巴的fastjson，然后在mvc的配置文件中配置:**

```xml 
<!--开启对SpringMVC注解的支持 -->
<mvc:annotation-driven />


<mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean 	class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">
                <property name="supportedMediaTypes" value="application/json"/>
                <property name="features">
                    <array>
                        <value>WriteMapNullValue</value>
                        <value>WriteDateUseDateFormat</value>
                    </array>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

**修改后的结果：**

```json
[
  {
    "cron": "* * * * * ？",
    "id": 1,
    "name": "测试1"
  },
  {
    "cron": "0 1/2 * * * ？",
    "id": 2,
    "name": "测试2"
  }
]
```

`tips:`

​	**`开启注解一定要在，fastjson类型转换前面，否则，还是500`**

​	**方式2：导入json相关3jar包，不用配置**



***

# spring task:

###常用注解：

`@Schedule:`

``` 
功能：
位置：
属性：
	1.String cron: cron表达式	:根据cron表达式，执行任务
	2.int fixedDelay(毫秒)	 :固定间隔任务从方法最后一次任务执行结束时间开始计算
	3.int fixedRate (毫秒)     :固定频率任务:当上一次任务执行超过任务调度频率时，下次任务会立即执行
```



###注解方式：

​	1.任务类要被注册成bean：如  `@Compont`

​	2.在要执行的方法上使用： `@Schedule` 开启定时任务

**配置文件：**约束和下方相同

```xml
	<!--包扫描-->
	<context:component-scan base-package="com.wxss.task"/>
    <!--开启注解支持-->
	<task:annotation-driven/>
```

**任务类：**

```java
package com.wxss.task;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * 注解方式：实现定时任务：
 *  	1.注册bean
 		2.使用@Scheduled
 */
@Component
public class SpringTaskDemoByAnno {

    private static int count = 0;

    @Scheduled(cron = "* * * * * ?")
    public void printTime(){
        System.out.println(new Date());
    }
}

```



###配置文件方式：

​	1.在配置文件中配置

​	2.需要加载配置文件

**配置文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.2.xsd">
    <description>
        定时任务（可删除，无实际作用）
    </description>
    <!--定时注解驱动-->
    <task:annotation-driven />
    
    <!--进行定时任务的类，将其定义为一个bean-->
    <bean id="springTaskDemoByXML" class="com.wxss.task.SpringTaskDemoByXML"/>
    <!--通过task标签，定义定时功能-->
    <task:scheduled-tasks>
        <!--任务类的bean                    要执行的定时任务（类中方法）   cron表达式 -->
        <task:scheduled ref="springTaskDemoByXML" method="printTime" cron="0/10 * * *          * ?" />
    </task:scheduled-tasks>
</beans>
```

**任务类：**

```java
package com.wxss.task;

import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.stereotype.Component;
import org.springframework.test.context.ContextConfiguration;

import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * xml方式：实现定时任务（applicationContext-task1.xml）
 *
 */
public class SpringTaskDemoByXML {

    public void printTime(){
        System.out.println(new SimpleDateFormat("yyyy-MM-dd hh:mm:ss")
                           .format(new Date()));
    }
}
```

# spring data redis

```
Redis是一种NOSQL数据库，Key-Value形式对数据进行存储，其中数据可以以内存形式存在，也可以持久化到文件系统。Spring data对Redis进行了很好的封装，用起来也是十分的得心应手。Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 字符串（strings）， 散列（hashes）， 列表（lists）， 集合（sets）， 有序集合（sorted sets） 与范围查询， bitmaps， hyperloglogs 和 地理空间（geospatial） 索引半径查询。 Redis 内置了 复制（replication），LUA脚本（Lua scripting）， LRU驱动事件（LRU eviction），事务（transactions） 和不同级别的 磁盘持久化（persistence）， 并通过 Redis哨兵（Sentinel）和自动 分区（Cluster）提供高可用性（high availability）。
```

###**spring 整合 redis**

```xml 
<dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-redis</artifactId>
      <version>1.5.0.RELEASE</version>
    </dependency>
<dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>2.6.2</version>
</dependency>
```

###**spring boot整合spring data redis:**

```xml
<!--springboot 整合 redis-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

```

###java配置类：

**配置文件方式可根据此类对比，进行配置**

```java
package com.wxss.demo.config;

import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;
import redis.clients.jedis.JedisPoolConfig;

/**
 * Redis配置类：
 *      JedisPoolConfig
 *          -redis.config
 *      JedisConnectionFactory
 *          -JedisPoolConfig
 *          【-哨兵】
 *      RedisTemplate
 *          -JedisConnectionFactory
 *          -序列化
 */
@Configuration
@PropertySource("classpath:redis.properties")
//@EnableAutoConfiguration
//@EnableCaching // 该注解的作用？
public class RedisConfig {

    // 从配置文件中获取配置信息
    @Value("${redis.maxIdle}")
    private Integer maxIdle;

    @Value("${redis.maxTotal}")
    private Integer maxTotal;

    @Value("${redis.maxWaitMillis}")
    private Integer maxWaitMillis;

    @Value("${redis.minEvictableIdleTimeMillis}")
    private Integer minEvictableIdleTimeMillis;

    @Value("${redis.numTestsPerEvictionRun}")
    private Integer numTestsPerEvictionRun;

    @Value("${redis.timeBetweenEvictionRunsMillis}")
    private long timeBetweenEvictionRunsMillis;

    @Value("${redis.testOnBorrow}")
    private boolean testOnBorrow;

    @Value("${redis.testWhileIdle}")
    private boolean testWhileIdle;


//    @Value("${spring.redis.cluster.nodes}")
//    private String clusterNodes;
//
//    @Value("${spring.redis.cluster.max-redirects}")
//    private Integer mmaxRedirectsac;

    /**
     * Jedis连接池配置
     * @return
     */
    @Bean(name = "myJedisPoolConfig")
    public JedisPoolConfig jedisPoolConfig(){
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();

        // 最大空闲数
        jedisPoolConfig.setMaxIdle(maxIdle);
        // 连接池的最大数据库连接数
        jedisPoolConfig.setMaxTotal(maxTotal);
        // 最大建立连接等待时间
        jedisPoolConfig.setMaxWaitMillis(maxWaitMillis);
        // 逐出连接的最小空闲时间 默认1800000毫秒(30分钟)
        jedisPoolConfig.setMinEvictableIdleTimeMillis(minEvictableIdleTimeMillis);
        // 每次逐出检查时 逐出的最大数目 如果为负数就是 : 1/abs(n), 默认3
        jedisPoolConfig.setNumTestsPerEvictionRun(numTestsPerEvictionRun);
        // 逐出扫描的时间间隔(毫秒) 如果为负数,则不运行逐出线程, 默认-1
        jedisPoolConfig.setTimeBetweenEvictionRunsMillis(timeBetweenEvictionRunsMillis);
        // 是否在从池中取出连接前进行检验,如果检验失败,则从池中去除连接并尝试取出另一个
        jedisPoolConfig.setTestOnBorrow(testOnBorrow);
        // 在空闲时检查有效性, 默认false
        jedisPoolConfig.setTestWhileIdle(testWhileIdle);

        return jedisPoolConfig;
    }

    /**
     * 连接工厂配置
     * @param jedisPoolConfig
     * @return
     */
    @Bean(name="myJedisConnectionFactory")
    public JedisConnectionFactory jedisConnectionFactory(@Qualifier("myJedisPoolConfig") JedisPoolConfig jedisPoolConfig){
        // 还可以在此传入哨兵
        JedisConnectionFactory jedisConnectionFactory = new JedisConnectionFactory(jedisPoolConfig);

        return jedisConnectionFactory;
    }

    /**
     * 模板：具体操作Redis
     * @param jedisConnectionFactory
     * @return
     */
    @Bean("myRedisTemplate")
    public RedisTemplate redisTemplate(@Qualifier("myJedisConnectionFactory") JedisConnectionFactory jedisConnectionFactory){
        RedisTemplate redisTemplate = new RedisTemplate();
        //如果不配置Serializer，那么存储的时候缺省使用String，如果用User类型存储，那么会提示错误User can't cast to String！
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        // 开启事务
        redisTemplate.setEnableTransactionSupport(true);
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        return redisTemplate;
    }
}

```



```
opsForValue() - Operations for working with entries having simple values
opsForList() - Operations for working with entries having list values
opsForSet() - Operations for working with entries having set values
opsForZSet() - Operations for working with entries having ZSet (sorted set) values
opsForHash() - Operations for working with entries having hash values
boundValueOps(K) - Operations for working with simple values bound to a given key
boundListOps(K) - Operations for working with list values bound to a given key
boundSetOps(K) - Operations for working with set values bound to a given key
boundZSet(K) - Operations for working with ZSet (sorted set) values bound to a given key
boundHashOps(K) - Operations for working with hash values bound to a given key
```

**可以直接注入模板，或者以ops形式注 入,示例如下：**

```java
public class Example {
    // inject the actual template
    @Autowired
    private RedisTemplate<String, String> template;  // 泛型可以省略

    // inject the template as ListOperations
    // can also inject as Value, Set, ZSet, and HashOperations
    @Resource(name="redisTemplate")
    private ListOperations<String, String> listOps;

    public void addLink(String userId, URL url) {
        // use xxxOps
        listOps.leftPush(userId, url.toExternalForm());
        // or use template directly
        template.boundListOps(userId).leftPush(url.toExternalForm());
    }
}
```

***

###**序列化与开启事务：**

>StringRedisTemplate默认采用的是String的序列化策略，保存的key和value都是采用此策略序列化保存的。 RedisTemplate默认采用的是JDK的序列化策略，保存的key和value都是采用此策略序列化保存的。   

​	

通常情况下Key和Value都采用不同的方式进行持久化，如下示例中Key使用String进行持久化，Value使用Jackson格式进行持久化: 

```java
@Bean
public RedisTemplate redisTemplate(RedisConnectionFactory rcf) {
 //如果不配置Serializer，那么存储的时候缺省使用String，如果用User类型存储，那么会提示错误User can't cast to String！
    // 1.创建模板实例
	RedisTemplate redisTemplate = new RedisTemplate();
    // 2.设置序列化方式：还可以设置其他类型的,如:zset,set,list...
    // 2.1设置字符类型的序列化方式
	redisTemplate.setKeySerializer(new StringRedisSerializer());
    redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
    // 2.2设置Hash类型的序列化方式
	redisTemplate.setHashKeySerializer(new StringRedisSerializer());
	redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
	
    // 开启事务
	redisTemplate.setEnableTransactionSupport(true);
	redisTemplate.setConnectionFactory(redisConnectionFactory);

	return redisTemplate;
}
```

***

###缓存时间：



***

###中文乱码问题：

**该问题由Redis序列化引起，对数据本身无影响，只是可读性不好。**

>StringRedisTemplate默认采用的是String的序列化策略，保存的key和value都是采用此策略序列化保存的。 RedisTemplate默认采用的是JDK的序列化策略，保存的key和value都是采用此策略序列化保存的。   

![redis序列化中文乱码](spring mvc：.assets/redis序列化中文乱码.png)

**方法1:** redis客户端：启动时，加参数 ：  `--raw` (**有时无效**)

**方法2:**修改windows的DOS窗口的编码和字体****

**方式3:**