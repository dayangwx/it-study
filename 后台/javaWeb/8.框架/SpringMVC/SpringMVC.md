# Spring MVC

---

## 1、MVC

### 1-1.什么是MVC

> M：model
>
> V：view 
>
> C：Controller
>
> MVC是模型(Model)、视图(View)、控制器(Controller)的简写，是一种软件设计规范。
>
> MVC不是一种设计模式，**MVC是一种架构模式**。当然不同的MVC存在差异。

**作用：**

- 将业务逻辑、数据、显示分离的方法来组织代码。
- **降低了视图与业务逻辑间的双向偶合**。

**Model（模型）：**

> 数据模型，提供要展示的数据，因此包含数据和行为，
>
> 可以认为是领域模型或JavaBean组件（包含数据和行为），
>
> 不过现在一般都分离开来：Value Object（数据Dao） 和 服务层（行为Service）。
>
> 也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。

**View（视图）：**

> 负责进行模型的展示，一般就是我们见到的用户**界面**，客户想看到的东西。

**Controller（控制器）：**

> 接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。
>
> 也就是说控制器做了个调度员的工作。

**最典型的MVC就是JSP + servlet + javabean的模式。**

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164234564.png" alt="image-20220529164234564" style="zoom:40%;" />

### 1-2.Model1时代

- 在web早期的开发中，通常采用的都是Model1。
- Model1中，主要分为两层，视图层和模型层。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/640.png" alt="图片" style="zoom:67%;" />



Model1优点：架构简单，比较适合小型项目开发；

Model1缺点：JSP职责不单一，职责过重，不便于维护；

### 1-3.Model2时代

Model2把一个项目分成三部分，包括**视图、控制、模型。**

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/640-16510314688474.png" alt="图片" style="zoom:67%;" />

1. 用户发请求
2. Servlet接收请求数据，并调用对应的业务逻辑方法
3. 业务处理完毕，返回更新后的数据给servlet
4. servlet转向到JSP，由JSP来渲染页面
5. 响应给前端更新后的页面

**职责分析：**

**Controller：控制器**

1. 取得表单数据
2. 调用业务逻辑
3. 转向指定的页面

**Model：模型**

1. 业务逻辑
2. 保存数据的状态

**View：视图**

1. 显示页面



**Model2的意义：**

> Model2这样不仅提高的代码的复用率与项目的扩展性，且大大降低了项目的维护成本。Model 1模式的实现比较简单，适用于快速开发小规模项目，Model1中JSP页面身兼View和Controller两种角色，将控制逻辑和表现逻辑混杂在一起，从而导致代码的重用性非常低，增加了应用的扩展性和维护的难度。Model2消除了Model1的缺点。

## 2、Servlet

> sun公司开发的**动态web**技术。

**步骤：**

- 导包
- 编写servlet
- 配置web.xml
- 编写视图层代码
- 配置tomcat

### 2-1.导包

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.1</version>
</dependency>
<!-- jstl用于解析EL表达式 -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

### 2-2.servlet代码

> 继承httpServlet，重写doPost和doGet方法

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String method2 = req.getParameter("method2");
    String msg = method2

    System.out.println(msg);
    req.setAttribute("msg",msg);
    req.getRequestDispatcher("/WEB-INF/jsp/hello.jsp").forward(req,resp);
}
```

### 2-3.配置web.xml

> 使前台访问可以找到后台代码。

```xml
 <servlet>
     <servlet-name>HelloServlet</servlet-name>
     <servlet-class>com.xiu.HelloServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

### 2-4.编写视图层代码

