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
> 用大佬的话来理解，就是 spring boot 其实不是什么新的框架，它默认配置了很多框架的使用方式，就像 maven 整合了所有的 jar 包，spring boot 整合了所有的框架，总结以下及几点：
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
> ​		2：设置初始化器（Initializer）；
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

```java
@Data
public class Staff {

    @Valid
    @Email(message = "邮箱格式不正确")
    private String email;
}
```

## 4、Spring Boot多环境配置

> 可以放配置文件的位置：
>
> ​	file:./config/   	项目根目录下的/config
>
> ​	file:./		 	 项目根目录下
>
> ​	classpath:/config/   resource目录中的config目录下
>
> ​	classpath:/   	   resource目录下（类路径下 classpath）

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220730222559092.png" alt="image-20220730222559092" style="zoom: 80%;" />



**读取顺序(1的优先级最高，依次递减)：**

 1、config/application.properties（项目根目录中config目录下）
 2、config/application.yml
 3、application.properties（项目根目录下）
 4、application.yml
 5、resources/config/application.properties（项目resources目录中config目录下）
 6、resources/config/application.yml
 7、resources/application.properties（项目的resources目录下）
 8、resources/application.yml

**多环境切换：**

```yml
spring:
  profiles:
    active: dev
```

**@Condition:**



![image-20220731162441728](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731162441728.png)



如何判断yml中的配置可以写哪些呢？比如说：server下可以写port，这个是由什么决定的呢？为什么可以写port，那又有哪些不可以写呢？

![image-20220731171129696](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731171129696.png)

xxxAutoConfiguration.class

xxxProperties.class

如：

```java
@Configuration(proxyBeanMethods = false)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
@ConditionalOnClass(ServletRequest.class)
@ConditionalOnWebApplication(type = Type.SERVLET)
@EnableConfigurationProperties(ServerProperties.class)  -- 关键
@Import({ ServletWebServerFactoryAutoConfiguration.BeanPostProcessorsRegistrar.class,
		ServletWebServerFactoryConfiguration.EmbeddedTomcat.class,
		ServletWebServerFactoryConfiguration.EmbeddedJetty.class,
		ServletWebServerFactoryConfiguration.EmbeddedUndertow.class })
public class ServletWebServerFactoryAutoConfiguration {
    ...
}
```

```java
@ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)
public class ServerProperties {

	/**
	 * Server HTTP port.
	 */
	private Integer port;

	/**
	 * Network address to which the server should bind.
	 */
	private InetAddress address;
    ...
}
```

## 5、Spring Boot Web开发

> 要解决的问题：
>
> ​	导入静态资源
>
> ​	首页
>
> ​	模板引擎Thymeleaf
>
> ​	装配扩展SpringMvc
>
> ​	增删改查
>
> ​	拦截器
>
> ​	国际化

### 5-1.静态资源

> web项目的静态资源，诸如css,js,jsp,image等是从哪里找的呢？是否可以自定义呢？

```java
WebMvcAutoConfiguration.java
...
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
    addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
        registration.addResourceLocations(this.resourceProperties.getStaticLocations());
        if (this.servletContext != null) {
            ServletContextResource resource = new ServletContextResource(this.servletContext, SERVLET_LOCATION);
            registration.addResourceLocations(resource);
        }
    });
}
...
```



```java
上述代码块中：

this.mvcProperties.getStaticPathPattern() =  "/**"

this.resourceProperties.getStaticLocations() =
private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
      "classpath:/resources/", "classpath:/static/", "classpath:/public/" };
也就是说"/**"代表的路径有4种，/META-INF/resources、resources、static、public这四种。
意味着这四个路径下的静态资源可以直接访问！
比如说：我有个1.js，放在static路径下，我可以直接用：http://localhost:8080/1.js 进行访问。
```

![image-20220731221124257](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731221124257.png)



![image-20220731221138443](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731221138443.png)



**截图可见四个位置如果都放置了同一个1.js，那谁的优先级最高呢？**

**resources>static>public**

上述例子中静态资源位置是默认配置好了的，那是否可以自定义呢？比如说我们想设置classpath:/leo/下的文件是默认加载的静态资源文件夹。

当然可以，只需要在application.yml中增加配置。

```yaml
spring:
  web:
    resources:
      static-locations: classpath:/leo/
```

![image-20220731222001086](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731222001086.png)

![image-20220731222015608](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731222015608.png)

### 5-2.首页

> 一个web工程启动后自动访问首页，这个如何做呢？

