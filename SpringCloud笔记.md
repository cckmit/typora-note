# SpringCloud

先创建一个空的maven父工程项目，用于管理全部子工程服务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>SpringCloud</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>SpringCloud</name>
    <!--pom打包-->
    <packaging>pom</packaging>

    <!--子工程模块-->
    <modules>
        <module>SpringCloud_Eureka</module>
        <module>SpringCloud_Config</module>
    </modules>
</project>

```



## 1.Eureka —— 注册中心

### （1）简介

> Eureka 是 Netflix 出品的用于实现服务注册和发现的工具，Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册和发现
>  Eureka采用C-S的设计架构，包含Eureka Server 和Eureka Client两个组件

### （2）基本原理

> Applecation-server :服务提供者
> Application-cliene:服务消费者
> 服务启动后向Eureka注册，Eureka Server会将注册信息向其他Eureka Server进行同步，当服务消费者要调用服务提供者，则向服务注册中心获取服务提供者地址，然后会将服务提供者地址缓存在本地，下次再调用时，则直接从本地缓存中取，完成一次调用

### （3）自我保护机制

> 在默认配置中EurekaServer服务在一定时间（默认为90秒）没接受到某个服务的心跳连接后，EurekaServer会注销该服务。但是会存在当网络分区发生故障，导致该时间内没有心跳连接，但该服务本身还是健康运行的情况。Eureka通过“自我保护模式”来解决这个问题。
> 在自我保护模式中，Eureka Server会保护服务注册表中的信息，不再注销任何服务实例。

### （4）快速使用

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>SpringCloud_Eureka-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>SpringCloud_Eureka</name>
    <description>SpringCloud_Eureka</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
    </properties>

    <dependencies>
        <!--eureka-server服务端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka-server</artifactId>
            <version>1.4.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

```yml
server:
  port: 8003 # 应用端口，Eureka服务端默认为：8761
  
eureka:
  instance:
    hostname: 127.0.0.1 # 应用实例主机名
  server:
    enable-self-preservation: false  # 是否允许开启自我保护模式，默认：true, 当Eureka服务器在短时间内丢失过多客户端时，自我保护模式可使服务端不再删除失去连接的客户端
  client:
    registerWithEureka: false # 是否向注册中心注册自己，默认:true，一般情况下，Eureka服务端不需要注册自己
    fetchRegistry: false # 是否从Eureka获取注册信息，默认：true，一般情况下，Eureka服务端是不需要的
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/ 
# Eureka服务器的地址，类型为HashMap，默认Key为 defaultZone；默认的Value为http://localhost:8761/eureka
# 如果服务注册中心为高可用集群时，多个注册中心地址以逗号分隔。 
spring:
  application:
    name: eureka_server # 应用名称，将会显示在Eureka界面的应用名称列
```

### （5）配置项解析

通用配置：

```properties
# 应用名称，将会显示在Eureka界面的应用名称列
spring.application.name=config-service
# 应用端口，Eureka服务端默认为：8761
server.port=3333
```

eureka.server前缀的配置项：

```properties
# 是否允许开启自我保护模式，缺省：true
# 当Eureka服务器在短时间内丢失过多客户端时，自我保护模式可使服务端不再删除失去连接的客户端
eureka.server.enable-self-preservation = false
# Peer节点更新间隔，单位：毫秒
eureka.server.peer-eureka-nodes-update-interval-ms = 
# Eureka服务器清理无效节点的时间间隔，单位：毫秒，缺省：60000，即60秒
eureka.server.eviction-interval-timer-in-ms = 60000
```

eureka.instance前缀的配置项:

```properties
# 服务名，默认取 spring.application.name 配置值，如果没有则为 unknown
eureka.instance.appname = eureka-client

# 实例ID
eureka.instance.instance-id = eureka-client-instance1

# 应用实例主机名
eureka.instance.hostname = localhost

# 客户端在注册时使用自己的IP而不是主机名，缺省：false
eureka.instance.prefer-ip-address = false

# 应用实例IP
eureka.instance.ip-address = 127.0.0.1

# 服务失效时间，失效的服务将被剔除。单位：秒，默认：90
eureka.instance.lease-expiration-duration-in-seconds = 90

# 服务续约（心跳）频率，单位：秒，缺省30
eureka.instance.lease-renewal-interval-in-seconds = 30

# 状态页面的URL，相对路径，默认使用 HTTP 访问，如需使用 HTTPS则要使用绝对路径配置，缺省：/info
eureka.instance.status-page-url-path = /info

# 健康检查页面的URL，相对路径，默认使用 HTTP 访问，如需使用 HTTPS则要使用绝对路径配置，缺省：/health
eureka.instance.health-check-url-path = /health
```

eureka.client前缀:

```properties
# Eureka服务器的地址，类型为HashMap，缺省的Key为 defaultZone；缺省的Value为 http://localhost:8761/eureka
# 如果服务注册中心为高可用集群时，多个注册中心地址以逗号分隔。 
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka

# 是否向注册中心注册自己，缺省：true
# 一般情况下，Eureka服务端是不需要再注册自己的
eureka.client.register-with-eureka = true

# 是否从Eureka获取注册信息，缺省：true
# 一般情况下，Eureka服务端是不需要的
eureka.client.fetch-registry = true

# 客户端拉取服务注册信息间隔，单位：秒，缺省：30
eureka.client.registry-fetch-interval-seconds = 30

# 是否启用客户端健康检查
eureka.client.health-check.enabled = true

# 询问Eureka服务url信息变化的时间间隔（s），默认为300秒
eureka.client.eureka-service-url-poll-interval-seconds = 60

