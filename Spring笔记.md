# Spring笔记

## 1.ApplicationContext与BeanFactory区别

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

## 2.Spring IOC

> **特点**： Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制
>
> **作用**：降低程序之间的耦合（依赖关系）

### Spring注入数据方法

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

#### 构造函数注入

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

#### Setter注入（常用）

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

#### 注入集合数据

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

### Spring依赖注入

```html
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

#### 使用set方法注入

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

#### 使用注解方法注入

```xml
<!--告知spring在创建容器时要扫描的包-->
<context:component-scan base-package "扫描包路径（如AccountDao所在的包路径）"></context:component-scan>
```

```java
@Autowired
private AccountDao accountDao;
```



## 3.@Component注解

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

### 由@Component衍生的注解

> @Controller、@Service、@Repository三个注解为@Component的衍生注解
>
> @Component是任何spring管理的组件或bean的通用构造型。
>
> @Repository是持久层的构造型。
>
> @Service是服务层的构造型。
>
> @Controller是表示层（spring-MVC）的构造型。

## 4.Spring注入数据的注解

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

## 5.@Configuration注解

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

## 6.Spring AOP

> **作用：**在程序运行期间，不修改源码对已有方法进行增强。
>
> **优势：**减少重复代码提高开发效率维护方便

### 动态代理

> **特点**:字节码随用随创建,随用随加戟
>
> **作用**:不修改源码的基础上对方法增强
>
> **分类**：基于接口的动态代理、基于子类的动态代理

#### **基于接口的动态代理**（Proxy）:

- **涉及的类**：Proxy

- **提供者**：JDK官方

- **如何创建代理对象**：使用Proxy类中的newProxyinstance方法

- **创建代理对象的要求**：被代理类最少实现一个接口,如果没有则不能使用

- **newProxy Instance方法的参数：**

  **ClassLoader类加载器**:它是用于加载代理对象字节码的。和被代理对象使用相同的类加载器。固定写法。

  **Class[]字节码数组**:它是用于让代理对象和被代理对象有相同方法。固定写法。

  **InvocationHandLer**:用于提供增强的代码，它是让我们写如何代理。我们一般都是些一个该接口的实现类，通常情况下都是匿名内部类，但不是必须的。此接口的实现类都是谁用谁写。

```java
//基于接口的动态代理:
IProducer proxyProducer = (IProducer)Proxy.newProxyInstance(
//固定写法 对象.getClass().getClassLoader()
producer.getClass().getClassLoader() ,
//固定写法 对象.getClass().getInterfaces()                          
producer.getClass().getInterfaces(), 
//提供增强的代码的方法，自定义                          
new InvocationHandler(){
/**
作用:执行被代理对象的任何接口方法都会经过该方法
*方法参数的含义
@param proxy 代理对象的引用
@param method 当前执行的方法
*@param args 当前执行方法所需的参数
@return 和被代理对象方法有相同的返回值
*/
@override
public Object invoke(Object proxy，Method method，Object[] args) throws Throwable {
//提供增强的代码
Object returnValue = null;. //获取方法执行的参数
Float money =(Float)args[0]; //判断当前方法是不是销售
if( "saleProduct".equals(method. getName()))
returnValue = method .invoke(producer, money*0.8f); //需要强转的数据类型
}
return returnValue;
});
proxyProducer.setsaleProduct(10000f) //调用接口方法

```

#### **基于子类的动态代理**（ CGlib ）:

- **涉及的类:Enhancer提供者**：第三方 CGlib 库
- **如何创建代理对象**：使用Enhancer类中的create方法创建代理对象的要求:
- **被代理类不能是最终类**
- **create方法的参数**：
  1. **Class**：字节码，它是用于指定被代理对象的字节码。	
  2. **Callback**：提供增强的代码，它是让我们写如何代理。一般都是些一个该接口的实现类，通常情况下都是匿名内部类，但不是必须的。此接口的实现类都是谁用谁写。一般写的都是该接口的子接口实现类:MethodInterceptor

```java
Producer cglbProducer = (Producer)Enhancer.create(
producer.getClass(), 
new MethodInterceptor(){
/**
*执行被代理对象的任何方法都会经过该方法
*@param proxy
*@param method
@param args
以上三个参数和基于接口的动态代理中invoke方法的参数是一样的
*@parammethodProxy :当前执行方法的代理对象
*/
@override
public Object intercept(Object proxy，Method method,Object[] args，MethodProxy methodProxy) throws Thromable {
    //提供增强的代码
    /**
   	。。。。。。。
    */
	return null;
	}
});
cglbProducer.setsaleProduct(12000f) //调用接口方法
```

### AOP相关术语

- **Joinpoint(连接点)**：所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法,因为spring只支持方法类型的
  连接点。
- **Pointcut(切入点)**：所谓切入点是指我们要对哪些Joinpoint 进行拦截的定义。

```java
 //它既是连接点，也是切入点，因为被增强了（实现了接口）
