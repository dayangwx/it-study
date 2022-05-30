# Spring

---

> 简单来说，Spring是一个分层的JavaSE/EE full-stack(一站式) 轻量级开源框架。Spring 的理念：不去重新发明轮子。其核心是控制反转（IOC）和面向切面（AOP）

不要重复造轮子

怎么避免重复造轮子，造的是什么轮子

## 1、控制反转(IOC) XML方式

> 这个过程就叫控制反转︰
> 控制:谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用Spring后，对象是由Spring来创建的.
> 反转∶程序本身不创建对象，而变成被动的接收对象．依赖注入∶就是利用set方法来进行注入的.
> IOC是一种编程思想，由主动的编程变成被动的接收．
> 可以通过newClassPathXmlApplicationContext去浏览一下底层源码﹒
> OK，到了现在，**我们彻底不用再程序中去改动了，要实现不同的操作，只需要在xml配置文件中进行修改**，所谓的loC,一句话搞定:
>
> ==对象由Spring来创建，管理，装配!==

```java
<depedency>  
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.18</version>
</depedency>  
这个依赖会导入spring很多的jar，如：core，context，web，beans，aop，expressions
```

### 1-1.IOC创建对象

##### 方式一：无参构造

> 1：类中要提供无参构造，以及set方法
>
> 2：xml内容如下：
>
>   可以看出，使用的是property

```xml
<bean id="dataDaoSqlserve" class="tech.xiu.dao.DataDaoSqlserve"/>
<bean id="dataServiceImpl" class="tech.xiu.service.impl.DataServiceImpl">
    <property name="dataDao" ref="dataDaoSqlserve"></property>
</bean>
```

##### 方式二：有参构造

> 1：类中要提供有参构造，**不需要set方法,也不需要get方法，只需要有参构造**
>
> 2：xml内容如下：

```xml
方式一：通过有参构造中参数的索引进行赋值
<bean id="user" class="tech.xiu.pojo.User">
    <constructor-arg index="0" value="liuwx"></constructor-arg>
     <constructor-arg index="1" value="swimming"></constructor-arg>
</bean>
方式二：通过有参构造中参数的名称
<bean id="user" class="tech.xiu.pojo.User">
    <constructor-arg name="username" value="张睿桐"></constructor-arg>
    <constructor-arg name="hobby" value="swimming"></constructor-arg>
</bean>
方式三：通过有参构造中参数的类型 
<bean id="user" class="tech.xiu.pojo.User">
    <constructor-arg type="java.lang.String" value="陈若愚"></constructor-arg>
    <constructor-arg type="java.lang.String" value="sing 孤勇者"></constructor-arg>
</bean>
```

**在spring容器初始化的时候所有的bean就会被创建好！所以每次获取的bean都是同一个（当然你可以设置成多例的）！**

##### 配置说明

###### bean属性

> id：获取对象时用的
>
> class：包路径+类名，就是为了找到这个类
>
> name：就是别名，获取这个对象的时候可以用name中的值，里面的值可以用,; 空格等进行分割，也就是说可以有多个别名
>
> scope：单例，多例

```xml
 <bean id="user" class="tech.xiu.pojo.User" name="u1" scope="prototype">
     
</bean>
```

###### alias

> 别名，给bean设置别名。

```xml
  <alias name="user" alias="ua1"></alias>
```

###### import

> 导入，相当于是整合多个xml为一个。

```xml
<import resource="beans.xml"></import>
<import resource="beans2.xml"></import>
```

### 1-2.依赖注入(DI)

> #### 依赖注入：Dependency Injection
>
> 依赖：bean对象的创建依赖于spring容器
>
> 注入：bean对象的所有属性，由spring容器来注入。

#### 1-2-1.构造器方式

> 就是有参构造，只需要提供有参构造，xml中就可以创建对象并注入数据！
>
> 详见：1-1-1的方法二，此处不再赘述。

#### 1-2-2.Set方式注入(重点)

> 利用setter方法注入，
>
> 类中提供无参构造，以及setter方法
>
> 基本使用详见：1-1-1的方法一，此处不再赘述。
>
> 对于复杂对象该如何使用此种方法创建对象并对属性赋值，详见下文xml：
>
> 包含：基本数据类型，对象，array，list，set，map，properties，null