> 就是jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Kuangshen</title>
</head>
<body>
<h4>进来了！</h4>
${msg}
</body>
</html>
```

### 2-5.配置tomcat

> 配置服务器（容器），运行代码。

### 2-6.注意事项

- 工程的web目录一定中间要有蓝色的圈圈

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529163927983.png" alt="image-20220529163927983" style="zoom:33%;" />

- 配置tomcat一定要注意Application context路径

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164013480.png" alt="image-20220529164013480" style="zoom:33%;" />



**MVC框架要做哪些事情**

1. 将url映射到java类或java类的方法 .
2. 封装用户提交的数据 .
3. 处理请求--调用相关的业务处理--封装响应数据 .
4. 将响应的数据进行渲染 . jsp / html 等表示层数据 .

## 3、Spring MVC理论

> Spring MVC是Spring Framework的一部分，是基于Java实现MVC的轻量级Web框架。
>
> 查看官方文档：https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/web.html#spring-web

### 3-1.为什么要学习SpringMVC?

 Spring MVC的特点：

1. 轻量级，简单易学
2. 高效 , 基于请求响应的MVC框架
3. 与Spring兼容性好，无缝结合
4. 约定优于配置
5. 功能强大：RESTful、数据验证、格式化、本地化、主题等
6. 简洁灵活

Spring的web框架围绕**DispatcherServlet** [ 调度Servlet ] 设计。

DispatcherServlet的作用是将请求分发到不同的处理器。从Spring 2.5开始，使用Java 5或者以上版本的用户可以采用基于注解形式进行开发，十分简洁；

正因为SpringMVC好 , 简单 , 便捷 , 易学 , 天生和Spring无缝集成(使用SpringIoC和Aop) , 使用约定优于配置 . 能够进行简单的junit测试 . 支持Restful风格 .异常处理 , 本地化 , 国际化 , 数据验证 , 类型转换 , 拦截器 等等......所以我们要学习 .

**最重要的一点还是用的人多 , 使用的公司多 .** 

### 3-2.中心控制器

> DispatcherServlet：调度 Servlet
>
> Spring的web框架围绕DispatcherServlet设计。DispatcherServlet的作用是将请求分发到不同的处理器。
>
> 从Spring 2.5开始，使用Java 5或者以上版本的用户可以采用基于注解的controller声明方式。

​	Spring MVC框架像许多其他MVC框架一样, **以请求为驱动** , **围绕一个中心Servlet分派请求及提供其他功能**，**DispatcherServlet是一个实际的Servlet (它继承自HttpServlet 基类)**。

![图片](D:\code\personal\springmvc-study\README.assets\640.png)

SpringMVC的原理如下图所示：

​	当发起请求时被前置的控制器拦截到请求，根据请求参数生成代理请求，找到请求对应的实际控制器，控制器处理请求，创建数据模型，访问数据库，将模型响应给中心控制器，控制器使用模型与视图渲染视图结果，将结果返回给中心控制器，再将结果返回给请求者。

![image-20220529164053363](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164053363.png)

### 3-3.SpringMVC执行原理



![image-20220529164127637](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164127637.png)

**简要分析执行流程**

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求。

   我们假设请求的url为 : http://localhost:8080/SpringMVC/hello

   

   **如上url拆分成三部分：**

   http://localhost:8080服务器域名

   SpringMVC部署在服务器上的web站点

   hello表示控制器

   通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。

2. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。

3. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。

4. HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。

5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。

6. Handler让具体的Controller执行。

7. Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。

8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。

9. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。

10. 视图解析器将解析的逻辑视图名传给DispatcherServlet。

11. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。

12. 最终视图呈现给用户

## 4、Hello SpringMVC程序

> 搭建第一个springMVC程序。

**步骤：**

- 导包
- 编写web.xml和springmvc.xml配置文件
- 编写Controller
- 视图-页面

导包

```xml
 <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-webmvc</artifactId>
     <version>5.1.9.RELEASE</version>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>
```

web.xml

```xml
<servlet>
    <servlet-name>dispatchServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:beans.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>dispatchServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

beans.xml

```xml
<context:component-scan base-package="com.xiu.controller"/>
<mvc:default-servlet-handler/>
<mvc:annotation-driven/>

<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
```

Controller

```java
@RequestMapping("/hello")
public String hello(Model model) {
    model.addAttribute("msg","hello springmvc ,this is only anno type!");
    return "hello";
}
```

页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>登陆页</title>
</head>
<body>
${msg}
<img src="../../images/sun.png">
</body>
</html>
```

### 4-1.常用注解

#### 4-1-1.@RequestMapping

> You can use the `@RequestMapping` annotation to map requests to controllers methods. 
>
> 就是把请求映射到controller方法上。

**There are also HTTP method specific(特定的) shortcut(快捷) variants(变体) of `@RequestMapping`:**

- `@GetMapping`
- `@PostMapping`

- `@PutMapping`
- `@DeleteMapping`
- `@PatchMapping`

#### 4-1-2.@RequestBody & @ResponseBody

> @RequestBody：将请求体(一般为post请求)的数据映射到Object对象上。
>
> @ResponseBody：将返回数据直接返回到请求体中。不去走springmvc的Model或者视图解析器。
>
> 底层由HttpMessageConverter来实现。

```java
// path：访问路径; consumes:消费的数据类型，就是请求数据的类型
// RequestBody：把请求体中的数据映射到Object对象上
@PostMapping(path = "/add",consumes = "application/json")
@ResponseBody
public String add(@RequestBody @Valid Staff staff){
    System.out.println(staff);
    return staff.toString();
}
```



## 5、RestFul风格

> Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。
>
> 基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

**功能：**

> 资源：互联网所有的事物都可以被抽象为资源
>
> 资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。
>
> 分别对应 添加、 删除、修改、查询。

**传统方式操作资源**  ：

> 通过不同的参数来实现不同的效果！方法单一，post 和 get

```properties
http://127.0.0.1/item/queryItem.action?id=1 查询,GET

http://127.0.0.1/item/saveItem.action 新增,POST

http://127.0.0.1/item/updateItem.action 更新,POST

http://127.0.0.1/item/deleteItem.action?id=1 删除,GET或POST
```

**使用RESTful操作资源** ：

> 可以通过不同的请求方式来实现不同的效果！如下：请求地址一样，但是功能可以不同！

```properties
http://127.0.0.1/item/1 查询,GET

http://127.0.0.1/item 新增,POST

http://127.0.0.1/item 更新,PUT

