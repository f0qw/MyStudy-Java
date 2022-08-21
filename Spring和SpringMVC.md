# Spring

## 一、IOC

### 1、概述

- IoC（Inversion Of Control）控制反转，Spring反向控制应用程序所需要使用的外部资源；
- Spring控制的资源全部放置在Spring容器中，该容器称为IoC容器；
- spring容器中存储的对象称为bean对象；

``` java
IOC(inversion of control): 控制反转,反转的是对象的创建权,用于削减程序之间的耦合关系,底层使用反射技术实现
	传统方式创建对象:  new 对象(); (主动创建)
	IOC方式创建对象: 找容器(被动接收),本质上就是一个Map集合

	作用：解耦
		通过标记(标记就是配置文件中的key)找工厂,工厂帮我们创建对应的类对象,并返回给我们使用
		当前类可以选择主动出击（new的方式）创建对象，但是此时耦合度高。
		把主动式改成被动接收，由工厂对象为当前类生产所必须的关联对象，此时降低了两个类的依赖关系。
控制反转：资源创建的权限（比如dao）由应用方（service）转移到工厂去创建（factory,spring容器去创建）   
```

![image-20220821164921028](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208211649128.png)

<div style="background-color:orange">1.导入Spring的核心jar包的坐标:</div>

``` xml
<dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
  </dependencies>
```

<div style="background-color:orange">2.创建applicationContext.xml,并导入约束:</div>

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- id: 容器中的唯一标识  class:被创建的类的全限定名(包名+类名) -->
    <bean id="accountService" class="com.study.service.impl.AccountServiceImpl"></bean>
</beans>
```

<div style="background-color:orange">3.获取IOC容器:</div>

``` java
a.从类路径下解析xml配置文件,获取容器
// 将配置文件中放在resources
ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
b.从磁盘路径下解析xml配置文件
// 将配置文件方法磁盘目录下
ApplicationContext ac = new FileSystemXmlApplicationContext("C:\Users\Administrator.DESKTOP-NG3ICNP\Desktop\applicationContext.xml");
```

> `ApplicationContex`t: 当解析完applicationContext.xml文件后,立即创建类对象(饿汉加载模式)
> 	它是一个接口。是spring中的IOC容器接口,可以通过该接口的方法来获取想要的bean对象。需要提供bean的id。
>     它有3个常用的实现类
>      	ClassPathXmlApplicationContext★:
> 					它是用于读取类路径(resources)下的xml配置文件，不在类路径下读取不到
>      	FileSystemXmlApplicationContext:
> 					它是读取文件系统中的配置文件，只要有访问权限，在文件系统中都能读取的到
>      	AnnotationConfigApplicationContext:
> 					它是用于根据注解配置 创建容器的。
>             
> (★★)ApplicationContext它是在一读取完配置文件，就马上创建配置文件中所配置的所有对象。(得到IOC容器)
>
> `BeanFactory`:
>      它是spring中ioc容器的顶层接口，ApplicationContext只是它的子接口。
>      它提供创建容器中对象的时机，使用延迟加载(懒加载)的思想。而ApplicationContext不仅继承了它的加载方式，而且还扩展出来了立即加载思想的创建容器的方式。
>      它是每次在使用时才真正的创建对象。



### 2、创建Bean对象的三种方式

``` xml
<!-- 方式1: 无参构造创建对象,存放到IOC容器中 -->
    <bean id="AccountService1" class="com.study.service.impl.AccountServiceImpl"></bean>

    <!-- 方式2: 调用工厂的静态方法创建类对象,存放到IOC容器中 -->
    <!--
        调用工厂的静态方法,将方法的返回值存放到IOC容器中
           id: 存放到IOC容器时,对象的唯一标识
           class: 工厂的全限定名
           factory-method: 指定静态方法名称
    -->
    <bean id="AccountService2" class="com.study.factory.MethodFactory"
          factory-method="createObject"></bean>

    <!-- 方式3: 调用工厂的非静态方法创建类对象,存放到IOC容器中 -->
    <!--3.1: 创建工厂类对象,并存放到IOC容器中 -->
    <bean id="methodFactory" class="com.itheima.factory.MethodFactory"></bean>
    <!--3.2: 调用工厂实例的非静态方法创建类对象 -->
    <!--
        调用工厂的非静态方法,将非静态方法的返回值存放到IOC容器中
            id:存放到IOC容器时,对象的唯一标识
            factory-bean:工厂对象实例的引用
            factory-method:调用实例工厂的非静态方法
    -->
    <bean id="AccountService3" factory-bean="methodFactory" factory-method="createObject1"></bean>
```



### 3、Bean对象的作用范围

``` xml
<bean id="" scope="singleton" class=" "></bean>
```

``` tex
bean的作用范围调整：
    调整bean的作用范围，使用的是bean标签的scope属性。
    它的属性取值有以下5个
      singleton           : 单例对象      用的最多的           它是默认值(最常用)
      prototype           ：多例对象       用的最多的
      ------------------------------------
      request             ：请求范围      (存入了请求域)
      session             ：会话范围      (存入了会话域)
      global-session      ：全局会话范围   当非集群环境下，它就是session
-------------
如果是有状态的bean选择多例；
如估无无状态的bean选择单例；
```

### 4、Bean对象的生命周期

bean对象的生命周期: 从加载到内存哪一刻起对象就出生了,当对象从内存中移除时,对象的生命就完结了

``` tex
bean的生命周期:
    单例对象: 将创建的bean放入容器
    	出生:容器创建，对象出生
    	活着:只要容器在，对象就一直活着
    	死亡:容器销毁，对象消亡
    特点: 生命周期与容器相同
	<!--
        单例对象:
            init-method: 初始化方法（构造器初始化对象之后被调用）
            destroy-method: 销毁时调用的方法（对象被销毁之前被调用）
    -->
    <bean id="AccountService" class="com.study.service.impl.AccountServiceImpl"
          init-method="init" destroy-method="destory"></bean>

多例对象：
    出生：每次使用时创建
    活着：在使用过程中
    死亡：使用完成之后，等待垃圾回收器回收。gc，也就是说多例模式下spring不负责对象的销毁，不会执行destroy方法；
    	多实例bean对象的生命周期
            scope的值必须为: prototype