```java
WebMvcAutoConfiguration.java
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
                                                               FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
    WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
        new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
        this.mvcProperties.getStaticPathPattern());
    welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
    welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
    return welcomePageHandlerMapping;
}
private Resource getIndexHtml(Resource location) {
    try {
        Resource resource = location.createRelative("index.html");
        if (resource.exists() && (resource.getURL() != null)) {
            return resource;
        }
    }
    catch (Exception ex) {
    }
    return null;
}
```

阅读源码可知，Spring Boot会自动在/META-INF/resources、resources、static、public这四个路径下寻找index.html作为首页！

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>WelcomePage</title>
</head>
<body>
<h1>Welcome!</h1>
</body>
</html>
```

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731225433928.png" alt="image-20220731225433928" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220731225312446.png" alt="image-20220731225312446" style="zoom:57%;" />

### 5-3.模板引擎Thymeleaf

> 视图层，取代了jsp。

**步骤：**

1. 导入jar包

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   ```

2. 阅读源码发现，springboot会去classpath:/templates/路径下找后缀为.html的文件

   ```java
   @ConfigurationProperties(prefix = "spring.thymeleaf")
   public class ThymeleafProperties {
   
   	private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;
   
   	public static final String DEFAULT_PREFIX = "classpath:/templates/";
   
   	public static final String DEFAULT_SUFFIX = ".html";
       ...
   }
   ```

3. 编写视图层代码

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
   <h1>Thymeleaf</h1>
   <div th:text="${msg}"></div>
   </body>
   </html>
   ```

4. 编写Controller

   ```java
   @Controller
   public class TestController {
   
       @RequestMapping("/test")
       public String test(Model model) {
           model.addAttribute("msg","hello,Thymeleaf!");
           return "test";
       }
   }
   ```

5. 运行测试

   <img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220801110512688.png" alt="image-20220801110512688" style="zoom:67%;" />

### 5-4.装配扩展SpringMvc

> 扩展mvc

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Override
    public void configureAsyncSupport(final AsyncSupportConfigurer configurer) {
        configurer.setDefaultTimeout(3000);
        configurer.registerCallableInterceptors(timeoutInterceptor());
    }

    @Bean
    public TimeoutCallableProcessingInterceptor timeoutInterceptor() {
        return new TimeoutCallableProcessingInterceptor();
    }
}
```

### 5-5.国际化

> 多种语言切换

查看WebMvcAutoConfiguration.java并追踪其相关源码，

```java
@Override
@Bean
@ConditionalOnMissingBean(name = DispatcherServlet.LOCALE_RESOLVER_BEAN_NAME)
public LocaleResolver localeResolver() {
    if (this.webProperties.getLocaleResolver() == WebProperties.LocaleResolver.FIXED) {
        return new FixedLocaleResolver(this.webProperties.getLocale());
    }
    AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
    localeResolver.setDefaultLocale(this.webProperties.getLocale());
    return localeResolver;
}
```

可以发现this.webProperties.getLocaleResolver()默认是ACCEPT_HEADER，也就是根据ACCEPT_HEADER传的值进行语言切换的。

同时也可以在配置文件种设置this.webProperties.getLocaleResolver()的值为FIXED，然后指定语言类型。

```yaml
spring:
  web:
    locale-resolver: fixed
    locale: en_US
```

那么，我们如何进行自定义设置呢？

编写自定义LocaleResolver并交由spring容器管理。

```java
public class MyLocaleResolver implements LocaleResolver {

    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        Locale locale = Locale.getDefault();
        String lan = request.getParameter("lan");
        System.out.println("language"+lan);
        if (Optional.ofNullable(lan).isPresent()) {
            String[] lanArr = lan.split("_");
            locale = new Locale(lanArr[0],lanArr[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {

    }
}
```

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Bean("localeResolver")
    public MyLocaleResolver getMyLocaleResolver() {
        MyLocaleResolver myLocaleResolver = new MyLocaleResolver();
        return myLocaleResolver;
    }


    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index.html");
        registry.addViewController("/index.html").setViewName("index.html");
    }
}
```

Q&A:
为什么我自定义的LocaleResolver不生效？

  @ConditionalOnMissingBean(name = DispatcherServlet.LOCALE_RESOLVER_BEAN_NAME)

```java
/** Well-known name for the LocaleResolver object in the bean factory for this namespace. */
public static final String LOCALE_RESOLVER_BEAN_NAME = "localeResolver";
```

所以我们在@Bean时必须指定name或value为localeResolver: @Bean("localeResolver")

### 5-6.拦截器

> 如果不登陆，那就不允许访问列表等页面。
>
> 1：编写拦截器
>
> 2：注册拦截器

拦截器代码：

```java
public class LoginHandleInterceptor implements HandlerInterceptor {

@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object userInfo = request.getSession().getAttribute("userInfo");
        if(Optional.ofNullable(userInfo).isPresent()) {
            request.setAttribute("msg","没有权限，请先登录");
            request.getRequestDispatcher("/").forward(request,response);
            return false;
        }
        return true;
    }
}
```

注册拦截器：

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LoginHandleInterceptor())
        .addPathPatterns("/**")
        .excludePathPatterns("/index","/","/login","/css/**","/img/**","/js/**");

}
```

