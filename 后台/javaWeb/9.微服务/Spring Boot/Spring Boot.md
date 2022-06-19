# Spring Boot

---

## 1、Spring Boot简介

> Spring Boot 是所有基于 Spring Framework 5.0 开发的项目的起点。Spring Boot 的设计是为了让你尽可能快的跑起来 Spring 应用程序并且尽可能减少你的配置文件。
>
> 
>
> **设计目的：** 用来简化新 Spring 应用的初始搭建以及开发过程。
>
> 
>
> 用大佬的话来理解，就是 spring boot 其实不是什么新的框架，它默认配置了很多框架的使用方式，就像 maven 整合了所有的 jar 包，spring boot 整合了所有的框架，总结一下及几点：
>
> （1）为所有 Spring 开发提供一个更快更广泛的入门体验。
>
> （2）零配置。无冗余代码生成和XML 强制配置，遵循“约定大于配置” 。
>
> （3）集成了大量常用的第三方库的配置， Spring Boot 应用为这些第三方库提供了几乎可以零配置的开箱即用的能力。
>
> （4）提供一系列大型项目常用的非功能性特征，如嵌入式服务器、安全性、度量、运行状况检查、外部化配置等。
>
> （5）Spring Boot 不是Spring 的替代者，Spring 框架是通过 IOC 机制来管理 Bean 的。Spring Boot 依赖 Spring  框架来管理对象的依赖。Spring Boot 并不是Spring 的精简版本，而是为使用 Spring 做好各种产品级准备

### 1-1.微服务

> **它是一种架构风格。**
>
> 要求我们的开发应用是由多个小应用组合而成。
>
> 可以通过http、rpc的方式进行通信。

#### 1-1-1.发展演变

> 由单体应用演变为微服务，后续可能还将继续演变，比如说：网格架构

##### 1-1-1-1.单体应用架构

> ALL IN ONE，我们把所有的功能都封装在一个应用中。
>
> 直白点说：你的项目就是一个war包！

**优点：**

- 所有的东西都在一个war包，好管理。方便部署、扩展只需要复制出一份再部署一遍就行了。

**缺点：**

- 任何一个bug都需要停掉重新打包、部署

##### 1-1-1-2.微服务架构

> 就是把all in one的大应用按照功能模块进行拆分，拆分成多个小应用。

**优点：**

- 每个功能元素的服务都是一个可替换的、可独立升级部署的服务。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220531214741024.png" alt="image-20220531214741024" style="zoom:67%;" />

> 微服务架构思想作者的论述：
>
> ​	https://martinfowler.com/articles/microservices.html

### 1-2.第一个Spring Boot项目

> 相信这个非常简单你已经会了！
>
> 选择启动器，填好你想要的包名，类名即可。

## 2、Spring Boot原理初探

> Spring Boot启动过程：
>
> 我们工作中使用ssm框架的时候，会随着项目的变大，配置也会越来越多，慢慢的就变得不好管理和维护了。springboot就是解决这个问题的，它可以简化配置，能够自动装配，同时呢集成了大量第三方库，像redis、mongoDB、JPA等等，这些第三方库几乎可以零配置的开箱即用。在springboot的启动类中，有个注解：@SpringBootApplication，这个注解里呢又有@SpringBootConfiguration、@EnableAtuoConfiguration、@ComponentScan三个重要的注解。@SpringBootConfiguration它本质上是一个@Confuguration，也就是说我们声明这个类是SpringBoot类。ComponentScan它扫描了启动类同级类和同级目录下的所有类，所以springboot项目通常会把入口放到顶层目录中，保证源码目录下的所有类能够被扫描到。@EnableAutoConfiguration它的作用就是打开自动配置的功能，找到META-INF/spring.factories文件，把这个文件里的配置项通过反射的方式实例化到Ioc容器中。这个文件里配置了非常多的第三方库，像springmvc、aop、redis等等，但是并不是把这个文件下所有的配置项都进行加载，比如说WebMvcAutoConfiguration，它里面有个注解@CondionalOnClass，也就是说你要添加了依赖才可以让它生效。
>
> ​	那么，在我们启动的时候，启动做了四件事情：
>
> ​		1：判断是不是web项目；
>
> ​		2： 设置初始化器（Initializer）；
>
> ​		3：设置监听器；
>
> ​		4：推断程序主入口。
>
> 
>
> 要点：
>
> - 从@SpringBootApplication说起
> - 启动、run方法
>