```



## 二、依赖注入DI

### 1、依赖注入概述

- IoC与DI是同一件事站在不同角度看待问题

  ​    在<font color='red'>应用程序的角度</font>看程序需要被动等待Spring提供资源（注入资源）是DI;

  ​    但是在<font color='red'>Spring容器的角度</font>看是，是将资源的创建权利做了翻转，且由容器提供数据，是IOC;

### 2、注入数据的方式和类型

```java
Spring中的依赖注入：
    注入的方式有三种：
        第一种：使用构造方法注入
            要求：必须有对应参数列表的构造函数
        第二种：使用setter方法注入（XML开发主流）
            要求：提供被注入对象的set方法（不需要get方法）
        第三种：使用注解注入

    注入的数据类型有三类：
        第一类：基本类型和String
        第二类：其他bean类型
            要求：其他bean指的是在spring的配置文件中定义过的bean，或者是用注解注释过的类。
        第三类：复杂类型（集合类型）
            Array: 数组
            List:
            Map:
            Properties:
```

<div style="background-color:orange">示例代码:</div>

```java
<!-- 依赖注入:
            在创建对象时,给对象中的属性赋值
         依赖注入方式:
            方式1: 有参构造
            方式2: setter方法
            方式3: 注解
         依赖注入的数据类型:
            基本类型和String
            其他bean对象
            数组
            List
            Map
            Properties
     -->
    <!-- 方式1: 有参构造 -->
    <!--<bean id="xxx" class="com.study">
        <constructor-arg name="port" value="3306"></constructor-arg>
    </bean>-->
    <!-- 方式2: 通过setter方法注入 ****-->
    <bean id="xxx" class="com.study">
        <property name="port" value="3306"></property>
        <!-- 注入其他bean类型 -->
        <property name="user" ref="user"></property>
        <!-- 注入数组 -->
        <property name="strArr">
            <array>
                <value>宝强</value>
                <value>乃亮</value>
                <value>羽凡</value>
                <value>大郎</value>
            </array>
        </property>
        <!-- 注入list -->
        <property name="list">
            <list>
                <value>蓉蓉</value>
                <value>璐璐</value>
                <value>合合</value>
                <value>莲莲</value>
            </list>
        </property>
        <!--  注入map-->
        <property name="map">
            <map>
                <entry key="name" value="波波"></entry>
                <entry key="age" value="18"></entry>
                <entry key="sex" value="妖"></entry>
            </map>
        </property>
        <!-- 注入properties -->
        <property name="prop">
            <props>
                <prop key="name">昌老师</prop>
                <prop key="age">40</prop>
            </props>
        </property>
    </bean>
    
    
    <!-- 创建user对象存放到IOC容器中 -->
    <bean id="user" class="com.study.User">
        <property name="username" value="xxx"></property>
    </bean>
</beans>
```

### 3、解析property配置文件

``` xml
<!-- 解析Properties配置文件,将配置文件中的数据存放到IOC容器中 -->
    <context:property-placeholder location="classpath:jdbc.properties" file-encoding="utf-8"></context:property-placeholder>
```



## 三、注解方式

### 1、启动注解功能

- 在spring XML配置文件中启动注解扫描，加载类中配置的注解项

  ```xml
  <context:component-scan base-package="packageName"/>
  ```

- 说明：

  - 在进行包所扫描时，会<font color='red'>对配置的包及其子包中所有文件</font>进行扫描;

  - 扫描过程是以文件夹递归迭代的形式进行的;

  - 扫描过程仅读取合法的java文件

  - 扫描时仅读取spring可识别的注解

  - 扫描结束后会将可识别的有效注解<font color='red'>转化为spring对应的资源加入IoC容器</font>

- 注意：

  - **无论是注解格式还是XML配置格式，最终都是将资源加载到IoC容器中**，差别仅仅是数据读取方式不同
  - 从加载效率上来说注解优于XML配置文件

### 2、定义bean的常用注解

- 名称：@Component    @Controller    @Service    @Repository

- 类型：**类注解**

- 位置：**类的定义上方**

- 作用：设置该类为spring管理的bean

- 范例：

  ```java
  @Component("userService")
  public class AccountServiceImpl{}
  ```

- 说明：

  - 以上注解相当于xml 中的bean标签：

    ~~~xml
    <bean id="userService" class="com.itheima.service.impl.UserServiceImpl"></bean>
    ~~~

  - @Controller、@Service 、@Repository是@Component的衍生注解，功能同@Component

  - @Controller(控制层注解)、@Service（服务层注解） 、@Repository（持久层注解）只是提供了更加明确的语义化(见名知意)，精确指出是哪一层的对象,但不是强制要求的；

- 相关属性

  - value（默认）：定义bean的访问id

### 3、bean的属性数据注入常用注解

``` xml
以下注解它们就相当于：
<property name="userDao" ref="userDao" 或 value="基本类型数据或String"></property>
------------------------
这些注解使用在属性上,用于给对象中的属性进行赋值
引用类型：
    @Autowired
    @Qualifier
    @Resource
基本类型+String：
    @Value   可以支持Spring的EL表达式(SPEL)，写法就是${表达式}
```

### 4、作用范围和生命周期相关注解

``` java
@Scope: 相当于bean标签的scope属性
	作用：用于调整bean的作用范围
	使用位置: 被创建的类上
	属性：
		value：指定作用范围的取值。取值是固定的5个，和XML的配置取值是一样的。
			singleton: 单实例 默认值
			prototype: 多实例
@PostConstruct : 使用在方法上
	使用位置: 初始化的方法上
	​作用：指定初始化方法，相当于init-method
@PreDestroy : 使用在方法上
	​作用：指定销毁方法，相当于destroy-method
```

### 5、纯注解格式★

``` java
我们需要使用配置类代替配置文件,将以下注解写在配置类上

@Configuration: 声明该类为Spring的配置文件类
@ComponentScan(basePackages = "com.itheima") : 指定要扫描的包
@PropertySource(value = "classpath:jdbc.properties") : 将配置文件交给Spring容器管理
@Bean() : 将方法返回的对象存放到IOC容器中
	将返回值存放到IOC容器中
	一般将【第三方提供的类】对加载到IOC容器
```

例子：

``` java
**
 * Spring的核心配置类
 */