### 5-7.错误页面

> BasicErrorController
>
> 直接404.html



## 6、Spring Boot整合DAO

### 6-1.Spring Boot整合JDBC

> 整合数据库了。
>
> DataSourceProperties.java
>
> DataSourceAutoConfig.java

- xml加入jdbc和mysql驱动的依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

- 配置连接信息

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/myemployees?serverTimezone=Asia/Shanghai&characterEncoding=utf8
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
```

- 编写Controller

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @Resource
    DataSource dataSource;

    @Autowired
    JdbcTemplate jdbcTemplate;

    @RequestMapping("/getList")
    public List<Map<String,Object>> getUserList() throws SQLException {
        // 原始dataSource（数据源）连接查询
        /*Connection connection = dataSource.getConnection();
        String sql = "select * from dept";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        ResultSet resultSet = preparedStatement.executeQuery();
        System.out.println(resultSet);
        connection.close();*/

        // 使用jdbcTemplate连接
        String sql = "select * from dept";
        List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql);
        return maps;
    }
}
```

- 访问结果

<img src="C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220811231745365.png" alt="image-20220811231745365" style="zoom: 67%;" />



### 6-2.Spring Boot整合Druid数据源

> 在上面的基础上只需要加一句话！！！
>
> type: com.alibaba.druid.pool.DruidDataSource

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<!-- 使用druid进行监控 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.10</version>
</dependency>
```

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/myemployees?serverTimezone=Asia/Shanghai&characterEncoding=utf8
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
```

