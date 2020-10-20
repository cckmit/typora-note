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



## 1、快速入门

1. springboot工程添加依赖

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

2. 配置数据库

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

   在 Spring Boot 启动类中添加 `@MapperScan` 注解，扫描 Mapper 文件夹：

   ```java
   @SpringBootApplication
   @MapperScan("com.baomidou.mybatisplus.samples.quickstart.mapper")
   public class Application {
   
       public static void main(String[] args) {
           SpringApplication.run(QuickStartApplication.class, args);
       }
   
   }
   ```

3. pojo-dao 

   