@Configuration // 声明配置类
@ComponentScan("com.itheima") // 开启包扫描
@PropertySource("classpath:jdbc.properties") // 解析properties配置文件
public class SpringConfig {

    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean("template")
    public JdbcTemplate getJdbcTemplate(@Qualifier("dataSource") DataSource dataSource){
        JdbcTemplate template = new JdbcTemplate();
        template.setDataSource(dataSource);
        return template;
    }
```

### 6、Spring整合junit

使用步骤:

``` java
1.导入jar包坐标
	<!-- 引入单元测试的jar包 4.12以上 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    <!-- 导入Spring整合junit的jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.0.2.RELEASE</version>
    </dependency>
2.使用
	在测试类上添加以下两个注解:
	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration(locations="classpath:applicationContext.xml") (配置xml文件的路径)
	或者：
	@ContextConfiguration(classes=SpringConfig.class) (配置类)
```

## 四、Spring-AOP

### 1、相关概念

- Target（目标对象）:   要增强的对象(被代理的类对象)

- Proxy（代理对象） :  对目标对象的增强的对象 (生成的代理类对象)

- Joinpoint（连接点）:  目标对象中的所有方法(被代理类中的所有方法)--》可以被增强的方法

- Pointcut（切入点）:   要被增强的方法(被代理类中要增强的方法)

- Advice（通知/增强）:  增强的那段【代码方法】以及代码切入的位置

  ​				通知包含了2个维度内容：**在增强方法的什么位置，切入什么代码；**

  	前置通知：在方法之前进行增强
  	
  	后置通知 ：在方法之后进行增强
  	
  	异常通知：在方法异常进行增强
  	
  	最终通知 ：最终执行的方法进行增强
  	
  	环绕通知:  单独使用（以上所有通知）

- **Aspect(切面):** 切入点+通知=切面

​				目标方法和增强方法合到在一起 叫做切面

​				说白了切面描述了在什么样的目标方法下(切入点)的什么位置（通知类型）切入了什么样的(增强代码）

- Weaving（织入）：

  ​          将增强的代码（方法）合并到目标方法的过程 叫织入过程;	

Spring AOP底层实现技术就是动态代理

spring底层会自动抉择使用Jdk Proxy或Enhancer(cglib)

### 2、AOP开发方式

- XML方式

- XML+注解方式

- 注解方式



### 3、全xml方式实现AOP【最繁琐的】

> 例子：
>
> 对AccountServiceImpl下的方法进行增强;
> 步骤如下：
> 1.导入相关jar坐标;  
> 2.自己封装要增强方法，并保存在某个专用类中【切面类/通知类/增强类】
> 3.将所有进行AOP操作的资源【切面类+目标对象】加载到IOC容器中；    
> 4.编写配置文件
> 	告诉spring【切面类中的增强方法】在哪个对象的哪个方法上增强使用（配置切面）；

``` xml
<!-- 将目标类(被增强的类)存放到IOC容器中 -->
    <bean id="accountService" class="com.study.service.impl.AccountServiceImpl"></bean>

    <!-- 将切面类加载到IOC容器中 -->
    <bean id="myAspect" class="com.study.aspect.MyAspect"></bean>

    <!-- 面向切面编程====织入 -->
    <aop:config>
        <!-- 切入点表达式 -->
        <!-- 指定切面类 -->
        <aop:aspect ref="myAspect">
            <!-- method: 调用的通知方法  pointcut: 切入点是什么 -->
            <aop:before method="before" pointcut="execution(public * com.study.service.impl.AccountServiceImpl.*(..))"></aop:before>
            <aop:after-returning method="afterReturning" pointcut="execution(public * com.itheima.service.impl.AccountServiceImpl.*(..))"></aop:after-returning>
            <aop:after-throwing method="afterThrowing" pointcut="execution(public * com.itheima.service.impl.AccountServiceImpl.*(..))"></aop:after-throwing>
            <aop:after method="after" pointcut="execution(public * com.study.service.impl.AccountServiceImpl.*(..))"></aop:after>