高级设置：

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/myemployees?serverTimezone=Asia/Shanghai&characterEncoding=utf8
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
    #配置druid
    druid:
      # 初始化大小，最小，最大
      initial-size: 5
      min-idle: 5
      max-active: 20
      # 配置获取连接等待超时的时间
      max-wait: 60000
      # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位毫秒
      time-between-eviction-runs-millis: 60000
      # 配置一个连接在池中最小生存时间
      min-evictable-idle-time-millis: 300000
      validation-query: SELECT 1 FROM sys_user
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      # 打开 PSCache，并且指定每个连接上 PSCache 的大小
      pool-prepared-statements: true
      max-pool-prepared-statement-per-connection-size: 20
      # 配置监控统计拦截的 Filter， SQL 去掉后监控界面无法统计，wall 用于防火墙
      filters: stat,wall,log4j
      # 通过 connection-properties 属性打开 mergeSql 功能；慢 SQL 记录
      connection-properties: druid.stat.mergeSql\=true;druid.stat.slowSqlMillis\=5000
      # 配置 DruidStatFilter
      web-stat-filter:
        enabled: true
        url-pattern: /*
        exclusions: .js,*.gif,*.jpg,*.bmp,*.png,*.css,*.ico,/druid/*
      # 配置 DruidStatViewServlet
      stat-view-servlet:
        url-pattern: /druid/*
        # IP 白名单，没有配置或者为空，则允许所有访问
        allow: 127.0.0.1
        # IP 黑名单，若白名单也存在，则优先使用
        deny: 192.168.31.33
        # 禁用 HTML 中 Reset All 按钮
        reset-enable: false
        # 登录用户名/密码
        login-username: root
        login-password: 123456
  #配置mybatis
mybatis-plus:
  global-config:
    db-config:
      id-type: auto
      field-strategy: not_empty
      table-underline: true
      db-type: mysql
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

![image-20220815221424231](C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220815221424231.png)

### 6-3.Spring Boot整合mybatis(Druid数据源)

> 整合mybatis，使用druid数据源

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.2</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.10</version>
</dependency>
<!-- 日志-->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>

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

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/myemployees?serverTimezone=Asia/Shanghai&characterEncoding=utf8
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
    #配置druid
    druid:
      # 初始化大小，最小，最大
      initial-size: 5
      min-idle: 5
      max-active: 20
      # 配置获取连接等待超时的时间
      max-wait: 60000
      # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位毫秒
      time-between-eviction-runs-millis: 60000
      # 配置一个连接在池中最小生存时间
      min-evictable-idle-time-millis: 300000
      validation-query: SELECT 1 FROM sys_user
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      # 打开 PSCache，并且指定每个连接上 PSCache 的大小
      pool-prepared-statements: true
      max-pool-prepared-statement-per-connection-size: 20
      # 配置监控统计拦截的 Filter， SQL 去掉后监控界面无法统计，wall 用于防火墙
      filters: stat,wall,log4j
      # 通过 connection-properties 属性打开 mergeSql 功能；慢 SQL 记录
      connection-properties: druid.stat.mergeSql\=true;druid.stat.slowSqlMillis\=5000
      # 配置 DruidStatFilter
      web-stat-filter:
        enabled: true
        url-pattern: /*
        exclusions: .js,*.gif,*.jpg,*.bmp,*.png,*.css,*.ico,/druid/*
      # 配置 DruidStatViewServlet
      stat-view-servlet:
        url-pattern: /druid/*
        # IP 白名单，没有配置或者为空，则允许所有访问
        allow: 127.0.0.1
        # IP 黑名单，若白名单也存在，则优先使用
        deny: 192.168.31.33
        # 禁用 HTML 中 Reset All 按钮
        reset-enable: false
        # 登录用户名/密码
        login-username: admin
        login-password: 123456
#配置mybatis
mybatis:
  type-aliases-package: com.xiu.pojo
```

```java
package com.xiu.mapper;

import com.xiu.pojo.Staff;
import org.apache.ibatis.annotations.Mapper;

import java.util.List;

@Mapper
public interface StaffMapper {
    /**
     *查 读取(Retrieve)
     * fetch data
     * @return List<User>
     */
    List<Staff> queryAlluser();
    /**
     *查 读取(Retrieve)
     * fetch data by userid
     * @param  userid
     * @return User
     */
    /**
     @Select("select * from user where userid = #{userid}")
     本人喜欢讲SQL语句写在mapper.xml文件中，这样显得代码比较整齐
     在后期开发中，一旦SQL语句变得繁琐起来，不利于维护
     但是这样用注解的形式也完全可行，毕竟有些人可能写配置文件可能已经快写吐了
     */
    Staff queryuserbyid( int staffId);
    /**
     * 增加(Create)
     * add data by user
     * @param staff
     * @return int
     */
    boolean AddUser(Staff staff);
    /**
     * 删除(Delete)
     * @param id
     * @return int
     */
    boolean DelUser(int id);
    /**
     * 更新(Update)
     * @param staff
     * @return boolean
     */
    boolean UpdUser(Staff staff);

    Staff queryuserbyname(String name);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace:改mapper.xml映射文件的唯一标识并且必须和数据处理层的接口的路径相同-->