# 连接Eureka服务器的超时时间，单位：秒，缺省：5
eureka.client.eureka-server-connect-timeout-seconds = 5

# 从Eureka服务器读取信息的超时时间，单位：秒，缺省：8
eureka.client.eureka-server-read-timeout-seconds = 8

# 获取实例时是否只保留状态为 UP 的实例，缺省：true
eureka.client.filter-only-up-instances = true

# Eureka服务端连接空闲时的关闭时间，单位：秒，缺省：30
eureka.client.eureka-connection-idle-timeout-seconds = 30

# 从Eureka客户端到所有Eureka服务端的连接总数，缺省：200
eureka.client.eureka-server-total-connections = 200

# 从Eureka客户端到每个Eureka服务主机的连接总数，缺省：50
eureka.client.eureka-server-total-connections-per-host = 50
```



## 2.Config —— 配置中心

### （1）使用配置中心的原因

> 微服务意味着要将单体应用中的业务拆分成一个个子服务，每个服务的粒度相对较小，因此系统中会出现大量的服务，由于每个服务都需要必要的配置信息才能运行，所以一套集中式、动态的配置管理设施是必不可少的。我们每个服务都有自己的配置文件，如果有上百个微服务的话就有上百个配置文件，如果要改某一个配置的参数，有可能就需要修改好多个微服务的配置文件，那样岂不是又费时又费力，Spring Cloud提供了ConfigServer配置中心来解决这个问题，实现了一处修改，处处运行。

### （2）配置中心的作用

> 集中管理配置文件，便于管理
> 不同的环境不同的配置，动态化的配置更新，分环境比如：dev/test/prod/bata/release
> 运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件，服务会向配置中心统计拉去配置自己的信息。
> 当配置发生改变时，服务不需要重启即可感知到配置的变化并应用新的配置
> 将配置信息以Rest接口暴露，post/curi访问刷新即可

### （3）快速使用

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
    </parent>
    <groupId>com.example</groupId>
    <artifactId>SpringCloud_Config</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>SpringCloud_Config</name>
    <packaging>jar</packaging>
    <description>SpringCloud_Config</description>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.SR1</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>   
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

```yml
server:
  port: 8004

eureka:
  instance:
    instance-id: 127.0.0.1:${server.port}
    prefer-ip-address: true
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:8003/eureka/

spring:
  application:
    name: service-configserver
  cloud:
    config:
      server:
        # 这里实用git协议从github、码云获取配置文件，也可以换成svn协议
           git:
                  uri: https://github.com/winjayok/typora-note
                  # default-label 指定默认的分支名称
                  default-label: master
                  # search-paths 如果配置文件不是放在分支根目录，则实用该属性指向文件夹，如下例代表：在master根目录的dev文件夹下查找配置文件
                  search-paths: dev
                  username: ***
                  password: ***
                  
#svn 配置
#spring:
  #application:
    #name: service-configserver
  #cloud:
    #config:
      #server:
        #svn:
          #uri: https://120.76.41.162:8002/svn/tl_products/e_oa/07Develop/TlOa/Config
          #username: chenmr
          #password: 6WFh4x7b
          #default-label: produce
  #profiles:
    #active: subversion

```

**配置可能出现的问题**：客户端的配置文件必须为boostrap.yml（properties），因为boostrap优先于application，否则将会读取远程配置文件失败！

## 3. Zuul /Gateway—— 服务网关

### （1）跨域问题

跨域是指跨域名的访问，有三种情况：

- 域名不同的跨域。
- 域名相同、端口不同的跨域。
- 二级域名不同的跨域。

 跨域方法解析

> - allowedOrigins：设置能跨域访问我的域名，其中*号代表任意域名。
> - allowCredentials：是否允许携带cookie？默认情况下值为false。
> - allowMethod：接受的请求方式。
> - maxAge：本次许可的有效时长，单位是秒。
> - allowHeaders：请求头信息。
> - path：“/**”表示的是拦截对应域名下的所有请求，也可以自行设置请求路径。

```java
 /*
 * 解决多个服务之间的跨域问题
 * */
@Bean
	public FilterRegistrationBean corsFilter() {
		UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
		CorsConfiguration config = new CorsConfiguration();
		config.setAllowCredentials(true); //是否允许携带cookie？默认情况下值为false。
		config.addAllowedOrigin("*"); //设置能跨域访问我的域名，其中*号代表任意域名
		config.addAllowedHeader("*"); //请求头信息
		config.addAllowedMethod("GET"); //接受的请求方式
		source.registerCorsConfiguration("/**", config);
		FilterRegistrationBean bean = new FilterRegistrationBean(new CorsFilter(source));
		bean.setOrder(Ordered.HIGHEST_PRECEDENCE);
		return bean;
	}
```

### （2）实现自定义的Filter

> filterOrder():使用返回值设定Filter执行次序。
>
> filterType():使用返回值摄动Filter类型，可以是pre，route，post，error类型。
>
> shouldFilter():使用返回值设定该Filter是否执行，可以作为开关使用。
>
> run():Filter里面的核心执行逻辑，业务处理在此编写。

```java
@Component
public class MyFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 0;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() throws ZuulException {
        return null;
    }
}

```

### （3）yml配置

```yml
zuul:
 routes:
   api-system: #会员服务网关配置
     path: /api-system/**   #访问只要是/api-system/ 开头的直接转发到service-consumer服务
     serviceId: service-consumer  #服务名
```



## 4.Feign —— 负载均衡（服务调用）

## 5. Hystrix  —— 熔断器