            <aop:around method="around" pointcut="execution(public * com.study.service.impl.AccountServiceImpl.*(..))"></aop:around>
        </aop:aspect>
    </aop:config>
```

#### 切点表达式

``` java
切入点表达式:
作用: 找到那些被增强的方法,类似正则表达式
格式:
    修饰符 返回值 方法的全限定名(参数列表)
    public void com.study.service.impl.AccountServiceImpl.delete()
    说明：修饰符可省略不写，默认public    
支持通配符的写法：
    *   ： 表示任意字符串
    ..  ： 任意重复次数
```

``` java
execution(* *(..)) //说明：第一个*表示任意返回值类型，第二个*匹配任意类，..匹配任意参数，该表达式表示匹配任意类的任意方法（简化书写方式，很少用到）
execution(* *..*(..))//同上
execution(* *..*.*(..))//同上，更符合书写习惯，第一个*表示匹配任意返回值类型，*..表示匹配任意包名称及其任意子包，第三个*匹配任意类，第四个匹配任意方法
execution(public * *..*.*(..))//表示匹配任意public修饰符的方法
execution(public int *..*.*(..))//表示陪陪任意返回值是int类型且修饰符是public的方法
execution(public void *..*.*(..))//匹配任意返回值是void且修饰符是public的方法
execution(public void com..*.*(..))//匹配com包及其任意子包下的被pulic修饰的返回值是void的方法
execution(public void com..service.*.*(..))//匹配com包下任意子包下存在service包下的任意类的任意方法，且方法为void 被public修饰
execution(public void com.study.service.*.*(..))
execution(public void com.study.service.User*.*(..))//侧重点：任意一User开头的类下的任意void且被public修饰的方法
execution(public void com.study.service.*Service.*(..))//侧重点：任意一Service结尾的类下的任意方法
execution(public void com.study.service.UserService.*(..))//匹配UserService类下的任意方法...
execution(public User com.study.service.UserService.find*(..))//侧重点：匹配UserService类下的任意以find开头的任意方法...
execution(public User com.study.service.UserService.*Id(..))//侧点：匹配UserService类下的任意以id结尾的任意方法...
execution(public User com.study.service.UserService.findById(..))//侧重点：匹配UserService类下方法名为findById的任意参数的方法....
execution(public User com.study.service.UserService.findById(int))//精准匹配，方法入参只有一个int类型
execution(public User com.study.service.UserService.findById(int,int))//精准匹配，方法入参包含2个int类型
execution(public User com.study.service.UserService.findById(int,*))//2个入参，第一个必须int，第二个任意类型
execution(public User com.study.service.UserService.findById(*,int))//2个入参，第一个任意类型，第二个必须int类型
execution(public User com.study.service.UserService.findById())//精准匹配无参
execution(List com.study.service.*Service+.findAll(..))//匹配任意以Service结尾的接口或者实现类下方法名为findAll的任意入参方法
```

### 4、半xml半注解实现AOP

#### 使用步骤:

<div style='background-color:orange;'>1.开启包扫描</div>

```xml
<!-- 开启需要扫描的包 -->
<context:component-scan base-package="com.study"></context:component-scan>
<!-- 声明当前项目可以使用AOP的注解 -->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

<div style='background-color:orange;'>2.在切面类中添加相关注解</div>	

```java
注解：
	@Aspect ： 声明切面类
	@PonitCut：定义公共的切入点
			配置到空方法上，value：切入点表达式
			引用：方法名()
配置通知类型：
    @Before() : 前置通知
    @AfterReturning() ： 后置通知
    @AfterThrowing()  ：异常通知
    @After()			：最终通知
    @Around()			：环绕通知
```

#### 相关代码:

**1.导入jar包坐标**

```xml
<dependencies>
    <!-- spring核心jar包,已经依赖的AOP的jar -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.2.RELEASE</version>
    </dependency>
    <!-- 切入点表达式 -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.6.8</version>
    </dependency>
</dependencies>
```

**2.切面类**

```java
/**
 * 此类我们称之为切面类,在切面类中存放有各种通知
 * 切面 = 切入点 + 通知
 * @Aspect : 声明此类为切面类
 */
@Aspect
@Component
public class MyAspect {

    /**
     * 配置切入点表达式
     * 调用切入点表单时,直接使用 方法名称()
     *      pt()
     */
    @Pointcut("execution(* com.itheima.service.*.AccountServiceImpl.*(..))")
    public void pt(){}

    /**
     * 前置通知调用的方法
     */
    @Before("pt()")
    public void before(){
        System.out.println("前置通知:11111111111111");
    }
    /**
     * 后置通知调用的方法
     */
    @AfterReturning("pt()")
    public void afterReturning(){
        System.out.println("后置通知:22222222222222");
    }

    /**
     * 异常通知调用的方法
     */
    @AfterThrowing("pt()")
    public void afterThrowing(){
        System.out.println("异常通知:33333333333333");
    }

    /**
     * 最终通知调用的方法
     */
    @After("pt()")
    public void after(){
        System.out.println("最终通知:44444444444444");
    }

    /**
     * 环绕通知
     * @param joinPoint 连接点 ---> 切入点
     *        ProceedingJoinPoint: method
     * @return
     */
    @Around("pt()")
    public Object around(ProceedingJoinPoint joinPoint){
        Object result = null;
        try {
            System.out.println("前置增强--111111111111");
            // 让原方法执行  method.invoke()
            result = joinPoint.proceed();
            System.out.println("后置增强--222222222222");
        } catch (Throwable throwable) {
            //throwable.printStackTrace();
            System.out.println("异常增强--333333333333");
        }finally {
            System.out.println("最终增强--444444444444");
        }
        return result;
    }
}
```

**4.applicationContext.xml配置**

```xml
 <!-- 开启组件扫描 -->
    <context:component-scan base-package="com.study"></context:component-scan>
 <!-- 开启对Spring-AOP注解的支持 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

### 5、全注解实现AOP

@EnableAspectJAutoProxy ： 开启对AOP注解的支持

##### aop全注解代码

**配置类代码**

```java
@Configuration // 声明此类为Spring的配置类
@ComponentScan("com.study") // 设置需要扫描的包(开启对Spring注解的支持)
@EnableAspectJAutoProxy // 开启对AOP注解的支持
public class SpringConfig {
}
```

**切面类代码同上**



# SpringMVC

## 一、回顾Servlet

Servlet:
	运行在服务器上的java小程序.
    本质上就是java代码,这个java代码可以被web服务器调用

【1】生命周期:

```java
实例化,创建一个对象放内存
    默认第一次请求Servlet时,会创建Servlet对象,并调用init方法进行初始化操作
    load-on-startup: 设置servlet的初始化时机
    取值: 正整数,值越小优先级越高
    初始化,创建完对象后,调用对象的方法进行初始化 
    init(): 初始化方法
    	当对象实例化完毕后,立即调用init方法完成初始化工作, 1次
    service(): 提供服务
    	每次请求来的时候,tomcat都会调用service方法提供服务, 请求一次执行一次
    destory(): 销毁方法
    	当对象从内存中移除前调用destory方法进行销毁工作, 1次
```

【2】Servletconfig

``` java
1.web.xml配置：
	<servlet>
           	 <servlet-name>servletDemo</servlet-name>
           	 <servlet-class>com.itheima.web.servlet.ServletDemo</servlet-class>
            	<!--配置初始化参数-->
            	<init-param>
                   <!--用于获取初始化参数的key-->
                	<param-name>key</param-name>
                    <!--初始化参数的值-->
                	<param-value>value</param-value>
            	</init-param>
         </servlet>	
          2.java代码
            @Override
            public void init(ServletConfig config) throws ServletException {
                //get config and do something
                 config.getInitParameter("key");
            }  
```

【3】ServletContext

``` java
ServletContext对象，它是应用上下文对象；
           每一个应用有且【只有一个】ServletContext对象；
           它可以实现让应用中所有Servlet间的数据共享；
          eg: 1.web.xml配置：
                <!--配置应用初始化参数-->
                <context-param>
                    <!--用于获取初始化参数的key-->
                    <param-name>servletContextInfo</param-name>
                    <!--初始化参数的值-->
                    <param-value>This is application scope</param-value>
                </context-param>
             2.java代码示例：
                getServletContext();
                getInitParameter("servletContextInfo");
```



## 二、SpringMVC概述

**SpringMVC** 顾名思义就是Spring对MVC架构的一种实现，属于轻量级的WEB框架。 

​	它通过一个简单的**注解**就可以让一个普通的Java类成为控制器，这种**低侵入性的设计**使得他备受业界欢迎 

​	同时他还支持RestFul风格的编程风格。

```java
SpringMVC是spring对web层进行的封装,提供的MVC架构
    M（模型）: 
		Model可以叫做数据模型层，说白了就是用来封装数据的
        例如：用户发送注册请求，那么请求的信息会被SpingMMVC封装到User实体类中，这个实体类就属于Model层；
             用户发送查询个人信息的请求，那么后台也会将个人信息封装到一个User类型，数据同样也是Model层；
            