<mapper namespace="com.xiu.mapper.StaffMapper">
    <!--   必须添加property属性 ，区别于是否加主键-->
    <resultMap id="user" type="Staff">
        <id column="id" property="id" javaType="int" ></id>
        <result column="name" property="name" javaType="String" ></result>
        <result column="password" property="password" javaType="String" ></result>
    </resultMap>
    <!--id的值必须和数据处理层的接口名一致-->
    <!--此处的User-->
    <select id="queryAlluser" resultType="com.xiu.pojo.Staff">
        select * from  staff
    </select>
    <select id="queryuserbyid"   parameterType="int" resultMap="user" resultType="Staff">
        select * from  staff
        <trim suffixOverrides="and"  prefixOverrides="and">
            <where>
                <if test="id!=null">
                    and id = #{staffId}
                </if>
            </where>
        </trim>
    </select>
    <select id="queryuserbyname" resultType="Staff" parameterType="string" resultMap="user">
        select * from  staff
        <trim suffixOverrides="and"  prefixOverrides="and">
            <where>
                <if test="name!=null">
                    and name = #{name}
                </if>
            </where>
        </trim>
    </select>
    <update id="UpdUser" parameterType="Staff">
    </update>
    <delete id="DelUser"></delete>
    <insert id="AddUser" parameterType="Staff" >
        insert into  staff value (${id},#{name},${password})
    </insert>
</mapper>
```

![image-20220816210529021](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220816210529021.png)

```java
package com.xiu.config;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.Map;

@Configuration
public class DataSourceConfig {

    /**
     * 当向容器中添加了 Druid 数据源
     * 使用 @ConfigurationProperties 将配置文件中 spring.datasource 开头的配置与数据源中的属性进行绑定
     * @return
     */
    @ConfigurationProperties("spring.datasource")
    @Bean
    public DataSource dataSource() throws SQLException {
        DruidDataSource druidDataSource = new DruidDataSource();
        return druidDataSource;
    }
}
```

<img src="C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220816210607130.png" alt="image-20220816210607130" style="zoom:67%;" />

![image-20220816210637545](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220816210637545.png)

![image-20220816210651486](C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220816210651486.png)

![image-20220816210702349](C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220816210702349.png)

## 7、Spring Boot整合Spring Data JPA

> JPA：Java Persistence API
>
> Spring Data API 也是一个java的持久层框架，**是 Spring 官方的天然全家桶**,可以去掉繁琐的额外配置，
>
> 底层以 Hibernate 为封装，对外提供了超级灵活的使用接口，又非常符合面向对象和 Rest 的风格
>
> 上手简单、开发效率高，又对对象的支持比较好，又有很大的灵活性，市场的认可度越来越高。

### 7-1.初识Spring Data JPA

```xml
mysql驱动
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
引入JPA
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/myemployees?serverTimezone=Asia/Shanghai&characterEncoding=utf8
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
```

```java
@Data
@Entity
public class Staff {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    Integer id;

    String name;

    String password;
}
```

```java
public interface StaffRepository extends CrudRepository<Staff, Integer> {
}
```

```java

@RestController
@RequestMapping(path = "/staff")
public class StaffController {
    @Autowired
    private StaffRepository staffRepository;


    @GetMapping(path = "/add")
    public Staff addNewUser(@RequestParam String name, @RequestParam String password) {
        Staff n = new Staff();
        n.setName(name);
        n.setPassword(password);
        staffRepository.save(n);
        return n;
    }
    @GetMapping(path = "/all")
    public Iterable<Staff> getAllUsers() {
        return staffRepository.findAll();
    }

    @PostMapping(path = "/update")
    public Staff updateUser(@RequestBody Staff staff) {
        return staffRepository.save(staff);
    }

    @GetMapping(path = "/info/{id}")
    public Optional<Staff> findOne(@PathVariable Integer id) {
        return staffRepository.findById(id);
    }

    @GetMapping(path = "/delete/{id}")
    public void delete(@PathVariable Integer id) {
        staffRepository.deleteById(id);
    }
}
```







## 8、Spring Security

> 认证
>
> shou'quan





## 9、异步任务

> 异步执行，不会等待。
>
> @Sync
>
> @EnableSync
>
> 
>
> 注：@Async所修饰的函数不要定义为static类型，这样异步调用不会生效

```java
@Service
public class AsyncService {

    @Async
    public void hello() {
        try {
            TimeUnit.SECONDS.sleep(3);
            System.out.println("方法执行种。。。");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }
}
```

```java
@SpringBootApplication
@EnableAsync
public class Springboot12AsyncTaskApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot12AsyncTaskApplication.class, args);
    }
}
```

**异步回调**

> 我们需要使用Future来返回异步调用的结果，就像如下方式改造doTaskOne函数：

```java
@Async
public Future<Boolean> doTaskOne() throws Exception {
    System.out.println("开始做任务一");
    long start = System.currentTimeMillis();
    Thread.sleep(random.nextInt(10000));
    long end = System.currentTimeMillis();
    System.out.println("完成任务一，耗时：" + (end - start) + "毫秒");
    return new AsyncResult<>(true);
}
tasktwo与taskThree相似。
```

```java
@RequestMapping("/hello3")
public String hello3() throws Exception {
    String result = "OK";
    Future<Boolean> task = taskHasReturn.doTaskOne();
    Future<Boolean> task2 = taskHasReturn.doTaskTwo();
    Future<Boolean> task3 = taskHasReturn.doTaskThree();

    if (task.isDone() && task2.isDone() && task3.isDone()) {
        System.out.println("all tasks is done");
    }
    return result;
}
```

## 10、邮件任务

> 发邮件，
>
> 1：发简单邮件，只有正文。
>
> 2：发送带有附件的邮件。
>
> 3：依赖thyme leaf模板发送邮件。
>
> https://www.bilibili.com/video/BV12J411M7Sj?p=40&vd_source=06f10324a2b249ad4613f8ea7b8aeb1c
>
> 

### 通用准备：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>

```