http://127.0.0.1/item/1 删除,DELETE
```

**使用：**

```java
@GetMapping("plus/{a}/{b}")
public String plusNum(@PathVariable int a,@PathVariable int b,Model model) {
    int result = a + b;
    model.addAttribute("result",result);
    return "plus";
}
```

**请求：**

```properties
http://localhost:8080/plus/100/99
```

**优点：**

- 使路径变得更加简洁；

- 获得参数更加方便，框架会自动进行类型转换。

- 提升安全性

- 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，如这里访问是的路径是/commit/1/a，则路径与方法不匹配，而不会是参数转换失败。

  

## 6、转发与重定向

### 6-1.转发

>   请求转发，是一种服务器行为，当客户端请求到达后，服务器进行转发，此时会将请求对象进行保存，地址栏中的URL地址不会改变，得到响应后，服务器端再将响应发送给客户端，这个过程只有一次请求发出。

- 特点：

1. 服务器只能在当前内部资源进行跳转

2. 浏览器地址栏不变

3. 转发只有一次请求

4. request数据可以共享

   通过request对象可以在一次请求中传递数据，经过请求转发，request域中的数据依然存在，可以通过request传输和共享数据。

   ```java
   // 设置域对象内容
   request.setAttribute(String name, String value)
   // 获取域对象内容
   request.getAttribute(String name)
   // 删除域对象内容
   request.removeAttribute(String name)
   ```

### 6-2.重定向

> 重定向是服务器指导客户端的行为，客户端发出第一个请求，被服务器接收处理后，服务器会作出响应，
>
> 在响应的同时，服务器会给客户端一个新的资源地址（就是下次请求的地址），
>
> 当客户端拿到接受到响应后，会马上根据新地址发起第二个请求，
>
> 服务器接收请求并作出响应，重定向完成。

### 6-3.总结

请求转发与重定向的比较 ：

|      请求转发forward       |      重定向redirect      |
| :------------------------: | :----------------------: |
|          一次请求          |         两次请求         |
|   数据在request域中共享    |  request域中数据不共享   |
|     浏览器地址栏不改变     |     浏览器地址栏改变     |
| 只能访问当前服务器下的资源 | 可以访问其他服务器的资源 |
|         服务器行为         |        客户端行为        |

## 7、中文乱码问题

> springmvc对中文支持不好，会出现中文乱码问题。

**解决方案：**

- 方案一：修改tomcat配置

   ```xml
   <Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
             connectionTimeout="20000"
             redirectPort="8443" />
   ```

- 方案二：配置SpringMVC提供的过滤器

```xml
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <!-- 
		/*:表示静态资源也会被拦截，如jsp等
	 	/ :表示只拦截请求，不包括静态资源。
	-->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

- 方案三：自己编写过滤器

```java
package com.xiu.filter;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

/**
* 解决get和post请求 全部乱码的过滤器
*/
public class GenericEncodingFilter implements Filter {

   @Override
   public void destroy() {
  }

   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
       //处理response的字符编码
       HttpServletResponse myResponse=(HttpServletResponse) response;
       myResponse.setContentType("text/html;charset=UTF-8");

       // 转型为与协议相关对象
       HttpServletRequest httpServletRequest = (HttpServletRequest) request;
       // 对request包装增强
       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
       chain.doFilter(myrequest, response);
  }

   @Override
   public void init(FilterConfig filterConfig) throws ServletException {
  }

}

//自定义request对象，HttpServletRequest的包装类
class MyRequest extends HttpServletRequestWrapper {

   private HttpServletRequest request;
   //是否编码的标记
   private boolean hasEncode;
   //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
   public MyRequest(HttpServletRequest request) {
       super(request);// super必须写
       this.request = request;
  }

   // 对需要增强方法 进行覆盖
   @Override
   public Map getParameterMap() {
       // 先获得请求方式
       String method = request.getMethod();
       if (method.equalsIgnoreCase("post")) {
           // post请求
           try {
               // 处理post乱码
               request.setCharacterEncoding("utf-8");
               return request.getParameterMap();
          } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
          }
      } else if (method.equalsIgnoreCase("get")) {
           // get请求
           Map<String, String[]> parameterMap = request.getParameterMap();
           if (!hasEncode) { // 确保get手动编码逻辑只运行一次
               for (String parameterName : parameterMap.keySet()) {
                   String[] values = parameterMap.get(parameterName);
                   if (values != null) {
                       for (int i = 0; i < values.length; i++) {
                           try {
                               // 处理get乱码
                               values[i] = new String(values[i]
                                      .getBytes("ISO-8859-1"), "utf-8");
                          } catch (UnsupportedEncodingException e) {
                               e.printStackTrace();
                          }
                      }
                  }
              }
               hasEncode = true;
          }
           return parameterMap;
      }
       return super.getParameterMap();
  }

   //取一个值
   @Override
   public String getParameter(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       if (values == null) {
           return null;
      }
       return values[0]; // 取回参数的第一个值
  }

   //取所有值
   @Override
   public String[] getParameterValues(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       return values;
  }
}
```

## 8、JSON



## 9、SSM整合