	V（视图）: 
		View说白了就是SpringMVC响应请求的一种数据展示（最终的执行结果）：
		例如：SpringMVC响应的数据方式：JSP、json、xml、html等

    C（控制）: 
		控制层就用来处理交互的部分，接收用户请求，然后执行业务等流程，最终反馈结果；
	    控制器,本质上就是一个Servlet,一切请求都访问这个Servlet,在这个Servlet中进行调度
            SpringMVC可以通过一个简单的注解,就能让浏览器访问到对应的方法.
            @RequestMapping("/请求路径")
    总之，这个唯一的Servlet核心控制器会【根据请求的url匹配基于注解的url】，完成请求处理；
```

![image-20220821224047251](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208212240313.png)

### 1、核心Servlet配置文件

编写web.xml文件

在web.xml文件中配置核心控制器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
		  http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
           version="3.0">
    <!--
        服务器启动时只会加载web项目的核心配置文件 web.xml
    -->

    <!-- 配置SpringMVC的核心Servlet: 前端控制器 (由SpringMVC框架提供)-->
    <servlet>
        
        <servlet-name>dispatcherServlet</servlet-name>
        
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置SpringMVC的核心配置文件 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!-- 我们给前端控制器配置的路径为 / (缺省匹配)可以匹配浏览器发送的所以请求
             注意: jsp除外
         -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>

```

### 2、SpringMVC配置文件

spring-mvc.xml约束头

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 开启包扫描 -->
    <context:component-scan base-package="com.itheima"></context:component-scan>

    <!-- 配置视图解析器: 生成前缀和后缀 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

    <!-- 将可能使用到的处理器适配器,处理器映射器,视图解析器统统的加载到内存中 -->
    <mvc:annotation-driven/>
</beans>
```



## 三、原理

#### 【1】启动初始化顺序

​		资源准备阶段：

​		（1）打包好war包，部署到Tomcat中==》mavne的自动发布插件完成

​		（2）Tomcat启动，加载web.xml文件

​		（3）初始化前端控制器：dispatcherServlet配置

​		（4）根据**contextConfigLocation**加载springmvc.xml配置

​		（5）根据spring-mvc.xml配置完成spring容器的初始化；

​			时序图：

![image-20191203171050122](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208212247249.png)

#### 【2】请求处理顺序

（1）浏览器发起 http://localhost:8080/hello 请求

（2）Tomcat调用dispatcherServlet拦截/hello

（3）dispatcherServlet调用一个处理器，根据请求路径[/hello]找对对应的处理方法（这里被@RequestMapping注释的方法）【处理器映射器】

```properties
处理器映射器：处理浏览器请求，根据路径映射到指定发方法；
eg:
/hello----->com.heima.controller.HelloController#hello()
```

（4）dispatcherServlet找到方法后，调用另外一种处理器，执行HelloController中的hello()方法【处理器适配器】

```properties
处理器适配器：
1.适配处理器handler类型（在这里对应的处理器是hello()方法）；
2.处理入参，把对应的参数直接封装为一个实体；
```

（5）HelloController中的hello()方法有返回值，创建ModelAndVIew（包含响应数据，页面）

（6）dispatcherServlet调用视图解析器，组装响应对象，然后把页面返回给浏览器【视图解析器】

```properties
视图解析器：把方法返回的ModelAndVIew，进行页面和数据的整合，标签根据传入的view进行页面跳转
```

![image-20220821224827632](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208212248714.png)

- 前端控制器：DispatcherServlet 是整体流程控制的中心，由其调用其它组件处理用户的请求， 有
  效的降低了组件间的耦合性；

- 处理器：handler,业务处理的核心类，通常由我们自己编写,比如：HelloController类等；

- **处理器映射器**：负责根据URL请求找到对应的Handler处理器(就是根据请求URL与Controller方法的关系)
- **处理器适配器**：将请求参数解析转换成处理器的入参，并调用处理器执行的组件； 
- **视图解析器**：将处理器执行的结果生成View视图（将逻辑视图转换成物理视图）
- 视图：View，最终产出结果， 常用视图如：jsp、 html  

![image-20200426183313406](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208212249484.png)

## 四、常用注解

``` java
@RequestMapping
	类上:
		窄化类,访问此类中的方法时,必须加上类上的路径
	方法:
		建立绑定路径与方法的对应关系
           常用属性 :
		value或者path: 用来指定路径,默认选项 
		method: 用来限定请求的方式 
               例如：RequestMapping(value = "/test03",method = {RequestMethod.GET,RequestMethod.POST})
		params: 用来限定请求参数 
               例如：@RequestMapping(value = "/test04",params = {"username","password"})
		headers: 用来限定请求必须携带指定的请求头
		以上的属性可以单独使用，也可以组合使用，组合使用时，必须同时满足才行。
@GetMapping()  : 只能处理get请求
@PostMapping() : 只能处理post请求
@PutMapping() : 只能处理put请求
@DeleteMapping() :只能处理delete请求
```

## 五、获取请求参数

SpringMVC支持的参数类型有 

```java
1.基本数据类型和string
2.vo类型(View object,浏览器携带的所有数据封装成的对象叫做vo)  
     	要求：请求参数的名称必须和对象中的属性名保持一致
3.复杂类型：    
	3.数组类型 
     		要求：保证前端传递的参数名称跟方法中的数组形参名称一致就好。 
	4.集合类型 
     		获取集合参数时，要将集合参数包装到一个Vo中才可以。
说明：
如果请求中携带这些类型的数据,SpringMVC会自动帮我们接收,并传递给方法进行使用,【方法上的形参名称必须要和请求参数名称保持一致】；
```

## 六、特殊情况

#### 1、日期处理

```java
如果请求携带的参数为日期类型,SpringMVC没有提供相关的转换器,哪该如何处理呢?
我们可以使用SpringMVC提供的注解进行日期格式化操作.
@DateTimeFormat(pattern = "yyyy-MM-dd")
说明：
    此时springmvc.xml中必须要开启注解驱动： <mvc:annotation-driven/>    
```

#### 2、编码过滤器

```xml
若请求携带了中文有乱码的情况
   在SpringMVC中已经提供好了编码过滤器,可以直接使用即可
    <!-- 在web.xml中配置springMVC编码过滤器 -->  
	<filter>  
		<filter-name>CharacterEncodingFilter</filter-name>  
		<filter-class>
			org.springframework.web.filter.CharacterEncodingFilter
		</filter-class>  
		<!-- 设置过滤器中的属性值 -->  
		<init-param>  
			<param-name>encoding</param-name>  
		    	      <param-value>UTF-8</param-value>  
		</init-param>
	</filter>  
	<!-- 过滤所有请求 -->  
	<filter-mapping>  
		<filter-name>CharacterEncodingFilter</filter-name>  
		<url-pattern>/*</url-pattern>  
	</filter-mapping>
```

#### 3、请求参数名称不一致

@RequestParam : 主要用于在SpringMVC后台控制层获取参数时，前端传入的参数和方法形参不一致时。

```java
它支持三个属性: 
	value：默认属性，用于指定前端传入的参数名称 
	required：用于指定此参数是否必传 
	defaultValue：当参数为非必传参数且前端没有传入参数时，指定一个默认值。
```

```java
<a href="/params/demo8?username=张三">请求参数-基本类型</a> <br>
------------------------------
@RequestMapping("demo8")
public String demo1(@RequestParam(defaultValue = "tom",required = false,value = "name") String username){
    System.out.println(username);
    return "success";
}
```

#### 4、获取请求头信息

@RequestHeader

**作用:** 主要用于从请求头中获取参数。它支持的属性跟@RequestParam一样。 

```java
@RequestMapping("demo2")
public String demo2(@RequestHeader("cookie") String cookie, HttpServletResponse response){
    System.out.println(cookie);
    return "success";
}
```

#### 5、获取cookie信息

@CookieValue

**作用:** 用于从cookie中取值。 

```java
注意: cookie的key值区分大小写
@RequestMapping("demo3")
public String demo3(@CookieValue("JSESSIONID") String jsessionid){
    System.out.println(jsessionid);
    return "success";
}
```

## 七、同步请求的响应

**SpringMVC返回值的方式可以分为两大类: **

​	 **1.同步请求的响应:**

​			请求**转发 或 重定向**

​	 **2.异步请求的响应**

​			异步指的是前端的请求的方式：ajax

​			ajax请求直接返回响应结果字符串

### 1、原生API和SpringMVC实现转发

``` java
 /**
     * 请求转发:
     *      原生API实现
     */
    @RequestMapping("/handler01")
    public void handler01(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("handler01方法执行了...");
        // 请求转发到 /pages/success.jsp
        request.getRequestDispatcher("/pages/success.jsp").forward(request,response);
    }

