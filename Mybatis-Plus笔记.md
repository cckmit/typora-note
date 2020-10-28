# Mybatis-Plus

## Lombok注解的使用

**@Getter/@Setter: 作用类上，生成所有成员变量的getter/setter方法；作用于成员变量上，生成该成员变量的getter/setter方法。可以设定访问权限及是否懒加载等。**

**@NoArgsConstructor：生成无参构造器；**

**@AllArgsConstructor：生成全参构造器**

**@Data：作用于类上，是以下注解的集合：@ToString @EqualsAndHashCode @Getter @Setter @RequiredArgsConstructor**

```java
import lombok.*;

@Data
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```



## 一、Springboot整合mybatis-plus

	### 1.springboot工程添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.4.0</version>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

### 2.配置数据库

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    schema: classpath:db/schema-h2.sql
    data: classpath:db/data-h2.sql
    url: jdbc:mysql://localhost:3306/demo?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone = GMT%2B8
    username: root
    password: 123456
```

###  3.添加 `@MapperScan` 注解

```java
@SpringBootApplication
@MapperScan("com.baomidou.mybatisplus.samples.quickstart.mapper")
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(QuickStartApplication.class, args);
    }

}
```

### 4.打印sql语句日志

```xml
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.stdOutImpl
```

## 5.分页插件

```java
//Spring boot方式
@Configuration
@MapperScan("com.baomidou.cloud.service.*.mapper*")
public class MybatisPlusConfig {

    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```



### 6.CRUD扩展

#### id主键生成策略

```java 
@TableId(type = idType.Auto)  //自增id
```



## 二、Spring整合mybatis-plus

### 1.spring配置添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>ssm</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>ssm</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
    <!-- 统一管理项目依赖版本 -->
      <mybatis.plus.version>3.2.0</mybatis.plus.version>
      <junit.version>4.12</junit.version>
      <log4j.version>1.2.17</log4j.version>
      <druid.version>1.1.6</druid.version>
      <lombok.version>1.16.18</lombok.version>
  </properties>

  <dependencies>
    <!-- Mybatis-Plus  依赖
            mybatis-plus 会自动维护 mybatis 以及 mybatis-spring 相关的依赖
            Mybatis 及 Mybatis-Spring 依赖请勿加入项目配置，以免引起版本冲突！！！Mybatis-Plus 会自动帮你维护！
         -->
    <dependency>
      <groupId>com.baomidou</groupId>
      <artifactId>mybatis-plus</artifactId>
      <version>${mybatis.plus.version}</version>
    </dependency>
    <!--junit -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>${junit.version}</version>
    </dependency>
    <!-- log4j -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>${log4j.version}</version>
    </dependency>
    <!-- druid -->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>${druid.version}</version>
    </dependency>
    <!--lombok -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>${lombok.version}</version>
    </dependency>
  <!-- mysql数据库的驱动包 -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.12</version>
  </dependency>
    <!--mp代码生成器-->
    <dependency>
      <groupId>com.baomidou</groupId>
      <artifactId>mybatis-plus-generator</artifactId>
      <version>3.2.0</version>
    </dependency>
    <!-- Apache velocity-->
    <dependency>
      <groupId>org.apache.velocity</groupId>
      <artifactId>velocity-engine-core</artifactId>
      <version>2.2</version>
    </dependency>
    <!-- sfl4j -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-simple</artifactId>
      <version>1.7.25</version>
      <scope>compile</scope>
    </dependency>
  <!-- 下面是spring需要的包，包括必备的core、beans、context、aop，与数据库相关的jdbc、tx，同时aop需要用到aspectjweaver包 -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.8.RELEASE</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>5.0.8.RELEASE</version>
  </dependency>
  <!-- spring mvc需要的jar包 -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.0.8.RELEASE</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.0.8.RELEASE</version>
  </dependency>
  </dependencies>
  <!-- 设置JDK编译版本 -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <encoding>UTF-8</encoding>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

### 2.applicationContext.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
       xsi:schemaLocation="http://mybatis.org/schema/mybatis-spring
        http://mybatis.org/schema/mybatis-spring-1.2.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.0.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx-4.0.xsd">

    <!-- 数据源 -->
    <context:property-placeholder location="classpath:db.properties"/>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>

    <!-- 事务管理器 -->
    <bean id="dataSourceTransactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 基于注解的事务管理 -->
    <tx:annotation-driven transaction-manager="dataSourceTransactionManager"/>

    <!-- 配置 SqlSessionFactoryBean
        mybatis提供的：org.mybatis.spring.SqlSessionFactoryBean
        mybatis-plus提供的：com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean
     -->
    <bean id="sqlSessionFactoryBean" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
        <!-- 数据源 mybatis plus配置-->
        <property name="dataSource" ref="dataSource"></property>
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
        <!-- 实体类别名处理 -->
        <property name="typeAliasesPackage" value="com.domain"></property>
    </bean>

    <!--配置 mybatis 扫描 mapper 接口的路径-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.mapper"></property>
    </bean>
</beans>
```

### 3.db.properties配置

```xml
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/stu?useUnicode=true&characterEncoding=UTF-8&useSSL=false
jdbc.username=root
jdbc.password=123456
```

### 4.mybaties-config.xml配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- 开启mybatis-plus打印sql语句-->
        <setting name="logImpl" value="org.apache.ibatis.logging.stdout.StdOutImpl"/>
    </settings>
    <!-- 实体类别名处理 -->
    <typeAliases>
        <typeAlias alias="user" type="com.domain.User" />
    </typeAliases>
<!--    <mappers>-->
<!--        <mapper resource="BlogMapper.xml"/>-->
<!--    </mappers>-->
</configuration>
```

### 5.log4j.xml配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n"/>
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug"/>
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info"/>
    </logger>
    <root>
        <level value="debug"/>
        <appender-ref ref="STDOUT"/>
    </root>
</log4j:configuration>
```

### 6.分页插件

```xml
<!-- spring xml 方式 -->
<property name="plugins">
    <array>
        <bean class="com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor">
            <property name="sqlParser" ref="自定义解析类、可以没有"/>
            <property name="dialectClazz" value="自定义方言类、可以没有"/>
            <!-- COUNT SQL 解析.可以没有 -->
            <property name="countSqlParser" ref="countSqlParser"/>
        </bean>
    </array>
</property>

<bean id="countSqlParser" class="com.baomidou.mybatisplus.extension.plugins.pagination.optimize.JsqlParserCountOptimize">
    <!-- 设置为 true 可以优化部分 left join 的sql -->
    <property name="optimizeJoin" value="true"/>
</bean>
```



### 7.测试

```java
   @Test
    public void test1() throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
        UserMapper userMapper = context.getBean(UserMapper.class);
        List<User> users = userMapper.selectList(null);
        users.forEach(System.out::println);
        }
    }
```

## 三、springmvc

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">