```xml
<bean id="student" class="tech.xiu.bean.Student">
        <property name="name" value="刘文秀"></property>
        <property name="address" ref="address"></property>
        <property name="books" >
            <array value-type="java.lang.String">
                <value>白鹿原</value>
                <value>万历十五年</value>
                <value>金瓶梅</value>
                <value>三体</value>
            </array>
        </property>
        <property name="hobbys">
            <list value-type="java.lang.String">
                <value>tennis</value>
                <value>basketball</value>
                <value>table tennis</value>
            </list>
        </property>
        <property name="games">
            <set value-type="java.lang.String">
                <value>英雄联盟</value>
                <value>王者荣耀</value>
                <value>王者荣耀</value> <!--  会自动去重-->
            </set>
        </property>
        <property name="scores">
            <map value-type="java.lang.String">
                <entry key="英语">
                    <value>90</value>
                </entry>
                <entry key="数学">
                    <value>89</value>
                </entry>
                <entry key="历史" value="100"></entry>
            </map>
        </property>
        <property name="info" >
            <props>
                <prop key="url">jdbc:mysql://localhost:3306</prop>
                <prop key="driver">mysql</prop>
                <prop key="username">root</prop>
                <prop key="password">12345</prop>
            </props>
        </property>
        <property name="wife" >
            <null></null>
        </property>
    </bean>

    <bean id="address" class="tech.xiu.bean.Address">
        <property name="address" value="成都市光明城市"></property>
    </bean>
```

#### 1-2-3.扩展注入方式