    /**
     * 请求转发:
     *      SpringMVC方式:
     *          forward:物理视图
     */
    @RequestMapping("/handler02")
    public String handler02(){
        System.out.println("handler02方法执行了...");
        // forward:物理视图路径
        return "forward:/pages/success.jsp";
    }

    /**
     * 请求转发:
     *      SpringMVC方式:
     *          逻辑视图 + 视图解析器
     */
    @RequestMapping("/handler03")
    public String handler03(){
        System.out.println("handler03方法执行了...");
        return "success";
    }
}
```

### 2、原生API和SpringMVC实现重定向

``` java
// 重定向-原生API
    @RequestMapping("/handler04")
    public void handler04(HttpServletResponse response) throws IOException {
        System.out.println("handler04方法执行了...");
        response.sendRedirect("/pages/success.jsp");
    }


//SpringMVC方式:
    @RequestMapping("/handler05")
    public String handler05() {
        System.out.println("handler05方法执行了...");
        return "redirect:https://www.baidu.com";
    }
```

### 3、原生API和SpringMVC以流的形式写回字符串

``` java
  /**
     * 以流的形式写回字符串
     *      原生API: HttpServletResponse
     * @return
     */
    @RequestMapping("/handler06")
    public void handler06(HttpServletResponse response) throws IOException {
        response.setContentType("text/html;charset=utf-8");
        System.out.println("handler06方法执行了...");
        response.getWriter().print("原生API响应的数据...");
    }
    /**
     * mime常用类型:
     *          *.html  text/html
     *          *.js    text/JavaScript
     *          *.png   image/png
     *          *.json  application/json
     * 以流的形式写回字符串
     *      SpringMVC方式:
     *          @ResponseBody + String
     *  RequestMapping参数:
     *      produces: 设置响应参数的mime类型
     * @return
     */
    @RequestMapping(value = "/handler07",produces = "text/html;charset=utf-8")
    @ResponseBody
    public String handler07() {
        System.out.println("handler07方法执行了...");
        return "SpringMVC写回的字符串";
    }
}
```

### 4、响应时数据共享问题

> 请求转发: 数据都会存放到Request域中
> 重定向: 将共享的数据拼接在请求路径的后面

#### 【1】转发响应时

``` java
//使用request域对象封装响应结果
public String demo1(HttpServletRequest request){
    request.setAttribute("user","admin");
    return "success";
}
//使用Model封装结果
public String demo2(Model model){
    model.addAttribute("user","admin");
    return "success";
}
//使用ModelAndView封装结果 
public ModelAndView demo3(ModelAndView modelAndView){
    modelAndView.addObject("user","admin");
    modelAndView.setViewName("success");
    return modelAndView;
}


```

#### 【2】重定向时

数据存放到session域下，会占用服务器内存空间；【不可取】

重定向时，request域下的数据不能共享，但是使用Model或者ModelAndView可以自动将数据以参数的方式拼接到请求路径中；

``` java
@RequestMapping("/test04")
    public String test04(Model model){
        System.out.println("test04方法执行了...");
        model.addAttribute("msg","你若安好,便是晴天");
        return "redirect:/pages/success.jsp";
    }

    @RequestMapping("/test05")
    public ModelAndView test05(ModelAndView modelAndView){
        System.out.println("test05方法执行了...");
        // 封装转发携带的数据
        modelAndView.addObject("msg1","情绪是智慧不够的产物!");
        modelAndView.addObject("msg2","哈哈哈哈");
        // 封装视图
        modelAndView.setViewName("redirect:/pages/success.jsp");
        return modelAndView;
    }
```



## 八、异步请求的响应

测试环境：vue+springmvc

### 1. 使用Response 原生API响应结果

<div style='background-color:orange;'>示例1:</div>

```java
/**
 * 原生API生成响应信息
 * @param name
 * @param age
 * @param response
 * @throws IOException
 */
