# redis笔记

 ## 1.linux安装

1、 下载最新版安装包https://redis.io

2、引入文件到wscp，使用xshell操作

3、基本的环境安装

```bash
yum install gcc.c++

make
```

```bash
//出现make[1]: [persist-settings] Error 2 (ignored)
//则需要升级gcc
sudo yum install centos-release-scl
sudo yum install devtoolset-7-gcc*
scl enable devtoolset-7 bash
```

不想升级建议使用redis6.0版本以下！

4、安装遇到的问题：

In file included from adlist.c:34:0:
zmalloc.h:50:31: 致命错误：jemalloc/jemalloc.h：没有那个文件或目录

![img](https://img2018.cnblogs.com/blog/1422210/201902/1422210-20190207173550547-1744974127.png)

解决：

```bash
make MALLOC=libc
```

错误：linux redis release.c:37:10: fatal error: release.h: No such file or directory

```bash
37 | #include “release.h”

步骤：1.cd 到文件中的src目录
2.chmod +x mkreleasehdr.sh
3.make
```

![1599458429257](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1599458429257.png)

安装成功！

## 2.reids配置

1、redis安装目录默认在cd /usr/local/bin

![1599458581600](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1599458581600.png)

2、redis复制conf文件到其他目录

![1599458960092](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1599458960092.png)

3、修改redis.conf

使用命令进入配置文件

```bash
vim redis.conf
```

修改daemonize  yes

![1599459249343](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1599459249343.png)

4.启动redis服务

输入命令

```bash
# cd /usr/local/bin
# redis-server myconf/redis.conf
//然后再输入
# redis-cli
最后输入ping查看是否连接成功
ping
```

出现下面命令便启动成功！

![1599459634412](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1599459634412.png)

## 3.redis发布订阅

1、Redis 客户端可以订阅任意数量的频道。

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub1.png)![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub2.png)

2、下表列出了 redis 发布订阅常用命令：

| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [PSUBSCRIBE pattern [pattern ...\]](https://www.runoob.com/redis/pub-sub-psubscribe.html)  订阅一个或多个符合给定模式的频道。 |
| 2    | [PUBSUB subcommand [argument [argument ...\]]](https://www.runoob.com/redis/pub-sub-pubsub.html)  查看订阅与发布系统状态。 |
| 3    | [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html)  将信息发送到指定的频道。 |
| 4    | [PUNSUBSCRIBE [pattern [pattern ...\]]](https://www.runoob.com/redis/pub-sub-punsubscribe.html)  退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...\]](https://www.runoob.com/redis/pub-sub-subscribe.html)  订阅给定的一个或多个频道的信息。 |
| 6    | [UNSUBSCRIBE [channel [channel ...\]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html)  指退订给定的频道。 |

3、测试

订阅者

```bash
127.0.0.1:6379> subscribe winjay
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "winjay"
3) (integer) 1
```

发布者

```bash
127.0.0.1:6379> publish winjay "hello winjay"
(integer) 1
127.0.0.1:6379> 
```

订阅者接收到发布者的消息hello winjay

![1599460648563](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1599460648563.png)

4、使用场景

实时消息系统、实时聊天、订阅，关注系统

## 4.redis主从复制

通常为一主二从。当redis存储数据超过20g时，就要考虑主从复制。

**1、环境配置**

只需配置从库，不用配置主库！

```bash
# info replication
```

![1599461383515](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1599461383515.png)

复制两个配置文件，修改对应信息

- 端口
- pid
- log文件名
- dump.rdb名字

**2、一主二从**

默认情况下redis都为主机，只需要认定一台为主机，配置从机即可。

```bash
#在从机端口配置
slaveof 127.0.0.1 6379  #选定端口号为6379的为主机
#查询信息
info replication
```

- 主机可以写，从机不可以写，只能读取内容
- 主机断了，从机依旧连接主机，如果主机回来了，从机仍能读取信息
- 使用命令执行是暂时的，重启后从机会变回主机

**3、配置哨兵模式**

1、配置哨兵文件

```bash
#sentinel monitor 被监控的名称 端口号 1
sentinel monitor myredis 127.0.0.1 6379 1
```

后面的1 代表主机挂了，slave投票让谁代替主机，票数多为主机，如果主机回来了，只能变为从机，这就是哨兵模式的规则

> 哨兵模式

优点：

- 哨兵集群，基于主从复制模式的所有优点
- 主从可以切换，故障可以转移，系统的可用性会更好
- 哨兵模式是主从模式的升级，手动到自动，更加奖状

缺点：

- 在线扩容困难

## 5.redis缓存穿透和雪崩

> 缓存穿透概念

 缓存穿透的概念很简单，用户想要查询一个数据，发现redis内存数据库没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中，于是都去请求了持久层数据库。这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透。 

 **这里需要注意和缓存击穿的区别，缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞。** 

> 解决方案

**（1）布隆过滤器**

布隆过滤器是一种数据结构，垃圾网站和正常网站加起来全世界据统计也有几十亿个。网警要过滤这些垃圾网站，总不能到数据库里面一个一个去比较吧，这就可以使用布隆过滤器。假设我们存储一亿个垃圾网站地址。

可以先有一亿个二进制比特，然后网警用八个不同的随机数产生器（F1,F2, …,F8） 产生八个信息指纹（f1, f2, …, f8）。接下来用一个随机数产生器 G 把这八个信息指纹映射到 1 到1亿中的八个自然数 g1, g2, …,g8。最后把这八个位置的二进制全部设置为一。过程如下：

![img](https://pics6.baidu.com/feed/21a4462309f79052696269b62519dfcc7acbd52d.jpeg?token=77d2a5fe27c3fd42d7ae7a07d68fadec&s=7DA434729B0A4D495EE195DF000050B3)

有一天网警查到了一个可疑的网站，想判断一下是否是XX网站，首先将可疑网站通过哈希映射到1亿个比特数组上的8个点。如果8个点的其中有一个点不为1，则可以判断该元素一定不存在集合中。

那这个布隆过滤器是如何解决redis中的缓存穿透呢？很简单首先也是对所有可能查询的参数以hash形式存储，当用户想要查询的时候，使用布隆过滤器发现不在集合中，就直接丢弃，不再对持久层查询。

![img](https://pics6.baidu.com/feed/d788d43f8794a4c2773846a5251e13d3ac6e3910.jpeg?token=029a90d30125df6852018a7e4e7294ff&s=1AA27423D99E44C80E5CE5DE000080B1)

这个形式很简单。

**2、缓存空对象**

当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数据将会从缓存中获取，保护了后端数据源；

![img](https://pics6.baidu.com/feed/203fb80e7bec54e7a4ba970397d293564ec26a4d.jpeg?token=0068420533f90cb97750b10ce30053b8&s=9A027C239B9E4DC848DDC4D6000080B2)

但是这种方法会存在两个问题：

如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键；即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响。

> 缓存雪崩概念

 缓存雪崩是指，缓存层出现了错误，不能正常工作了。于是所有的请求都会达到存储层，存储层的调用量会暴增，造成存储层也会挂掉的情况。 

> 解决方案

**（1）redis高可用**

这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群。

**（2）限流降级**

这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

**（3）数据预热**

数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。

# Spring整合Redis

```xml
  <!--spring整合Redis的jar包-->
      <dependency>
        <groupId>org.springframework.data</groupId>
        <artifactId>spring-data-redis</artifactId>
        <version>2.4.0</version>
      </dependency>
      <!--java连接Redis的jar包-->
      <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.3.0</version>
      </dependency>
```

```properties
#redis主机号
redis.hostname=127.0.0.1
#redis端口号
redis.port=6379
#redis密码，可不填
redis.password=
#redis超时时间，默认2000
redis.timeout=2000
        
#redis数据源连接池
#redis检测是否连接成功
redis.testOnBorrow=true
#redis最大连接数
redis.pool.maxActive=600
#redis最大空闲数
redis.pool.maxIdle=300
#redis最大等待时间
redis.pool.maxWait=3000
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.0.xsd">

    <context:property-placeholder location="classpath:conf/redis.properties"/>

    <!--reids连接池-->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxIdle" value="${redis.pool.maxIdle}"></property>
        <property name="maxTotal" value="${redis.pool.maxActive}"></property>
        <property name="maxWaitMillis" value="${redis.pool.maxWait}"></property>
        <property name="testOnBorrow" value="${redis.testOnBorrow}"></property>
    </bean>

    <!-- Spring-redis连接池管理工厂 -->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <!-- IP地址 -->
        <property name="hostName" value="${redis.host}" />
        <!-- 端口号 -->
        <property name="port" value="${redis.port}" />
        <property name="password" value="${redis.password}" />
        <!-- 超时时间 默认2000-->
        <property name="timeout" value="${redis.timeout}" />
        <!-- 连接池配置引用 -->
        <property name="poolConfig" ref="poolConfig" />
    </bean>

    <!-- redis template definition -->
    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="jedisConnectionFactory" />
        <!--序列化-->
        <property name="keySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
        <!--默认jdk序列化，json有问题-->
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
        </property>
        <property name="hashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
        <property name="hashValueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
        </property>
        <!--开启事务  -->
        <property name="enableTransactionSupport" value="true"></property>
    </bean>

    <!--自定义redis工具类,在需要缓存的地方注入此类  -->
<!--    <bean id="redisService" class="com.cff.springwork.redis.service.RedisService">
        <property name="redisTemplate" ref="redisTemplate" />
    </bean>-->
```

# Springboot整合Redis

```xml
 <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>      
 </dependency>
```

```yml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    password: 123456
    # jedis连接池配置
    jedis:
      pool:
        #连接池最大连接数，若为-1则表示没有任何限制
        max-active: 8
        #连接池最大阻塞等待时间，若为-1则表示没有任何限制
        max-wait: -1
        #连接池中的最大空闲连接
        max-idle: 500
        min-idle: 0
    lettuce:
      shutdown-timeout: 0
```

```java
/**
 * redis序列化配置
 * @PackageName: com.example.config
 * @ClassName: redisConfig
 * @Description: redisConfig
 * @Author: Winjay
 * @Date: 2022-04-10 17:12:24
 */
@Configuration
public class RedisConfig {
    /**
     * redisTemplate 序列化使用的jdkSerializeable, 存储二进制字节码, 所以自定义序列化类
     * @param redisConnectionFactory
     * @return
     */
    @Bean(name = "redisTemplate")
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        /**
         * 配置具体的序列化方式
         */

        //JSON序列化
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer =
                new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.configure(MapperFeature.USE_ANNOTATIONS, false);
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        objectMapper.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, false);
        // 此项必须配置，否则会报java.lang.ClassCastException: java.util.LinkedHashMap cannot be cast to XXX
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
        objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        //String序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        //key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        //hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        //hash的value序列化采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();

        return template;
    }


    @Bean
    public JdkSerializationRedisSerializer jdkSerializationRedisSerializer() {
        return new JdkSerializationRedisSerializer();
    }

    @PostConstruct
    public void initFastJson(){//fastJson 设置全局安全性问题
        ParserConfig.getGlobalInstance().setSafeMode(true);
    }

}
```