void transfer(String sourceName , String targetName, Float money);
 //它只是连接点，但不是切入点，因为没有被增强（没有被实现）
void test(); 

```

- **Advice(通知/增强**)：所谓通知是指拦截到Joinpoint 之后所要做的事情就是通知。
- **通知的类型**：前置通知,后置通知,异常通知,最终通知,环绕通知。

![1619753732231](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1619753732231.png)

- Target(目标对象)：代理的目标对象。
- weaving(织入)：是指把增强应用到目标对象来创建新的代理对象的过程。spring采用动态代理织入，而Aspect采用编译期织入和类装载器织入。
- Proxy(代理)：一个类被Aop织入增强后，就产生一个结果代理类。
- Aspect(切面)：是切入点和通知(引介)的结合。

> **小结：**
>
> a开发阶段我们做的
> 编写核心业务代码（开发主线):大部分程序员来做，要求熟悉业务需求。
> 把公用代码抽取出来，制作成通知。((开发阶段最后再做）:由编程人员来做。                                                     在配置文件中，声明切入点与通知间的关系，即切面。:AOr编程人员来做。
>
> b运行阶段《Spring框架完成的）
> spring框架监控切入点方法的执行。一旦监控到切入点方法被运行，使用代理机制，动态创建目标对
> 象的代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。

###  基于XML的AOP配置

```xml
    <!--配置spring IOC ， 把mailServiceImpl对象交由spring管理-->
    <bean id="mailServiceImpl" class="com.service.MailServiceImpl" />
    <!--spring中基于XML 配置AOP步骤
        1、把通知bean也交给spring管理
        2、使用aop: config标签表明开始AoP的配百
        3、使用aop:aspect标签表明配置切面
            id属性:是给切面提供—个唯—标识ref展性:是指定通知类bean的Id。
        4、在aop:aspect标签的内部使用对应标签来配置通知的类型
                        测试：前置通知aop: before
                            method属性:用于指定Logger类中哪个方法是前置通知
                            pointcut属性，用于指定切入点表达式，该表达式的含义指的是对业务层中哪些方法增强
                                    切入点表达式的写法:
                                    关键字:execution(表达式)表达式:
                                    访间修饰符返回值 包名..类名.方法名(参数列表)
             标准的表达式写法：execution(public void com.itheima.service.Service.saveAccount())
			全通配写法：execution(* *..*.*(..))
 		5、注意事项：Java代理不可以对类进行代理，只能针对接口代理						-->
      <!--配置Logger类-->
    <bean id="logger" class="com.utils.Logger" />
    <!--配置AOP-->
    <aop:config>
        <!--配置切面-->
        <aop:aspect id="logerAdviser" ref="logger">
            <!--前置通知-->
            <aop:before method="printlog" 
                        pointcut="execution(void com.service.Itest.sendlog())" />
        </aop:aspect>
    </aop:config>
</beans>
```

#### **AOP四种通知类型**

> 前置通知：aop:before
>
> 后置通知：aop:after-returning
>
> 异常通知：aop:after-throwing
>
> 最终通知：aop:after
>
> **后置通知和异常通知一定不会同时出现**

```xml
 <!--前置通知：在切入点方法之前执行-->
<aop:before method="beforelog" pointcut="execution(* com.service..*.*(..))" />
 <!--后置通知：在切入点方法正常执行之后执行-->
<aop:after-returning method="afterlog" pointcut="execution(* com.service..*.*(..))" />
<!--异常通知：在切入点方法出现异常时执行-->
<aop:after-throwing method="throwlog" pointcut="execution(* com.service..*.*(..))" />
<!--最终通知：无论切入点是否出现异常都会最后执行-->
<aop:after method="finallog" pointcut="execution(* com.service..*.*(..))" />
```

#### **通用化切入点表达式**

```xml
<!--配置切入点表达式id属性用于指定表达式的唯一标识。expression属性用于指定表达式内容
                    此标签写在aop:aspect标签内部只能当前切面使用。
                    它还可以写在aop:aspect外面,此时就变成了所有切面可用
				  注意：当xml有约束时，aop:pointcut写在aop:aspect会报错，因此要写在上面-->