@RequestMapping("/demo1")
public void demo1(String name, Integer age, HttpServletResponse response)
        throws IOException {
    response.setContentType("application/json;charset=utf-8");
    System.out.println("demo1方法执行了.."+name+" : "+age);
    response.getWriter().print("hello..."+name);
}
```

### 2. 使用@ResponseBody注解

#### 返回普通字符串

<div style='background-color:orange;'>示例2:</div>

```java
    /**
     * 获取请求携带的json字符串,将字符串存放到一个变量中
     * @RequestBody:
     *      获取请求体中的字符串
     * @param str
     * @return
     */
    @RequestMapping(value = "/test03",produces = "text/html;charset=utf-8")
    @ResponseBody
    public String test03(@RequestBody String str){
        System.out.println("test03方法执行了");
        System.out.println(str);
        return "你好";
    }
    /**
     * 获取请求携带的json字符串,将字符串存放到一个变量中
     * @RequestBody:
     *      注意: 需要导入jackson的jar包
     *      解析请求体中的json字符串,并将数据封装到vo对象中
     * @param userVo
     * @return
     */
    @RequestMapping(value = "/test04",produces = "application/json;charset=utf-8")
    @ResponseBody
    public String test04(@RequestBody UserVo userVo){
        System.out.println("test04方法执行了");
        System.out.println(userVo);
        return "你好";
    }
```

#### 返回json字符串

<div style='background-color:orange;'>示例3:</div>

先要导入JSON转换需要的包 

```xml
<dependency> 
    <groupId>com.fasterxml.jackson.core</groupId> 
    <artifactId>jackson-core</artifactId> 
    <version>2.9.8</version> 
</dependency> 
<dependency> 
    <groupId>com.fasterxml.jackson.core</groupId> 
    <artifactId>jackson-databind</artifactId> 
    <version>2.9.8</version> 
</dependency> 
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId> 
    <artifactId>jackson-annotations</artifactId> 
    <version>2.9.8</version> 
</dependency>
```

```java
将返回结果转成json字符串返回给浏览器
--------------------------------------
/**
     * 响应数据:
     *      响应json格式的数据给浏览器
     *      @ResponseBody + 对象
     *          可以直接将对象转成json字符串写回给浏览器
     * @param userVo
     * @return
     */
    @RequestMapping(value = "/test05",produces = "application/json;charset=utf-8")
    @ResponseBody
    public UserVo test05(@RequestBody UserVo userVo){
        System.out.println("test04方法执行了");
        System.out.println(userVo);
        userVo.setAge(21);
        userVo.setUsername("笑雯");
        return userVo;
    }
```

### 3. @RequestBody和@ResponseBody的区别

@RequestBody

```java
1.@RequestBody核心是接收客户端发送的json格式字符串，并封装到某个vo下(反序列化过程)；
2.get请求是没有请求体,而请求携带的json格式数据必须存放在请求体中携带给服务器
3.编写位置: 参数列表中参数的前面
作用:
  在post请求时,获取请求携带的json字符串,自动解析json字符串,并将解析到的内容封装到对应的java对象中(导入Jackson的jar包)
```

@ResponseBody

```java
@ResponseBody:
  作用1: 以流的形式写回字符串给浏览器
  作用2: 将对象转成json字符串,将转完后的字符串以流的形式写回给浏览器
    	需要设置返回数据的mime类型: produces = "application/json;charset=utf-8"
```

**@RequestBody和@ResponseBody都是用来干什么的？** 

​		@RequestBody : 使用在post请求中

​				1.获取请求体的参数(获取的是一个字符串)

​				2.获取请求携带的json格式字符串,并解析json格式的字符串,封装到对象中

​				3.使用在参数的前面

​		@ResponseBody: 生成响应字符串的

​				1.直接返回一个普通字符串

​				2.如果返回的是对象,则将对象转出json字符串并返回

​				3.使用在方法上或方法的返回值前面

## 九、RESTFul风格的路径

``` java
@Controller
public class Demo6Controller {

    /**
     * 请求路径中 {占位符} 接收请求路径中当前位置的值
     * @PathVariable: 解析请求路径中的值,并将值赋给形参变量
     */
    @PostMapping("/user/{username}/{age}")
    public String add(@PathVariable("username")String username ,
                      @PathVariable("age")Integer age){
        System.out.println("添加用户信息 "+username+" : "+age);
        return "success";
    }

    @PutMapping("/user/{id}")
    public String update(@PathVariable("id")Integer id){
        System.out.println("修改用户信息 " +id);
        return "success";
    }

    @DeleteMapping("/user/{id}")
    public String delete(@PathVariable("id") Integer id){
        System.out.println("删除用户信息 " +id);
        return "success";
    }

    @GetMapping("/user/{currentPage}/{pageSize}")
    public String findByPage(@PathVariable("currentPage")Integer currentPage,
                             @PathVariable("pageSize")Integer pageSize){
        System.out.println("分页查询用户信息 "+currentPage+" : "+pageSize);
        return "success";
    }
}
```

## 十、其他

### 1、静态资源映射

#### 解决方式1：

  在springmvc.xml文件中配置：

< mvc:resources mapping="匹配请求路径" location="真实路径">  

```xml
<!-- 设置静态资源不过滤 -->
	<mvc:resources mapping="/css/**" location="/css/" />  <!-- 样式 -->
	<mvc:resources mapping="/images/**" location="/images/" />  <!-- 图片 -->
	<mvc:resources mapping="/js/**" location="/js/" />  <!-- javascript -->
	<mvc:resources mapping="/html/**" location="/html/" />  <!-- html资源 -->
```

#### 解决方式2：

  在springmvc.xml文件中配置：

~~~xml
    <!-- 统一配置 ★ -->
	<!-- 代替以上所有的配置 -->
	<mvc:default-servlet-handler/>
~~~



### 2、异常处理

#### 方式1：基于HandlerExceptionResolver接口实现

```java
自定义异常处理器
 a）.编写一个类,实现 HandlerExceptionResolver 接口
 b）.重写 resolveException方法：完成异常处理
        1.跳转到公共的错误页面
        2.携带错误信息
        返回：ModelAndView
           addObject：  携带参数
           setViewName：指定页面跳转
 c.配置异常处理器（交给spring容器管理）
	<bean id="exceptionResolver" class="com.study.exception.MyHandlerExceptionResolver" />	 或者使用注解加入spring的ioc容器中；
```

实现代码如下：

##### 自定义异常处理类

```java
@Component
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
    /**
     * @param request : 请求对象
     * @param response : 响应对象
     * @param handler : 处理器对象(哪个处理器方法抛出的异常)
     * @param ex : 抛出的异常是什么
     * @return
     */
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response,Object handler, Exception ex) {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("msg",ex.getMessage());
        // 设置视图
        modelAndView.setViewName("forward:/pages/error.jsp");
        return modelAndView;
    }
}

