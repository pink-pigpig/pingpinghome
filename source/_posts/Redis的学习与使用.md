---
title: Redis的学习与使用
date: 2025-08-13T12:27:01+08:00
tags:
- redis
- 入门知识
categories: 入门知识
index_img: https://qjw-00.oss-cn-guangzhou.aliyuncs.com/%E4%B8%AA%E4%BA%BA%E4%BD%BF%E7%94%A8%E5%AD%98%E5%82%A8/redis.jpg
---
# redis学习与使用

## 一、入门

### 1.概念

redis是一种数据库，但与一般的数据库不同，采用的是键值对来进行存储，他是在内存当中工作的，所以速度比较快。

- 适用于在一些热点数据上使用，减少数据库的压力
- 企业用的比较多
- 基于内存存储，读写性能高

### 2.redis的下载和安装

github下载连接[redis-windows/redis-windows: Redis 6.0.20 6.2.18 7.0.15 7.2.8 7.4.3 8.0.0 for Windows --- redis-windows/redis-windows: Redis 6.0.20 6.2.18 7.0.15 7.2.8 7.4.3 8.0.0 for Windows](https://github.com/redis-windows/redis-windows)

其他教程[Window下Redis的安装和部署详细图文教程（Redis的安装和可视化工具的使用）_redis安装-CSDN博客](https://blog.csdn.net/weixin_44893902/article/details/123087435)

### 3.需要注意的几个文件

- conf配置文件
- cli连接客户端
- server启动服务

```
>redis-cli.exe -h 1ocalhost -p 6379
```

通过此命令指定连接的地址和端口

```
redis-cli.exe -h 1ocalhost -p6379-a 123456
```

如果设置了密码，则需要在后面添加-a 密码

密码设置直接在conf上修改，其他操作直接查看官方

[Redis中文网](https://www.redis.net.cn/)

## 二、常用的用法

### 2.1redis常用数据类型

- 分类
  - 字符串 string
  - 哈希 hash
  - 列表 list
  - 集合 set
  - 有序集合 sorted set / zset

- 特点
  - 字符串(string)：普通字符串，Redis中最简单的数据类型
  - 哈希(hash)：也叫散列，类似于Java中的HashMap结构
  - 列表(list)：按照插入顺序排序，可以**有重复**元素，类似于Java中的LinkedList
  - 集合(set)：无序集合，**没有重复元素**，类似于Java中的HashSet
  - 有序集合(sorted set/zset)：集合中每个元素关联一个分数(score)，根据分数**升序排序，没有重复元素**

## 三、Redis常用命令

### 3.1字符串操作命令

Redis 中字符串类型常用命令：

| 字符串类型常用              |                                                     |
| --------------------------- | :-------------------------------------------------: |
| **SET** key value           |                   设置指定key的值                   |
| **GET** key                 |                   获取指定key的值                   |
| **SETEX** key seconds value | 设置指定key的值，并将 key 的过期时间设为 seconds 秒 |
| **SETNX** key value         |         只有在 key    不存在时设置 key 的值         |

更多命令可以参考Redis中文网：https://www.redis.net.cn

### 3.2哈希操作命令

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于**存储对象**，常用命令： 

:book:**需要注意的是**：key是表格，field是字段，value是字段的值。                           

| 哈希类型常用命令         |                                            |
| ------------------------ | ------------------------------------------ |
| **HSET** key field value | 将哈希表 key 中的字段 field 的值设为 value |
| **HGET** key field       | 获取存储在哈希表中指定字段的值             |
| **HDEL** key field       | 删除存储在哈希表中的指定字段               |
| **HKEYS** key            | 获取哈希表中所有字段                       |
| **HVALS** key            | 获取哈希表中所有值                         |

### 3.3列表操作命令

Redis 列表是简单的字符串列表，按照插入**顺序排序**，常用命令：

:blue_book:**例子：**插入abcd，则在队列中，是dcba。a在尾部，d在头部。

| 列表操作命令                   |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| **LPUSH** key value1 [value2]  | 将一个或多个值插入到列表头部                                 |
| **LRANGE** key start stop      | 获取列表指定范围内的元素                                     |
| **RPOP** key                   | 移除并获取列表最后一个元素                                   |
| **LLEN** key                   | 获取列表长度                                                 |
| **BRPOP** key1 [key2 ] timeout | 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超    时或发现可弹出元素为止 |

###  3.4 集合操作命令

Redis set 是string类型的无序集合。集合成员是**唯一的**，这就意味着集合中 不能出现重复的数据，常用命令：

:book: 可以通过集合的特性来进行运算，比如交集可以求共同数据。

| 集合操作命令                   |                          |
| ------------------------------ | ------------------------ |
| **SADD** key member1 [member2] | 向集合添加一个或多个成员 |
| **SMEMBERS** key               | 返回集合中的所有成员     |
| **SCARD** key                  | 获取集合的成员数         |
| **SINTER** key1 [key2]         | 返回给定所有集合的交集   |
| **SUNION** key1 [key2]         | 返回所有给定集合的并集   |
| **SREM** key member1 [member2] | 移除集合中一个或多个成员 |

### 3.5 有序集合操作命令

Redis**有序**集合是string类型元素的集合，且**不允许有重复**成员。每个元素都会关联一个double类型的分数。常用命令：

常用命令：

| 有序集合操作命令                             |                                              |
| -------------------------------------------- | -------------------------------------------- |
| **ZADD** key score1 member1 [score2 member2] | 向有序集合添加一个或多个成员                 |
| **ZRANGE** key start stop [WITHSCORES]       | 通过索引区间返回有序集合中指定区间内的成员   |
| **ZINCRBY** key increment member             | 有序集合中对指定成员的分数加上增量 increment |
| **ZREM** key member [member ...]             | 移除有序集合中的一个或多个成员               |

### 3.6 通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令：

- KEYS pattern 		查找所有符合给定模式( pattern)的 key 
- EXISTS key 		检查给定 key 是否存在
- TYPE key 		返回 key 所储存的值的类型
- DEL key 		该命令用于在 key 存在是删除 key



:bulb:**提示**：可以通过 0 -1 的方式来遍历整个表格

:bulb:**笔记**：同样兼容“ * ”来进行查询比如用keys set*就可以查询到所有set开头的key。



## 四、在java中操作redis

### 4.1redis的java客户端

Redis 的 Java 客户端很多，常用的几种：

- Jedis
- Lettuce
- Spring Data Redis

我这里用的是**Spring Data Redis**



### 4.2Spring Data Redis使用方式

Spring Data Redis 是 Spring 的一部分，提供了在 Spring 应用中通过简单的配置就可以访问 Redis 服务，对 Redis 底层开发包进行了高度封装。在 Spring 项目中，可以使用Spring Data Redis来简化 Redis 操作。

网址：https://spring.io/projects/spring-data-redis



Spring Boot提供了对应的Starter，maven坐标：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

Spring Data Redis中提供了一个高度封装的类：**RedisTemplate**，对相关api进行了归类封装,将同一类型操作封装为operation接口，具体分类如下：

- ValueOperations：string数据操作
- SetOperations：set类型数据操作
- ZSetOperations：zset类型数据操作
- HashOperations：hash类型的数据操作
- ListOperations：list类型的数据操作

### 4.3使用步骤

#### 4.3.1导入坐标

```xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

#### 4.3.2配置

**解释说明：**

database:指定使用Redis的哪个数据库，Redis服务启动后默认有16个数据库，编号分别是从0到15。

可以通过修改Redis配置文件来指定数据库的数量。

在application.yml中添加读取application-dev.yml中的相关Redis配置

```yaml
spring:
  profiles:
    active: dev
  redis:
    host: ${sky.redis.host}
    port: ${sky.redis.port}
    password: ${sky.redis.password}
    database: ${sky.redis.database}
```

在application-dev.yml中添加

```yaml
sky:
  redis:
    host: localhost
    port: 6379
    #没有设置密码的话不需要设置
    password: 123456
    #默认是10
    database: 10
```

#### 4.3.3配置类

```java
package com.sky.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
        log.info("开始创建redis模板对象...");
        RedisTemplate redisTemplate = new RedisTemplate();
        //设置redis的连接工厂对象
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置redis key的序列化器
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```

**解释说明：**

当前配置类不是必须的，因为 Spring Boot 框架会自动装配 RedisTemplate 对象，但是默认的key序列化器为

JdkSerializationRedisSerializer，导致我们存到Redis中后的数据和原始数据有差别，故设置为

StringRedisSerializer序列化器。

#### 4.3.4通过RedisTemplate对象操作Redis

在test下新建测试类

```java
package com.sky.test;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.*;

@SpringBootTest
public class SpringDataRedisTest {
    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void testRedisTemplate(){
        System.out.println(redisTemplate);
        //string数据操作
        ValueOperations valueOperations = redisTemplate.opsForValue();
        //hash类型的数据操作
        HashOperations hashOperations = redisTemplate.opsForHash();
        //list类型的数据操作
        ListOperations listOperations = redisTemplate.opsForList();
        //set类型数据操作
        SetOperations setOperations = redisTemplate.opsForSet();
        //zset类型数据操作
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();
    }
}
```

#### 4.2.3 操作常见类型数据

**1). 操作字符串类型数据**

```java
	/**
     * 操作字符串类型的数据
     */
    @Test
    public void testString(){
        // set get setex setnx
        redisTemplate.opsForValue().set("name","小明");
        String city = (String) redisTemplate.opsForValue().get("name");
        System.out.println(city);
        redisTemplate.opsForValue().set("code","1234",3, TimeUnit.MINUTES);
        redisTemplate.opsForValue().setIfAbsent("lock","1");
        redisTemplate.opsForValue().setIfAbsent("lock","2");
    }
```



**2). 操作哈希类型数据**

```java
	/**
     * 操作哈希类型的数据
     */
    @Test
    public void testHash(){
        //hset hget hdel hkeys hvals
        HashOperations hashOperations = redisTemplate.opsForHash();

        hashOperations.put("100","name","tom");
        hashOperations.put("100","age","20");

        String name = (String) hashOperations.get("100", "name");
        System.out.println(name);

        Set keys = hashOperations.keys("100");
        System.out.println(keys);

        List values = hashOperations.values("100");
        System.out.println(values);

        hashOperations.delete("100","age");
    }
```



**3). 操作列表类型数据**

```java
	/**
     * 操作列表类型的数据
     */
    @Test
    public void testList(){
        //lpush lrange rpop llen
        ListOperations listOperations = redisTemplate.opsForList();

        listOperations.leftPushAll("mylist","a","b","c");
        listOperations.leftPush("mylist","d");

        List mylist = listOperations.range("mylist", 0, -1);
        System.out.println(mylist);

        listOperations.rightPop("mylist");

        Long size = listOperations.size("mylist");
        System.out.println(size);
    }
```



**4). 操作集合类型数据**

```java
	/**
     * 操作集合类型的数据
     */
    @Test
    public void testSet(){
        //sadd smembers scard sinter sunion srem
        SetOperations setOperations = redisTemplate.opsForSet();

        setOperations.add("set1","a","b","c","d");
        setOperations.add("set2","a","b","x","y");

        Set members = setOperations.members("set1");
        System.out.println(members);

        Long size = setOperations.size("set1");
        System.out.println(size);

        Set intersect = setOperations.intersect("set1", "set2");
        System.out.println(intersect);

        Set union = setOperations.union("set1", "set2");
        System.out.println(union);

        setOperations.remove("set1","a","b");
    }
```



**5). 操作有序集合类型数据**

```java
	/**
     * 操作有序集合类型的数据
     */
    @Test
    public void testZset(){
        //zadd zrange zincrby zrem
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();

        zSetOperations.add("zset1","a",10);
        zSetOperations.add("zset1","b",12);
        zSetOperations.add("zset1","c",9);

        Set zset1 = zSetOperations.range("zset1", 0, -1);
        System.out.println(zset1);

        zSetOperations.incrementScore("zset1","c",10);

        zSetOperations.remove("zset1","a","b");
    }
```



**6). 通用命令操作**

```java
	/**
     * 通用命令操作
     */
    @Test
    public void testCommon(){
        //keys exists type del
        Set keys = redisTemplate.keys("*");
        System.out.println(keys);

        Boolean name = redisTemplate.hasKey("name");
        Boolean set1 = redisTemplate.hasKey("set1");

        for (Object key : keys) {
            DataType type = redisTemplate.type(key);
            System.out.println(type.name());
        }

        redisTemplate.delete("mylist");
    }
```
## 五、简化方式

### 5.1 Spring Cache

#### 5.1.1 介绍

Spring Cache 是一个框架，实现了基于注解的缓存功能，只需要简单地加一个注解，就能实现缓存功能。

Spring Cache 提供了一层抽象，底层可以切换不同的缓存实现，例如：

- EHCache
- Caffeine
- Redis(常用)

**起步依赖：**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>  		            		       	 <version>2.7.3</version> 
</dependency>
```



#### 5.1.2 常用注解

在SpringCache中提供了很多缓存操作的注解，常见的是以下的几个：

| **注解**       | **说明**                                                     |
| -------------- | ------------------------------------------------------------ |
| @EnableCaching | 开启缓存注解功能，通常加在启动类上                           |
| @Cacheable     | 在方法执行前先查询缓存中是否有数据，如果有数据，则直接返回缓存数据；如果没有缓存数据，调用方法并将方法返回值放到缓存中 |
| @CachePut      | 将方法的返回值放到缓存中                                     |
| @CacheEvict    | 将一条或多条数据从缓存中删除                                 |

在spring boot项目中，使用缓存技术只需在项目中导入相关缓存技术的依赖包，并在启动类上使用@EnableCaching开启缓存支持即可。

例如，使用Redis作为缓存技术，只需要导入Spring data Redis的maven坐标即可。



**注解的属性**

:bulb:**注意：**cachename：用于制定操作的Redis表

key：用于制定对于的key，可以用spel语法来书写，进入源码即可查看。

举个例子：

```java
@Cacheable(cacheNames = "setmealcache" ,key = "#categoryId")
//在方法执行前先查询缓存中是否有数据，如果有数据，则直接返回缓存数据；如果没有缓存数据，调用方法并将方法返回值放到缓存中
```

```java
@CacheEvict(cacheNames = "setmealcache", key = "#setmealDTO.categoryId")
```

```java
@CacheEvict(cacheNames = "setmealcache",allEntries = true)
//全部删除
//将一条或多条数据从缓存中删除
```





### 5.2 具体的实现思路

具体的实现思路

如下:

- 导入Spring Cache和Redis相关maven坐标

- 在启动类上加入@EnableCaching注解，开启缓存注解功能

- 在接口Controller的l方法上加入@Cacheable、@CachePut、@CacheEvict注解