```yaml
spring:
  mail:
    #坑爹的地方：host 通道个人邮箱和企业邮箱通道不同。163的个人邮箱：smtp.163.com ，企业邮箱：smtp.qiye.163.com
    # 腾讯的，个人smtp.qq.com， 企业的：smtp.exmail.qq.com
    host: smtp.163.com
    username: xxx@163.com
    # 口令是邮箱开通的smtp服务后得到的客户端授权码,不是你的邮箱登录密码
    password: xxxx
```

```java

@Service("mailService")
public class MailServiceImpl implements MailService {

    private static final Logger logger = LoggerFactory.getLogger(MailServiceImpl.class);


    /**
     * JavaMailSender是Spring Boot在MailSenderPropertiesConfiguration 类中配直好的，该类在 Mail
     * 自动配置类 MailSenderAutoConfiguration 中导入 因此这里注入 JavaMailSender 就可以使用了
     */
    @Autowired
    private JavaMailSender mailSender;


    /**
     * 1、发送普通文本邮件
     *
     * @param mailFrom     发件人邮箱
     * @param mailFromNick 发件人昵称
     * @param mailTo       收件人邮箱
     * @param cc           抄送人邮箱(可为空，方法内部处理)
     * @param subject      主题
     * @param content      内容
     */
    @Override
    public void sendSimpleMail(String mailFrom, String mailFromNick, String mailTo, String cc,
                               String subject, String content) {
        try {
            // 多个收件人之间用英文逗号分隔
            String[] mailToArr = mailTo.split(",");
            for (String address : mailToArr) {
                // 简单邮件信息类
                MimeMessage mimeMessage = mailSender.createMimeMessage();
                // HTML邮件信息类
                MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(mimeMessage);
                // 昵称
                mimeMessageHelper.setFrom(new InternetAddress(mailFromNick + " <" + mailFrom + ">"));
                mimeMessageHelper.setTo(address);
                if (!StringUtils.isEmpty(cc)) {
                    mimeMessageHelper.setCc(cc);
                }
                mimeMessageHelper.setSubject(subject);
                mimeMessageHelper.setText(content);

                mailSender.send(mimeMessage);
            }
        } catch (Exception e) {
            logger.error("发送邮件失败：", e);
        }
    }

    /**
     * 2、发送带附件的邮件
     *
     * @param mailFrom     发件人
     * @param mailFromNick 发件人昵称
     * @param mailTo       收件人
     * @param cc           抄送人
     * @param subject
     * @param content
     * @param files
     */
    @Override
    public void sendMailWithAttachments(String mailFrom, String mailFromNick, String mailTo, String cc,
                                        String subject, String content, List<File> files) {

        MimeMessage mimeMessage = mailSender.createMimeMessage();
        try {
            /*
            第二个参数true表示构造一个multipart message类型的邮件，multipart message类型的邮件包含多个正文、附件以及内嵌资源，
            邮件的表现形式更丰富
             */
            MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(mimeMessage, true);
            mimeMessageHelper.setFrom(new InternetAddress(mailFromNick + " <" + mailFrom + ">"));
            mimeMessageHelper.setSubject(subject);
            mimeMessageHelper.setText(content);

            // 设置多个收件人
            String[] toAddress = mailTo.split(",");
            mimeMessageHelper.setTo(toAddress);
            if (!StringUtils.isEmpty(cc)) {
                mimeMessageHelper.setCc(cc);
            }
            // 添加附件
            if (null != files) {
                for (File file : files) {
                    // 通过addAttachment方法添加附件
                    mimeMessageHelper.addAttachment(file.getName(), file);
                }
            }
        } catch (MessagingException e) {
            e.printStackTrace();
        }
        //发送邮件
        mailSender.send(mimeMessage);

    }

    /**
     * 3、正文内容带图片
     *
     * @param mailFrom
     * @param mailFromNick
     * @param mailTo
     * @param cc           抄送人
     * @param subject
     * @param content
     * @param imagePaths
     * @param imageId
     */
    @Override
    public void sendMailWithImage(String mailFrom, String mailFromNick, String mailTo, String cc, String subject,
                                  String content, String[] imagePaths, String[] imageId) {
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        try {
            MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(mimeMessage, true);
            mimeMessageHelper.setFrom(new InternetAddress(mailFromNick + " <" + mailFrom + ">"));
            // 设置多个收件人
            String[] toAddress = mailTo.split(",");
            mimeMessageHelper.setTo(toAddress);
            if (!StringUtils.isEmpty(cc)) {
                mimeMessageHelper.setCc(cc);
            }
            mimeMessageHelper.setSubject(subject);
            // 第二个参数为true表示邮件正文是html格式的，默认是false
            mimeMessageHelper.setText(content, true);

            // 添加图片
            if (imagePaths != null && imagePaths.length != 0) {
                for (int i = 0; i < imagePaths.length; i++) {
                    // 通过FileSystemResource构造静态资源
                    FileSystemResource fileSystemResource = new FileSystemResource(imagePaths[i]);
                    // 调用addInline方法将资源加入邮件对象中
                    mimeMessageHelper.addInline(imageId[i], fileSystemResource);
                }
            }

            mailSender.send(mimeMessage);

        } catch (MessagingException e) {
            System.out.println(e);
        }
    }

    /**
     * 4、使用Themeleaf构建邮件模板。需额外加spring-boot-starter-thymeleaf依赖
     *
     * @param mailFrom
     * @param mailFromNick
     * @param mailTo
     * @param cc
     * @param subject
     * @param content
     */
    @Override
    public void sendHtmlMailThymeLeaf(String mailFrom, String mailFromNick, String mailTo, String cc,
                                      String subject, String content) {
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        try {
            MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(mimeMessage, true);
            mimeMessageHelper.setFrom(new InternetAddress(mailFromNick + " <" + mailFrom + ">"));
            // 设置多个收件人
            String[] toAddress = mailTo.split(",");
            mimeMessageHelper.setTo(toAddress);
            if (!StringUtils.isEmpty(cc)) {
                mimeMessageHelper.setCc(cc);
            }
            mimeMessageHelper.setSubject(subject);
            // 第二个参数为true表示邮件正文是html格式的，默认是false
            mimeMessageHelper.setText(content, true);

            mailSender.send(mimeMessage);
        } catch (MessagingException e) {
            System.out.println(e);
        }
    }
}
```