## 3、Spring Boot配置

> 支持application.properties,application.yaml,application.yml配置。
>
> properties只支持键值对
>
> yml支持键值对、对象、map、数组等功能更加强大，推荐使用。

<img src="C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220612204853400.png" alt="image-20220612204853400" style="zoom:47%;" />

> 有了这个依赖可以对你的配置文件进行提示

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220612211831082.png" alt="image-20220612211831082" style="zoom:50%;" />

> @Value

```java
@Component
@Data
public class Dog {

    @Value("${dog.name:}")
    private String name;

    @Value("${dog.age}")
    private int age;
}
```

```yaml
dog:
	name: 小狗
    age: 3
```

> @ConfigurationProperties

```java
@Component
@Data
@ConfigurationProperties("person") //该注解可以从配置文件找到对应的类型，把里面的属性一一映射
public class Person {

    private String name;

    private int age;

    // 布尔类型必须为引用类型，不能写成boolean，否则值只是false
    private Boolean isCoolGuy;

    private Map maps;

    private List hobby;

    private Dog dog;
}
```

```yaml
person:
  name: Leo
  age: 28
  isCoolGuy: true
  maps: {k1: v1,k2 : v2}
  hobby:
    - book
    - code
    - movie
  dog:
    name: 小狗
    age: 3
```

> 当然，yml的功能很强大，你可以在yml里使用一些比较好玩的东西。

```yaml
person:
  name: Leo_${random.uuid}  // 随机生成uuid
  age: ${random.int}		// 随机生成一个int
  isCoolGuy: true
  maps: {k1: v1,k2 : v2}
  hobby:
    - book
    - code
    - movie
  dog:
    name: 小狗_${person.happy:hello} // 如果person.happy存在则使用，不存在则使用hello
    age: 3
```

> 指定某个配置文件：@PropertySource()

```java
@Data
@Component
@PropertySource("classpath:user.properties")
public class User {

    @Value("${name}")
    private String name;

    @Value("${age}")
    private int age;
}
```

```properties
name=liuwx
age=23
```

@ConfigurationProperties与@Value对比

|                | @ConfigurationProperties | @Value     |
| -------------- | ------------------------ | ---------- |
| 功能           | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定       | 支持                     | 不支持     |
| SpEL           | 不支持                   | 支持       |
| JSR303数据校验 | 支持                     | 不支持     |
| 复杂类型封装   | 支持                     | 不支持     |

> 看了上面，你一定很好奇什么是松散绑定和JSR303数据校验吧。

**松散绑定：**

> 就是没有那么严格的要求配置和属性一致。请看下例：

```java
@Data
@Component
//@PropertySource("classpath:staff.yml") // 不可以这么组合使用，@ConfigurationProperties注解找的就是application.yml这些
@ConfigurationProperties("staff")
public class Staff {

    private String firstName;

    private String lastName;
}
```

```yaml
staff:
  last-name: Liu
  first-name: Leo
```

**JSR303校验：**

> 所谓JSR303校验，就是对属性的类型进行校验。请看下例：

```xml
<!-- 实现了jdk的Validator、ConstraintValidator、Constraint接口，提供校验实现 -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.1.5.Final</version>
</dependency>
<!-- 对hibernate-validator中不完善的校验规则进行补充 -->
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.0.1.Final</version>
</dependency>
```



```java
@Data
@Component
//@PropertySource("classpath:staff.yml") // 不可以这么组合使用，@ConfigurationProperties注解找的就是application.yml这些
@ConfigurationProperties("staff")
public class Staff {

    private String firstName;

    private String lastName;

    @javax.validation.constraints.Email()
    private String Email;
}
```

```yaml
```

@RequestBody注解与@ResponseBody的作用

**@Valid与@@Validated的区别:**

> @Validated：用在方法入参上无法单独提供嵌套验证功能。不能用在成员属性（字段）上，也无法提示框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。
>
> @Valid：用在方法入参上无法单独提供嵌套验证功能。能够用在成员属性（字段）上，提示验证框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。

## 4.Spring Boot多环境配置

