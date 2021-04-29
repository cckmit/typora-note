# Spring笔记

## ApplicationContext与BeanFactory区别

核心容器的两个接口引发出的问题:
* ApplicationContext:

  它在构建核心容器时，创建对象采取的策略是采用<b>立即加载</b>的方式。也就是说，只要一读取完配置文件马上就创建配置文件中配置的对象。

* BeanFactory:.

  它在构建核心容器时，创建对象采取的策略是采用<b>延迟加载</b>的方式。也就是说，什么时候恨据id获取对象了，什么时候才真正的创建对象

  ```java
  /*CLassPathXmLApplicationContext：可以加载类路径下的配置文件，要求配置文件必须在类路径下。
  FileSystemXmLApplicationContext：可以加载磁盘任意路径下的配置文件(必须有访间权限）*/	
  		
  /*ApplicationContext*/
          ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
          MailServiceImpl ms = ac.getBean("mailServiceImpl", MailServiceImpl.class);
  /*BeanFactory*/
          BeanFactory factory = new XmlBeanFactory(new ClassPathResource("bean.xml"));
          MailServiceImpl mss = factory.getBean("mailService", MailServiceImpl.class);
  ```

ApplicationContext的实现类:

- CLassPathXmLApplicationContext：可以加载类路径下的配置文件，要求配置文件必须在类路径下。不在就加载不了
- FileSystemXmLApplicationContext：可以加载磁盘任意路径下的配置文件(必须有访间权限）

## Spring注入数据方法

```java
  public Mail(String fromAddress,String toAddress,String subject,String content) {
        this.fromAddress = fromAddress;
        this.toAddress = toAddress;
        this.subject = subject;
        this.content = content;}
  ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        Mail ms = ac.getBean("mail", Mail.class);
        System.out.println(ms.toString());
```

### 构造函数注入

```xml
<!--    优势:
        在获取bean对象时,注入数据是必须的操作，否则对象无法创建成功。
        弊端:
        改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供。 -->
  <bean id="mail" class="com.po.Mail">
        <constructor-arg name="content" value="1"></constructor-arg>
        <constructor-arg name="fromAddress" value="2"></constructor-arg>
        <constructor-arg name="subject" value="3"></constructor-arg>
        <constructor-arg name="toAddress" value="4"></constructor-arg>
    </bean>
```

### Setter注入（常用）

```xml
<!--    优势:
        我们在创建对象时，数据可以不用全部注入
        弊端:
        在获取bean对象时,不能发现对象是否创建成功。 -->
<bean id="mail1" class="com.po.Mail">
        <property name="content" value="1"></property>
        <property name="fromAddress" value="2"></property>
 </bean>
```

### 注入集合数据

```xml
<!--复杂类型的注入/集合共型的注
用于给List结构算合注入的标签:list array set
用于个Map结构华合注入的标签:map、props
结论：结构相同，标签可以互换-->
 <bean id="list" class="com.po.Mail">
        <property name="list"><!--list array set可互换-->
            <list>
                <value>1</value>
                <value>2</value>
            </list>
        </property>
        <property name="map"><!--map、props可互换-->
            <map>
              <entry key="t1" value="1"></entry>
            </map>
        </property>
    </bean>
```

## Spring依赖注入

```html
<IOC的作用：降低程序之间的耦合（依赖关系）>
依赖关系的管理：
以后都交给spring来维护
在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明依赖关系的维护,就称之为依赖注入。
依赖注入:
能注入的数据有三类:
(1)基本类型和String
(2)其他bean类型(在配置文件中或者注解配置过的bean)
(3)复杂类型/集合类型
注入的方式有三种:
第一种:使用构造方法提供
第二种:使用set方法提供
第三种:使用注解提供
```

### 使用set方法注入

```xml
<bean id="accountService" class="com. itheima.service.impl.AccountServiceImpl">
<!--注入dao -->
<property name="accountDao" ref=" accountDao"></property>
< /bean>
```

```java
private AccountDao accountDao;
public void setAccountDao(AccountDao accountDao) { this.accountDao = accountDao;}
```

### 使用注解方法注入

```xml
<!--告知spring在创建容器时要扫描的包-->
<context:component-scan base-package "扫描包路径（如AccountDao所在的包路径）"></context:component-scan>
```

```java
@Autowired
private AccountDao accountDao;
```



## @Component注解

> 用于创建对象的
> 他的作用就和在XL配了文件中编写一个<bean>标签实现的功能是一样的Component:
> 作用:用于把当前类对象存入spring容器中性:
> value:用于指定bean的id。当我们不写时，它的默认位是当的类名，且首字母改小写。                                       使用@Componet后需要在xml中扫描所在包的路径                                                                                                                                                                                                                 

```java
@Componet
public class MailUtil{...} //id默认为mailUtil
```

```xml
 <!-- 使用@Componet后需要在xml中扫描所在包的路径-->
<context:component-scan base-package="包名"></context:component-scan>
```

## 由@Component衍生的注解

> @Controller、@Service、@Repository三个注解为@Component的衍生注解
>
> @Component是任何spring管理的组件或bean的通用构造型。
>
> @Repository是持久层的构造型。
>
> @Service是服务层的构造型。
>
> @Controller是表示层（spring-MVC）的构造型。

## Spring注入数据的注解

> @Autowired、@Qualifier、@Resource注解都只能注入其他bean类型的数据，
>
> 基本类型和string类型无法使用上述注解实现。另外，集合类型的注入只能通过XML来实现。

```java
/**
* 例：出现多个数据类型实现同一个接口时，设置不同的id
*/
	@Repository("mailDao1")
    public class MailDaoImpl1 implements MailDao
    @Repository("mailDao2")
    public class MailDaoImpl2 implements MailDao
```

### @Autowired

```java
  /**
     * @Autowired :
     * 作用:自动按照类型注入。只要容器中有唯一的一个bean对象类型和要注入的变量类型匹配，就可以注入成功
     * 如果ioc容器中没有任何bean的类型和要注入的变量类型匹配，则报错。
   	*出现多个类型匹配时，根据变量名称(mailDao1)查找对应的id
     * 细节:在使用注解注入时,set方法就不是必须的了。
     */
	@Autowired
	private MailDao mailDao1; 
```

### @Qualifier

```java
/**
  * @Qualifier:
  * 作用，在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用。但是在给方法参数注入时可以
  *属性:
  * vaLue:用于指定注bean的id。
*/
 	@Autowired
    @Qualifier(value = "mailDao1")
    private MailDao mailDao;
```

### @Resource

```java
 /**
     * @Resource
     * 作用:直接按照bean的id注入。它可以独立使用属性:
     * name:用于指定bean的id。
     */
    @Resource(name = "mailDao2")
    private MailDao mailDao;
```

### @Value

```java
 /**
    * @value
    * 作用:用于注入基本类型和String类型的数据属性:
    * value:用于指定数据的值。它可以使用spring中SpEL(也就是spring的el表达式)
    * SpEL的写法:${表达式}
*/
@Value("winjay")
private String name;
```

## @Configuration注解

> @Configuration、@Bean、@Import的作用

```java
/**
*@Configuration，spring中的新注解
*该类是一个配置类,它的作用和bean.xmL是一样的*
*作用:指定当前类是一个配置类
*ComponentScan：
*作用:用于通过注解指定spring在创建容器时要扫描的包属性:
*vaLue:它和basePackages的作用是一样的，都是用于指定创建容器时要扫描的包。
*我们使用此注解就等同于在xmL中配置了:
*<context : component-scan base-package="com.itheimo"></context :component-scan>
*/

/**
*@Bean
*作用:用于把当前方法的返回值作为bean对象存入spring的ioc容器中属性:
*name:用于指定bean的id。当不写时，默认值是当前方法的名称细节:
*当我们使用注解配置方法时，如果方法有参数,spring框架会去容器中查找有没有可用的bean对象。查找的方式和@Autowired主解的作用是一样的

@Import
作用:用于导入其他的配置类
属性:
vaLue:用于指定其他配置类的字节码。
当我们使用工mport的注解之后，有Import往解的类就父配置类，而导入的都是子配置类

@PropertySource
作用:用于指定properties文件的位置属性:
vaLue:指定文件的名称和路径。
关键字:cLasspath，表示类路径下
*/
@Configuration
@ComponentScan(basePackages = {"com.service","com.dao"}) //只有一个包时可省略{}
@Import(类名) //使用这个注解可以省略@Configuration注解，通常作用于主配置类上
@PropertySource("classpath:jdbc.properties")
public class ConfigurationTest {}

```

![1619517434074](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1619517434074.png)