    <!-- 配置推荐使用注解驱动，会默认的加载上面的两个 HandlerMapping, HandlerAdapter -->
    <mvc:annotation-driven />
    <!-- 开启springmvc注解扫描 -->
    <context:component-scan base-package="com.controller"></context:component-scan>
    <!-- 这个是excelView的加载，原生态ssm不需要，所以这里是可以省略的 -->
    <!-- <bean name="excelView" class="cn.usermanage.view.UserExcelView"></bean> -->

    <!-- 视图解析器 -->
    <bean
            class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 前缀,这里是请求的路径文件 -->
        <property name="prefix" value="/WEB-INF/views/"></property>
        <!-- 后缀 ，支持.jsp的请求-->
        <property name="suffix" value=".jsp"></property>
    </bean>
    <!-- 以上是原生态的ssm配置 -->

    <!-- 定义文件上传解析器 -->
    <!-- <bean id="multipartResolver"
    class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    设定默认编码
    <property name="defaultEncoding" value="UTF-8"></property>
    设定文件上传的最大值5MB，5*1024*1024
    <property name="maxUploadSize" value="5242880"></property>
    </bean> -->

    <!-- 解决静态资源被拦截的问题 -->
    <!-- <mvc:default-servlet-handler/> -->


</beans>
```

## web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" version="2.5">
    <display-name>ssmzh</display-name>
    <!-- 加载spring相关配置 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <!-- 这里使用的是以spring*.xml的通配符方式加载配置的 -->
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!--Spring的ApplicationContext 载入 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- 编码过滤器，以UTF8编码 -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <!-- 配置SpringMVC -->
    <servlet>
        <servlet-name>usermanage</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <!-- 指定加载外部的spring-mvc配置文件 -->
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <!-- 这个可以不配，可以省略 -->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>usermanage</servlet-name>
        <!-- 拦截所有的请求，除了jsp。  /xx.html js css 会 -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

</web-app>
```