```java
package com.xiu;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;


@RunWith(SpringRunner.class)
@SpringBootTest
public class SendmailApplicationTest {
    @Autowired
    private MailService mailService;

    // 发件人要跟yml配置文件里填写的邮箱一致
    String mailFrom = "m13408606645@163.com";
    // 收件人
    String mailTo = "1181778947@qq.com,971465407@qq.com";
    // 抄送
    String cc = "971465407@qq.com";


    /**
     * 1、测试普通邮件发送
     */
    @Test
    public void testSendSimpleMail() {

        String result = "发送邮件成功";
        try {
            mailService.sendSimpleMail(mailFrom, "一个大帅哥", mailTo, cc, "TestMail", "Hello World !");
        } catch (Exception e) {
            result = "发送邮件失败！";
            System.out.println(result);
            System.out.println(e);
        }
        System.out.println(result);
    }

    /**
     * 2、测试带附件的方法
     */
    @Test
    public void testSendAttachment() {
        File imgFile = new File("src\\main\\resources\\img\\tong.jpg");
        File txtFile = new File("src\\main\\resources\\hello.txt");
        List<File> fileList = new ArrayList<>();
        fileList.add(imgFile);
        fileList.add(txtFile);

        // 发件人要跟yml配置文件里填写的邮箱一致
        String mailFrom = "m13408606645@163.com";
        String mailTo = "1181778947@qq.com";

        String result = "发送邮件成功";
        try {
            mailService.sendMailWithAttachments(mailFrom, "一个大帅哥", mailTo, cc, "TestMail", "what do you want to say ?", fileList);
        } catch (Exception e) {
            result = "发送邮件失败！";
            System.err.println(result);
            System.err.println(e);
        }
        System.out.println(result);
    }


    /**
     * 3、正文带图片
     */
    @Test
    public void testSendMailWithImage() {
        // 图片路径
        String image01Path = "src\\main\\resources\\img\\tong.jpg";
        String image02Path = "src\\main\\resources\\img\\work.jpg";
        String[] imageArr = new String[]{image01Path, image02Path};
        String[] imageIdArr = new String[]{"image01", "image02"};

        String result = "发送邮件成功";
        try {
            String contentHtml = "这是图片1:<div><img src='cid:image01'/></div>" +
                    "这是图片2:<div><img src='cid:image02'/></div>";
            mailService.sendMailWithImage(mailFrom, "一个大帅哥", mailTo, cc, "TestMail", contentHtml, imageArr, imageIdArr);
        } catch (Exception e) {
            result = "发送邮件失败！";
            System.err.println(result);
            System.err.println(e);
            e.printStackTrace();
        }
        System.out.println(result);
    }
}
```



### 特别说明：