<aop:pointcut id="p1" expression="execution(* com.service..*.*(..))" />
<aop:before method="beforelog" pointcut-ref="p1" />
```

#### **环绕通知**

> 环绕通知：aop:around
>
> 它是spring框架为我们提供的一种可以在代码中**手动控制增强方法**何时执行的方式。

```xml
<!--环绕通知
    问题:
      当我们配置了环绕通知之后，切入点方法没有执行，而通知方法执行了。
    分析:
      通过对比动态代理中的环绕通知代码，发现动态代理的环绕通知有明确的切入点方法调用，而我们的代码中没有。
    解决:
     spring框架为我们提供了一个接口:ProceedingJoinPoint。该接口有一个方法proceed()，此方法就相当于明确调用切入点方法。该接口可以作为环绕通知的方法参数，在程序执行时，spring框架会为我们提供该接口的实现类供我们使用。 -->
<aop:around method="aroundlog" pointcut="execution(* com.service..*.*(..))" />
```

```java
  public Object aroundlog(ProceedingJoinPoint point){
        Object value = null ;
        Object[] args = point.getArgs(); //得到方法所需参数
        try {
            //可放前置通知
            value = point.proceed(args); //明确调用业务层方法（切入点方法）
            //可放后置通知
            return value;
        } catch (Throwable throwable) {
            //可放异常通知
            throwable.printStackTrace();
        }finally {
            //可放最终通知
        }
        return value;
    }
```

### 基于注解的AOP配置

> spring的基于注解的AOP调用顺序是：前置通知——切入点方法——最终通知——后置通知（异常通知）
>
> 因此，在开发中要想避免此问题，可用注解的环绕通知实现

```xml
 <!--开启spring创建容器扫描包-->
    <context:component-scan base-package="com"></context:component-scan>
 <!--开启spring aop注解支持-->
    <aop:aspectj-autoproxy/>
```

```java
@Component
@Aspect //设置切面类
public class Logger {
    @Pointcut("execution(* com.service..*.*(..))") //通用化切入点表达式
    public void p1(){}
      @Before("execution(* com.service..*.*(..))") //前置通知
    public void beforelog(){
        System.out.println("前置通知……");
    }
    @AfterReturning("p1()")  //后置通知
    public void afterlog(){
        System.out.println("后置通知……");
    }
    @AfterThrowing("p1()") //异常通知
    public void throwlog(){
        System.out.println("异常通知……");
    }
    @After("p1()") //最终通知
    public void finallog(){
        System.out.println("最终通知……");
    }
    @Around("p1()") //环绕通知
    public Object aroundlog(ProceedingJoinPoint point){
     ........
    }
}
```

## 7.声明式事务

#### 基于XML配置声明式事务

> **配置事务的属性**：
>
> **name**：方法名，如save*表示方法前缀为save （必填）
>
> **propagation**：用于指定事务的传播行为。默认值是REQUIRED，表示一定会有事务，增删改的选择。查询方法可以选择SUPPORTS （最好区分填）
>
> isolation：用于指定事务的隔离级别。默认值是DEFAULT，表示使用数据库的默认隔离级别。
>
> read-only：用于指定事务是否只读。只有查询方法才能设为true。默认是false，表示读写。
>
> timeout：用于指定事务的超时时间、默认值是-1、表示永不超时。如果指定了数值．以秒为单位。
>
> rollback-for：用于指定一个异常，当产生该异常时，事务回滚，产生其他异常时，事务不回滚。没有默认值。表示任何异常都回滚。
>
> no-rollback-for：用于指定一个异常、当产生该异常时，事务不回滚，产生其他异常时事务回滚。没有默认值。表示任何异常都回滚

```xml
<!-- 配置通知 -->
 <tx:advice id="txAdvice" transaction-manager="transactionManager">
 <tx:attributes>
 <!-- 传播行为 -->
 <tx:method name="save*" propagation="REQUIRED" />
 <tx:method name="insert*" propagation="REQUIRED" />
 <tx:method name="add*" propagation="REQUIRED" />
 <tx:method name="find*" propagation="SUPPORTS" read-only="true" />
 <tx:method name="select*" propagation="SUPPORTS" read-only="true" />
 <tx:method name="get*" propagation="SUPPORTS" read-only="true" />
 </tx:attributes>
 </tx:advice>
 <!-- 配置AOP切面 -->
 <aop:config>
 <aop:advisor advice-ref="txAdvice"
 pointcut="execution(* com.teacher.service.*.*(..))" />
 </aop:config>
```

#### 基于注解配置声明式事务

> 1、配置事务管理器
>
> 2、开启spring对注解事务的支持
>
> 3、在需要事务支持的地方使用@Transactional

```xml
 <!-- 开启Spring基于注解的事务管理 -->
 <tx:annotation-driven transaction-manager="dataSourceTransactionManager"/>
```

```java
@Service
@Transactional
public class MailServiceImpl implements MailService {...}
```