> Spring 支持[带有命名空间](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#xsd-schemas)的可扩展配置格式，它基于 XML 模式定义。
>
> 1：p-namespace
>
> 2：c-namespce

##### 1-2-3-1.p-namespace

> p就是property的意思。
>
> 它的作用就是简化<property>
>
> 它是依赖于Set注入的，所以要提供setter方法和无参构造。

```xml
xmlns:p="http://www.springframework.org/schema/p"

<bean id="user" class="tech.xiu.bean.User" p:username="张睿桐" p:age="23"></bean>
```

##### 1-2-3-2.c-namespace

> c就constructor的意思。
>
> 它的作用就是简化<constructor-arg>
>
> 它是依赖于有参构造的，所以要提供有参构造。

```xml
xmlns:d="http://www.springframework.org/schema/c"

 <bean id="user2" class="tech.xiu.bean.User" d:age="28" d:username="刘文秀"></bean>
```

### 1-3.bean的作用域

> 
>

|                            scope                             |               description                |
| :----------------------------------------------------------: | :--------------------------------------: |
| [singleton](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-singleton) |         单例模式，也是默认的模式         |
| [prototype](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype) | 原型模式，其实就是多例。一般用于多线程。 |
| [request](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-request) |            web中用到，待学习             |
| [session](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-session) |            web中用到，待学习             |
| [application](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-application) |            web中用到，待学习             |
| [websocket](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#websocket-stomp-websocket-scope) |            web中用到，待学习             |

### 1-4.Bean的自动装配

> 自动装配是Spring满足bean依赖的一种方式。
>
> Spring会在上下文中自动寻找，并自动给bean装配属性

Spring中的三种装配方式：

- 在xml中显示的配置
- 在java中显示配置
- 隐式的自动装配【重要】

隐式自动装配方式一：

> byName：会自动在容器上下文中寻找，和自己对象set方法后面的值对应的beanid；
>
> 需要beanid唯一，否则就会报错！

```xml
<bean id="cat" class="tech.xiu.bean.Cat"></bean>
<bean id="dog" class="tech.xiu.bean.Dog"></bean>

<bean id="person" class="tech.xiu.bean.Person" autowire="byName">
    <property name="name" value="刘文秀"></property>
</bean>

```

隐式自动装配方式二：

> byType：会自动在容器上下文中寻找，和自己对象属性类型相同的bean；
>
> 需要bean类型唯一，否则就会报错！
>
> 这种情况下是可以省略beanid的

```xml
<bean class="tech.xiu.bean.Cat"></bean>
<bean class="tech.xiu.bean.Dog"></bean>

<bean id="person" class="tech.xiu.bean.Person" autowire="byType">
    <property name="name" value="刘文秀"></property>
</bean>
```



## 2、控制反转(IOC) 注解方式

### 2-1.自动装配

> 通过注解的方式给属性设置值。

两种方式：

- @Autowired
- @Resouce

#### 2-1-1.@Autowired

> 可以放到set方法上，也可以直接放在属性上。
>
> **它是通过byType去找到被装配目标的。**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 开启注解模式，也就是说在代码中的spring注解可以生效了 -->
    <context:annotation-config/>

    <bean id="cat" class="tech.xiu.bean.Cat"></bean>
    <bean id="cat22" class="tech.xiu.bean.Cat"></bean>
    <bean id="dog" class="tech.xiu.bean.Dog"></bean>
    <bean id="dog22" class="tech.xiu.bean.Dog"></bean>

    <bean id="person" class="tech.xiu.bean.Person">
        <property name="name" value="刘文秀"></property>
    </bean>
</beans>
```

```java
@Data
public class Person {

    private String name;

    @Autowired
    private Dog dog;

    @Autowired
    private Cat cat;
}

```

@Qualifier：

​	如果有多个，可以指定某个。一般与@Autowired配合使用。

```java
// xml与上面的一致
@Data
public class Person {

    private String name;

    @Autowired
    @Qualifier(value = "dog22")
    private Dog dog;

    @Autowired
    @Qualifier(value = "cat22")
    private Cat cat;
}
```

#### 2-1-2.@Resource

> 也是用与自动装配。
>
> 它是java的注解。
>
> **它默认是通过byName去找到被装配目标的，如果找不到那就通过buType去找，如果都找不到那就报错**
>
> 注意：
>
>   1：byName和byType找不到，报错
>
>   2：byType找到多个，报错
>
>   3：byName找到多个，这种情况xml本身不允许存在。

```java
// xml与2-1-1中相同
@Data
public class Person {

    private String name;

//    @Autowired
//    @Qualifier(value = "dog22")
    @Resource
    private Dog dog;

//    @Autowired
//    @Qualifier(value = "cat22")
    @Resource
    private Cat cat;
}
```

**小结：**

​	**@Autowired和@Resource都是自动装配的方式。**

​	**@Autowired和@Resource都是不需要set方法的**

​	**@Autowired是spring的注解，通过byType找到被装配对象。**

​	**@Resource是java的注解，默认通过byName找到被装配的对象，如果找不到，再通过byType的方式找到装配对象。**

### 2-2.注解方式创建对象

> 还是需要beans.xml，只是内容很少了。
>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 开启注解模式，也就是说在代码中的spring注解可以生效了 -->
    <context:annotation-config/>
    <context:component-scan base-package="tech.xiu"></context:component-scan>

</beans>
```

```java
@Data
@Component  // 相当于<bean id="user" class=""/>
public class User {

    @Value("liuwx")  // 相当于 <property name="name" value="liuwx"/>
    private String name;
}
```

**常用注解：**

- @Component

- @Repository

- @Service (写在impl上)

- @Controller

  以上四个注解作用都是一样的，将对象交给Spring容器托管。功能相当于<bean id="" class=""></bean>

- @Value：给属性赋值，可以写在属性上，也可以写在set方法上。



### 2-3.纯注解方式(重要)

> 完全使用注解，没有beans.xml

```java
@Configuration      //  该注解表明这个类是配置类，相当于说这个类是个xml了。
@ComponentScan("tech.xiu.pojo") // 表明扫描的包路径，
public class AnnoConfig {

    @Bean(name = "user") // 相当于 <bean id="user" class=""/>
    public User getUser() {
        return new User();
    }
}

```

```java
@Data
@Component
public class User {

    @Value("liuwx")
    private String name;
}
```



**常用注解：**

- @Configuration

- @ComponentScan()

- @Import

- @Bean

- @Scope

- @Component

- @Value

  

## 3、面向切面编程(AOP)

### 3-1.代理模式

> [代理模式](https://link.segmentfault.com/?enc=NefWwXp01fWCuvxA1GpKtQ%3D%3D.a3qT9T5hzzM%2FxoEVzFDtG77qf74SY0qO0hPHl58hCpPjP2h5jv9F7bAFRbfUMumWSOnwFeKuxBAVDa523AHC2tqKu9DnNu6ViVlewV4NQjY%3D)是一种设计模式，提供了对目标对象额外的访问方式，即通过代理对象访问目标对象，这样可以在不修改原目标对象的前提下，提供额外的功能操作，扩展目标对象的功能。
>
> 简言之，代理模式就是设置一个中间代理来控制访问原目标对象，以达到增强原对象的功能和简化访问方式。
>
> 中间代理，增强对象功能或者简化访问方式。

#### 3-1-1.静态代理

> 

![image-20220529164356032](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164356032.png)

```java

/**
 * 租房接口
 * 中介和房东都要做的事情
 */
public interface Rent {

    void rent();
}


/**
 * 房东要出租房子。
 * 属于租房的范畴
 * 租房范畴：
 *   出租房子，
 *   租房子
 */
public class Host implements Rent{

    @Override
    public void rent() {
        System.out.println("房东出租房子！");
    }
}


/**
 * 中介
 *  中介可以有一些除了租房意外的附属的属性，比如说：看房，签合同，收钱、多家房源进行比对，有针对性的推荐等等
 */
public class Proxy implements Rent{

    private Host host;

    public Proxy(Host host) {
        this.host = host;
    }

    public Proxy() {
    }

    @Override
    public void rent() {
        host.rent();
        this.seeHouse();
        this.signContract();
    }

    private void seeHouse() {
        System.out.println("中介看房！");
    }

    private void signContract() {
        System.out.println("和中介签租房合同！");
    }
}


public class Guest {

    public static void main(String[] args) {
        // 直接找房东租房子。
//        Host host = new Host();
//        host.rent();

        /**
         * 代理和真实的host都做了同一件事情：租房。
         * 所以他们两都需要implements 租房
         */
        Host host = new Host();
        Proxy proxy = new Proxy(host);
        proxy.rent();
    }
}

```

**角色分析：**

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色。同时一般会有一些附属操作
- 客户   ：调用代理对象。

**代理模式的好处：**

- **可以在不修改目标对象的前提下扩展目标对象的功能。**
- 公共业务交给代理角色，实现了业务的分工。
- 公共业务发生扩展的时候，方便集中管理。

**代理模式的缺点：**

- 一个真实角色就会产生一个代理角色；代码量激增。

#### 3-1-2.动态代理

> 解决了静态代理的缺点，不需要一个真实角色就产生一个代理角色了。
>
> 使用一个类动态生成代理对象，
>
> 这样就可以传什么类型就生成什么类型的代理对象。

**实现动态代理的方式：**

- **基于接口的方式：其实就是基于JDK，使用jdk提供的Proxy、InvocationHandler实现**
- 基于类      ：cglib
- java字节码实现：javassist

##### 3-1-2-1:基于接口的方式

> 其实就是基于JDK，使用jdk提供的Proxy、InvocationHandler实现
>
> 源码：https://gitee.com/liuwenxiu/springStudy

```java

public class ProxyInvocationHandler implements InvocationHandler {

    private Object target;

    public void setObject(Object target) {
        this.target = target;
    }

    //    Foo f = (Foo) Proxy.newProxyInstance(Foo.class.getClassLoader(),
//            new Class<?>[] { Foo.class },
//            handler);
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        Object invoke = method.invoke(target, args);
        return invoke;
    }

    private void log(String msg) {
        System.out.println("调用了"+msg+"方法！");
    }
}
```

##### 3-1-2-2：基于类的方式

> 可自行学习

##### 3-1-2-3：基于java字节码

> 可自行学习

### 3-2.AOP

### 3-2-1.AOP概述

> AOP (Aspect Oriented Programming)意为:面向切面编程，通过预编译方式和运行期动态代埋头现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
>
> 说白了一句话：==**就是在不改变原有代码的前提下，去增加新功能 ！**==



<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164431862.png" alt="image-20220529164431862" style="zoom:33%;" />

### 3-2-2.AOP在Spring中的作用

> 作用：提供声明式事务;允许用户自定义切面
>
> 说白了一句话：**就是在不改变原有代码的前提下，去增加新功能 ！**
>
> 
>
> 名词理解：
>
> - 横切关注点:跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点如日志，安全，缓存，事务等等....
>
> - 切面(ASPECT)︰横切关注点被模块化的特殊对象。即，它是一个类。
> - 通知(Advice) :切面必须要完成的工作。即，它是类中的一个方法。
> - 目标（Target):被通知对象。
> - 代理(Proxy) ︰向目标对象应用通知之后创建的对象。
> - 切入点(PointCut):切面通知执行的“地点"的定义。
> - 连接点(JointPoint) :与切入点匹配的执行点。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164459799.png" alt="image-20220529164459799" style="zoom:30%;" />



**aop支持的五种通知：**

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164539246.png" alt="image-20220529164539246" style="zoom:30%;" />



#### 3-2-3.使用Spring实现AOP

需要导入的依赖：

```xml
注意：
  JDK1.6 —— aspectJ1.6
  JDK1.7 —— aspectJ1.7.3+ 

<dependency>
     <groupId>aspectj</groupId>
     <artifactId>aspectjweaver</artifactId>
     <version>1.9.9</version>
</dependency>
```

##### 3-2-3-1.使用spring的api接口

```xml
    切入点表达式的写法：
        关键字：execution(表达式)
        表达式：
            访问修饰符  返回值  包名.包名.包名...类名.方法名(参数列表)
        标准的表达式写法：
            public void com.itheima.service.impl.AccountServiceImpl.saveAccount()
        访问修饰符可以省略
            void com.itheima.service.impl.AccountServiceImpl.saveAccount()
        返回值可以使用通配符，表示任意返回值
            * com.itheima.service.impl.AccountServiceImpl.saveAccount()
        包名可以使用通配符，表示任意包。但是有几级包，就需要写几个*.
            * *.*.*.*.AccountServiceImpl.saveAccount())
        包名可以使用..表示当前包及其子包
            * *..AccountServiceImpl.saveAccount()
        类名和方法名都可以使用*来实现通配
            * *..*.*()
        参数列表：
            可以直接写数据类型：
                基本类型直接写名称           int
                引用类型写包名.类名的方式   java.lang.String
            可以使用通配符表示任意类型，但是必须有参数
            可以使用..表示有无参数均可，有参数可以是任意类型
        全通配写法：
            * *..*.*(..)：这样的*(..)就代表这个类里的所有的方法都是被增强的

        实际开发中切入点表达式的通常写法：
            切到业务层实现类下的所有方法
                * com.itheima.service.impl.*.*(..)
```



```java
public class AfterLog implements AfterReturningAdvice {

    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了："+method.getClass()+"的方法:"+method.getName()+"。返回结果："+returnValue);
    }
}

public class Log implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println("前置通知：执行了"+method.getClass()+"类的"+method.getName()+"的方法！");
    }
}
```



```xml
<bean id="userService" class="tech.xiu.service.UserServiceImpl"/>
<bean id="afterLog" class="tech.xiu.log.AfterLog"></bean>
<bean id="log" class="tech.xiu.log.Log"></bean>
<aop:config>
    <aop:pointcut id="userServicePointcut" expression="execution(* tech.xiu.service.UserServiceImpl.*(..))"/>

    <aop:advisor advice-ref="afterLog" pointcut-ref="userServicePointcut"/>
    <aop:advisor advice-ref="log" pointcut-ref="userServicePointcut"/>
</aop:config>
```

##### 3-2-3-2.自定义实现aop

> 主要是切面定义。
>
> 自己写个类就行

```java
public class DiyAspect {

    public void before() {
        System.out.println("方法执行前。。。");
    }

    public void after() {
        System.out.println("方法执行后。。。");
    }
    
    // 环绕通知需要传入这个参数
    public void around(ProceedingJoinPoint proceedingJoinPoint) {
        try{
            System.out.println("环绕通知里的前置通知。。。");
            // proceedingJoinPoint.getArgs();
            proceedingJoinPoint.proceed();
            System.out.println("环绕通知里的后置通知。。。。");
        }catch (Throwable e) {
            System.out.println("异常通知。。。");
        }finally {
            System.out.println("最终通知。。。。。");
        }
    }
}
```

```xml
<bean id="diyAspect" class="tech.xiu.diy.DiyAspect"/>
<aop:config>
    <aop:pointcut id="userServicePointcut" expression="execution(* tech.xiu.service.UserServiceImpl.*(..))"/>

    <aop:aspect ref="diyAspect">
        <aop:around method="around" pointcut-ref="userServicePointcut"></aop:around>
    </aop:aspect>
</aop:config>
```

##### 3-2-3-3.纯注解方式实现【重要】

> 推荐使用这种方式。
>
> 步骤：
>
>  1：导入jar包
>
>  2：编写切面
>
>  3：测试

-  导入jar包

  ```xml
  <!-- 支持切入点表达式等等,包含aspectjrt：简单理解，支持aop相关注解等等 -->
  <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.9</version>
  </dependency>
  <!-- 导入这个依赖，会导入spring相关的context，aop等 -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.18</version>
  </dependency>
  <!-- spring整合junit，主要使测试用 -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.3.8</version>
  </dependency>
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.22</version>
  </dependency>
  <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
  </dependency>
  ```

-  编写切面

  ```java
  package tech.xiu;
  
  import org.aspectj.lang.ProceedingJoinPoint;
  import org.aspectj.lang.annotation.*;
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.context.annotation.EnableAspectJAutoProxy;
  
  @Configuration   // 说明它是个配置类，相当于beans.xml
  @Aspect          // 说明这是个切面
  @ComponentScan("tech.xiu")  // 扫描该路径下的类的注解
  @EnableAspectJAutoProxy     // 开启AOP
  public class AspectConfig {
  
      @Pointcut(value = "execution(* tech.xiu.UserServiceImpl.*(..))")
      public void pointcut1(){}
  
      @Before("pointcut1()")
      public void before() {
          System.out.println("前置通知。。。。");
      }
  
      @After("pointcut1()")
      public void after() {
          System.out.println("后置通知。。。。");
      }
  
      @AfterThrowing("pointcut1()")
      public void throwsErr() {
          System.out.println("异常通知。。。。");
      }
  
      @Around("pointcut1()")
      public void around(ProceedingJoinPoint pjp) {
  
          try{
              System.out.println("环绕通知前。。。");
              pjp.proceed();
              System.out.println("环绕通知后。。。");
          }catch (Throwable t) {
              System.out.println("异常通知。。。（环绕通知里）");
          }finally {
              System.out.println("最终通知。。。（环绕通知里）");
          }
      }
      
       @AfterReturning(value="pointcut1()",
              returning="result")
      public void afterReturning(JoinPoint joinPoint, Object result){
          String methodName = joinPoint.getSignature().getName();
          System.out.println("The method " + methodName + " ends with " + result);
      }
  
  }
  ```

-  测试

  ```java
  import org.junit.Test;
  import org.junit.runner.RunWith;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.test.context.ContextConfiguration;
  import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
  import tech.xiu.AspectConfig;
  import tech.xiu.UserService;
  
  @RunWith(SpringJUnit4ClassRunner.class)                // junit整合spring，固定写法
  @ContextConfiguration(classes = {AspectConfig.class})  // 加载AspectConfig配置
  public class MyTest {
  
      @Autowired
      private UserService userService;
  
      @Test
      public void test1() {
          userService.addUser();
      }
  }
  
  
  结果：
      环绕通知前。。。
      前置通知。。。。
      添加一个user
      后置通知。。。。
      环绕通知后。。。
      最终通知。。。（环绕通知里）
      
     特殊点说明：
       在AfterReturning中，是可以获得原始方法的返回数据的。
      “执行的方法是： isSucceed ，返回的结果是：是的，成功了！！！”
  ```

  

## 4、整合Mybatis

### 4-1.Mybatis回忆

> 步骤：
>
> ​	1：导包
>
> ​	2：编写配置文件及类
>
> ​	3：测试

- 导包

  ```java
  - junit  测试用
  - mysql
  - mybatis
      	<dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.13.2</version>
              <scope>test</scope>
          </dependency>
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>5.1.46</version>
          </dependency>
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis</artifactId>
              <version>3.5.6</version>
          </dependency>
  ```

- 编写配置文件及类

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/myemployees?characterEncoding=UTF-8"/>
                  <property name="username" value="root"/>
                  <property name="password" value="root"/>
              </dataSource>
          </environment>
      </environments>
  
      <mappers>
  <!--        <mapper class="tech.xiu.dao.EmpMapper"/>-->
          <mapper resource="tech/xiu/dao/EmpMapper.xml"></mapper>
      </mappers>
  </configuration>
  ```

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="tech.xiu.dao.EmpMapper" >
  
      <select id="selectEmps"  resultType="tech.xiu.pojo.Emp">
          select *
          from emp order by empno desc
      </select>
  
  </mapper>
  ```

- 测试

  ```java
  @Test
  public void test() throws IOException {
      InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
      SqlSessionFactory build = new SqlSessionFactoryBuilder().build(inputStream);
      SqlSession sqlSession = build.openSession();
      EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
      List<Emp> emps = mapper.selectEmps();
      for (Emp emp : emps) {
          System.out.println(emp);
      }
  }
  ```

  注意：

  maven静态资源过滤问题：就是mapper.xml文件没有编译到target目录下。

  会报：bind 绑定异常。

  ```xml
  <build>
      <resources>
          <resource>
              <directory>src/main/java</directory>
              <includes>
                  <include>**/*.properties</include>
                  <include>**/*.xml</include>
              </includes>
              <filtering>true</filtering>
          </resource>
      </resources>
  </build>
  ```

  

### 4-2、Mybatis整合Spring

> 步骤：
>
>  1：导包 
>
>  2：编写配置文件及代码
>
>  3：测试

#### 4-2-1.方式一：xml方式 SqlSessionFactory

> 代码：详见代码：https://gitee.com/liuwenxiu/springStudy.git   spring-11-mybatis-spring

- 导包

  ```xml
  1:mybatis相关的包
   mysql
   mybatis
  2：spring
   spring-webmvc
   aspectj-weaver
   spring-jdbc 小心版本冲突，可以使用exclude去掉无关jar
  3：测试用
   junit
   spring-test
  4：其他
   Lombok
  ```

- 编写配置文件及代码

  ```xml
  Service
  ServiceImpl
  Mapper.java
  Mapper.xml
  ```

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd">
  
      <!-- 导入数据库连接信息 -->
      <context:property-placeholder location="classpath*:jdbc.properties"></context:property-placeholder>
  
      <!-- 数据源：DataSource;使用Spring的数据源替换Mybatis的配置 c3p，dbcp druid spring-->
      <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
          <property name="driverClassName" value="${jdbc.driver}"></property>
          <property name="url" value="${jdbc.url}"></property>
          <property name="username" value="${jdbc.username}"></property>
          <property name="password" value="${jdbc.password}"></property>
      </bean>
  
      <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
          <property name="dataSource" ref="dataSource" />
          <!-- 绑定MyBatis配置文件 -->
          <property name="configLocation" value="classpath:mybatis-config.xml"/>
          <!-- 注册Mapper文件 -->
          <property name="mapperLocations" value="tech/xiu/mapper/*.xml"/>
      </bean>
  
  
      <bean id="empMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
          <property name="mapperInterface" value="tech.xiu.mapper.EmpMapper" />
          <property name="sqlSessionFactory" ref="sqlSessionFactory" />
      </bean>
  
      <bean id="empService" class="tech.xiu.service.impl.EmpServiceImpl">
          <property name="empMapper" ref="empMapper"></property>
      </bean>
  </beans>
  
  ```

  ```properties
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://127.0.0.1:3306/myemployees?useUnicode=true&characterEncoding=utf8&autoReconnect=true&failOverReadOnly=false
  jdbc.username=root
  jdbc.password=root
  ```

#### 4-2-2.xml方式 使用SqlSession

> 在 MyBatis 中，你可以使用 `SqlSessionFactory` 来创建 `SqlSession`。 一旦你获得一个 session 之后，你可以使用它来执行映射了的语句，提交或回滚连接，最后，当不再需要它的时候，你可以关闭 session。 使用 MyBatis-Spring 之后，你不再需要直接使用 `SqlSessionFactory` 了，因为你的 bean 可以被注入一个线程安全的 `SqlSession`，它能基于 Spring 的事务配置来自动提交、回滚、关闭 session。
>
> 详见代码：https://gitee.com/liuwenxiu/springStudy.git   spring-11-mybatis-spring2

- 导包

  ```xml
  与上面相同，
  不过要注意：静态资源过滤问题
  ```

- 编写配置文件及类

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd">
  
      <context:property-placeholder location="classpath*:jdbc.properties"></context:property-placeholder>
  
      <!-- 数据源：DataSource;使用Spring的数据源替换Mybatis的配置 c3p，dbcp druid spring-->
      <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
          <property name="driverClassName" value="${jdbc.driver}"></property>
          <property name="url" value="${jdbc.url}"></property>
          <property name="username" value="${jdbc.username}"></property>
          <property name="password" value="${jdbc.password}"></property>
      </bean>
  
      <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
          <property name="dataSource" ref="dataSource" />
          <!-- 绑定MyBatis配置文件 -->
          <property name="configLocation" value="classpath:mybatis-config.xml"></property>
          <!-- 注册Mapper文件 -->
          <property name="mapperLocations" value="tech/xiu/dao/*.xml"></property>
      </bean>
  
      <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
          <constructor-arg index="0" ref="sqlSessionFactory" />
      </bean>
  
      <bean id="empService" class="tech.xiu.service.impl.EmpServiceImpl">
          <property name="sqlSession" ref="sqlSession"></property>
      </bean>
  </beans>
  
  ```

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
  <typeAliases>
      <package name="tech.xiu.pojo"/>
  </typeAliases>
  </configuration>
  ```

  ```java
  类似（mybatis-spring官网摘抄）：
  public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao {
    public User getUser(String userId) {
      return getSqlSession().selectOne("org.mybatis.spring.sample.mapper.UserMapper.getUser", userId);
    }
  }
  ```

**classpath 和 classpath\*的 区别：**

​	**classpath**：**只会到你指定的class路径中查找找文件**; 
​	**classpath\***：**不仅包含class路径，还包括jar文件中(class路径)进行查找.**

#### 4-2-3.注解方式 使用SqlSession

> 使用纯注解+sqlSession的方式实现整合mybatis

- 导包

  ```xml
  mybatis
  mysql
  spring-webmvc
  spring-jdbc
  mybatis-spring
  aspectj-weaver
  junit
  spring-test
  静态资源过滤
  ```

- 编写配置类及业务代码

- 测试

## 5、事务

### 5-1.事务回顾

> 事务：
>
>  就是要么全成功，要么全失败。

**ACID**

- 原子性
- 一致性
- 隔离性
- 持久性

### 5-2.Spring中的事务

> Spring中有两种：
>
> 声明式事务
>
> 编程式事务

#### 5-2-1.声明式事务

```java
package tech.xiu.config;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.transaction.TransactionFactory;
import org.apache.ibatis.transaction.jdbc.JdbcTransactionFactory;
import org.apache.ibatis.transaction.managed.ManagedTransactionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.transaction.SpringManagedTransactionFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.stereotype.Component;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@Configuration
@Component
@ComponentScan("tech.xiu")
@EnableTransactionManagement  // 启注解事务管理，等同于xml配置方式的 <tx:annotation-driven />
public class SpringMybatisConfig {

    private String driverClassName = "com.mysql.jdbc.Driver";
    private String url = "jdbc:mysql://127.0.0.1:3306/myemployees?useUnicode=true&characterEncoding=utf8&autoReconnect=true&failOverReadOnly=false";
    private String username = "root";
    private String password = "root";

    @Bean
    public DriverManagerDataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }


    @Bean
    public PlatformTransactionManager txManager() {
        return new DataSourceTransactionManager(dataSource());
    }

    @Bean
    public SqlSessionFactory sqlSessionFactory() throws Exception {
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource());
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        factoryBean.setConfigLocation(resolver.getResource("classpath:mybatis-config.xml"));
        factoryBean.setMapperLocations(resolver.getResource("classpath:tech/xiu/mapper/EmployeeMapper.xml"));
        return factoryBean.getObject();

    }

    @Bean
    public SqlSessionTemplate sqlSession() throws Exception {
        return new SqlSessionTemplate(sqlSessionFactory());
    }
}
```

然后在适合的位置使用@Transaction

#### 5-2-2.编程式事务

> 就是需要改变原来的代码，在代码中提交、回滚什么的。
>
> 通常不用。