```



#### 方式2：注解开发异常处理器（推荐）

* 使用注解实现异常分类管理
   名称： @ControllerAdvice
   类型： 类注解
   位置：异常处理器类上方
   作用：设置当前类为异常处理器类
   范例：

```java
@Component
@ControllerAdvice
public class ExceptionAdvice {
}  
```

* 使用注解实现异常分类管理
   名称： @ExceptionHandler
   类型： 方法注解
   位置：<font color='red'>异常处理器类中针对指定异常进行处理的方法上方</font>
   作用：设置指定异常的处理方式
   范例：
   说明：处理器方法可以设定多个

 ```java
@ExceptionHandler(Exception.class)
@ResponseBody
public String doOtherException(Exception ex){
    return "出错啦，请联系管理员！ ";
}  
 ```

- 流程如下：

1.以业务异常为例说明：

```java
//自定义异常继承RuntimeException，覆盖父类所有的构造方法
public class BusinessException extends RuntimeException {
}
```

2.定义分类异常管理器

~~~java
@Component
@ControllerAdvice
public class MyExceptionAdvice {

    @ExceptionHandler(BusinessException.class)
    public ModelAndView handlerException(BusinessException exception){
        ModelAndView mv = new ModelAndView();
        mv.setViewName("forward:/pages/error.jsp");
        mv.addObject("msg",exception.getMessage());
        return mv;
    }


    @ExceptionHandler(otherDefinerException.class)
    public ModelAndView handlerException(BusinessException exception){
       ModelAndView mv = new ModelAndView();
        mv.setViewName("forward:/pages/error.jsp");
        mv.addObject("msg",exception.getMessage());
        return mv;
   }

}
~~~

3.触发业务异常代码示例：

```java
@Controller
public class TestExceptionController {
    @GetMapping("/user")
    public String getUser(String name){
        //举例业务异常说明
        if(name.trim().length()<4 ){
            throw  new BusinessException("用户名长度必须在2-4位之间，请重新输入！");
        }
        return "hello";
    }
}
```

**总之，通过自定义异常将所有的异常现象进行分类管理，以统一的格式对外呈现异常消息**  



### 3、拦截器

#### 【1】拦截器介绍

```java
拦截器（interceptor）是springmvc提供的一种机制，可以对请求进行拦截或直接放行，可以在进入控制器方法前后对请求做出相应的处理.
作用:
	在处理器方法执行前后,进行拦截过滤
主要执行的拦截有:
	在处理器方法执行前执行拦截: preHandle
    	      在处理方法执行后执行拦截: postHandle
            在视图解析器解析完毕后执行拦截: afterCompletion	
```

![1577627711990](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208212356074.png)

#### 【2】拦截器VS过滤器

拦截器与过滤器区别？

-  归属不同： Filter属于Servlet技术， Interceptor属于SpringMVC技术
-  拦截内容不同： Filter可以对所有访问进行增强， Interceptor仅针对SpringMVC的访问进行增强  
-  过滤器在Servlet执行前和执行后进行过滤，而 拦截器在servlet执行后,处理器执行前或后进行执行.

![image-20200427164512745](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208212356069.png)

#### 【3】自定义拦截器

​	**1.编写一个类实现HandlerInterceptor**

```java
/**
 * 编写一个拦截器
 * 1.编写一个类,实现 HandlerInterceptor 接口
 * 2.重写后抽象方法
 * 3.配置拦截器 springmvc.xml
 */
public class MyIntercept implements HandlerInterceptor {
    @Override
    /**
     * 处理器指定前执行拦截
     * 返回值为boolean
     *      如果返回ture则放行,执行处理器方法
     *      如果返回false则不放行,处理器方法不会执行
     */
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle在处理器执行前拦截了..");
        return true;
    }

    @Override
    /**
     * 处理器执行后拦截
     */
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle在处理器执行后拦截了..");
    }

    @Override
    /**
     * 视图解析器执行后拦截
     */
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion在视图解析器执行后拦截了..");
    }
}

```

​	**2. 配置拦截器**

```xml
// 在springmvc.xml文件中配置拦截器需要拦截的路径
<!-- 配置拦截器 -->
<mvc:interceptors>
<!-- 配置一个拦截器 -->
<mvc:interceptor>
    <!--设置拦截器的拦截路径，支持*通配-->
    <!--/**         表示拦截所有映射-->
    <!--/*          表示拦截所有/开头的映射-->
    <!--/user/*     表示拦截所有/user/开头的映射-->
    <!--/user/add*  表示拦截所有/user/开头，且具体映射名称以add开头的映射-->
    <!--/user/*All  表示拦截所有/user/开头，且具体映射名称以All结尾的映射-->
    <mvc:mapping path="/**"/>
    <!-- 排除指定路径不需要拦截 -->
    <mvc:exclude-mapping path="/demo"></mvc:exclude-mapping>
    <mvc:exclude-mapping path="/hello"></mvc:exclude-mapping>
    <!-- 使用指定的拦截器类处理 -->
    <bean class="com.itheima.intercept.MyIntercept"></bean>
</mvc:interceptor>
</mvc:interceptors> 
```

**3、多拦截器配置**

```java
<!-- 配置拦截器 -->
    <!-- 配置拦截器组 -->
    <mvc:interceptors>
        <!-- 配置一个拦截器 -->
        <mvc:interceptor>
            <!-- 配置拦截器拦截的路径 第一*: 表示所有的包 第二*:表示所有的资源 -->
            <mvc:mapping path="/**"/>
            <!-- 排除指定路径不需要拦截 -->
            <mvc:exclude-mapping path="/demo"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/hello"></mvc:exclude-mapping>
            <!-- 使用指定的拦截器类处理 -->
            <bean class="com.itheima.intercept.MyIntercept"></bean>
        </mvc:interceptor>

        <mvc:interceptor>
            <!-- 配置拦截器拦截的路径 第一*: 表示所有的包 第二*:表示所有的资源 -->
            <mvc:mapping path="/hello"/>
            <!-- 使用指定的拦截器类处理 -->
            <bean class="com.itheima.intercept.MyIntercept2"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

![image-20200427171422781](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208212356075.png)



# SpringBoot





# Git



# Docker