> 使用Thymeleaf模板发送邮件。因为在代码种维护html太麻烦并且容易出错，所以使用Thymeleaf。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

在resource下创建templates文件夹，然后在里面创建mailTemplate01.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h2>
    通过ThymeLeaf发送html邮件
</h2>
<table border="1">
    <tr>
        <td>用户名</td>
        <!--使用th,html标签内要导入xmlns:th="http://www.thymeleaf.org"-->
        <td th:text="${username}"></td>
    </tr>
    <tr>
        <td>年龄</td>
        <td th:text="${age}"></td>
    </tr>
</table>

<div>
    这是一张图片:
</div>
<div>
	<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220531214741024.png"/>
</div>
</body>
</html>

```

```java
/**
     * 4、使用ThymeLeaf
     */
// 注入TemplateEngine
@Autowired
TemplateEngine templateEngine;

@Test
public void testSendHtmlMailThymeLeaf() {

    // 注意导入的包是org.thymeleaf.context
    Context context = new Context();
    context.setVariable("username", "zrt");
    context.setVariable("age", "18");
    String content = templateEngine.process("mailTemplate01.html", context);

    mailService.sendHtmlMailThymeLeaf(mailFrom, "一个大帅哥", mailTo, cc, "TestMail", content);

    System.out.println("邮件发送成功");
}
```

## 11、定时任务

> https://blog.csdn.net/qianlixiaomage/article/details/106599951
>
> 每隔一段时间执行计划的任务。

### 入门

```java
@SpringBootApplication
@EnableScheduling
public class Springboot15ScheduleApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot15ScheduleApplication.class, args);
    }


    @Scheduled(cron = "*/5 08 18 * * * 2023")
    public void sayHello() {
        System.out.println("hello");
    }
}
```

### cron表达式

#### **结构**

> cron表达式是一个字符串，分为6或7个域，每两个域之间用空格分隔，
>
> 其语法格式为："秒域 分域 时域 日域 月域 周域 年域"
>
> 年域似乎并不能用，会报错。

#### **取值范围**

| 域名 | 可取值              | 可取符号（仅列部分常用） |
| ---- | ------------------- | ------------------------ |
| 秒域 | 0~59的整数          | * - , /                  |
| 分域 | 0~59的整数          | * - , /                  |
| 时域 | 0~23的整数          | * - , /                  |
| 日域 | 1~31的整数          | * - , / ? L              |
| 月域 | 1~12的整数或JAN~DEC | * - , /                  |
| 周域 | 1~7的整数或SUN~SAT  | * - , / ? L #            |
| 年域 | 1970~2099的整数     | * - , /                  |

### 基于接口的方式

> 问题：
>
> ​	如果我们改变了cron，就需要重启服务才能生效。
>
> ​	那如何才能实时生效呢？
>
> ​	**可以使用接口来完成定时任务，统一将定时器信息存放在数据库中。**

```sql
drop table if exists `scheduled`;
create table `scheduled` (
 `cron_id` varchar(30) NOT NULL primary key,
 `cron_name` varchar(30) NULL,
 `cron` varchar(30) NOT NULL
);
insert into `scheduled` values ('1','定时器任务一','0/6 * * * * ?');
```

```properties
## mysql数据源配置
spring.datasource.url=jdbc:mysql://host:3306/dbname?useUnicode=true&serverTimezone=Asia/Shanghai
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

```java
package com.zhenma.mapper;
 
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;
 
@Repository
@Mapper
public interface CronMapper {
    @Select("select cron from scheduled where cron_id = #{id}")
    public String getCron(int id);
}
```

```java
package com.zhenma.scheduled;
 
import com.zhenma.mapper.CronMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.scheduling.annotation.SchedulingConfigurer;
import org.springframework.scheduling.config.ScheduledTaskRegistrar;
import org.springframework.scheduling.support.CronTrigger;
import org.springframework.stereotype.Component;
 
@Component
@EnableScheduling
public class MyTask implements SchedulingConfigurer {
 
    @Autowired
    protected CronMapper cronMapper;
 
    @Override
    public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
        scheduledTaskRegistrar.addTriggerTask(() -> process(),
                triggerContext -> {
                    String cron = cronMapper.getCron(1);
                    if (cron.isEmpty()) {
                        System.out.println("cron is null");
                    }
                    return new CronTrigger(cron).nextExecutionTime(triggerContext);
                });
    }
 
    private void process() {
        System.out.println("基于接口定时任务");
    }
}
```

运行结果：

![image-20220903204628068](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220903204628068.png)
