# Swagger

---

## 1、简介

> Swagger 既是一款接口的文档在线自动生成软件，也是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。
>
> 国内绝大部分人还在用过时的swagger2（17年停止维护并更名为swagger3）
>
> Swagger 为我们提供了一套通过代码和注解自动生成文档的方法，这一点对于保证API 文档的及时性将有很大的帮助。
>
> 接口信息与文档同步。也就是说我代码变更文档会同步我的变更。

## 2、入门

### 2-1.导入依赖

> swagger2： springboot version lower than 2.5.0
>
> swagger3： 也别太高，2.7.0不能成功。
>
> - springfox-swagger2：这个组件的功能用于帮助我们自动生成描述API的json文件
> - springfox-swagger-ui：就是将描述API的json文件解析出来，用一种更友好的方式呈现出来。

```xml
swagger3
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>

swagger2
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.7.0</version>
</dependency>
```

### 2-2.配置文件

```java
@Configuration
@EnableSwagger2
public class Swagger2Config {
    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //为当前包下controller生成API文档
                .apis(RequestHandlerSelectors.basePackage("com.xiu.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("配置SwaggerUI")
                .description("Swagger-UI-Demo")
                .version("1.0")
                .build();
    }
}
```

### 2-3.Controller代码

```java
@RestController
@Api(value = "用户控制器", tags = {"用户操作"})
public class UserController {
    @GetMapping("/hello")
    @ApiOperation("打招呼")
    public String hello(){
        return "你好，我是小娜";
    }

    @PostMapping("/login")
    @ApiOperation("登录")
    public String login(@RequestBody @ApiParam("用户对象") User user){
        return "你好" + user.toString();
    }
}
```

### 2-4.访问

> http://localhost:8080/swagger-ui/index.html

![image-20220820231255883](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220820231255883.png)

## 3、Swagger注解

```java
@Api：用在请求的类上，表示对类的说明
    tags="说明该类的作用，可以在UI界面上看到的注解"
    value="该参数没什么意义，在UI界面上也看到，所以不需要配置"
​
@ApiOperation：用在请求的方法上，说明方法的用途、作用
    value="说明方法的用途、作用"
    notes="方法的备注说明"
​
@ApiParam：用于方法，参数，字段说明，表示对参数的添加元数据（说明或是否必填等） 
    name:属性名称
    value：属性值
    defaultValue：默认属性值
    required：是否属性必填
    example：举例子
​
@ApiImplicitParams：用在请求的方法上，表示一组参数说明
    @ApiImplicitParam：用在@ApiImplicitParams注解中，指定一个请求参数的各个方面
        name：参数名
        value：参数的汉字说明、解释
        required：参数是否必须传
        paramType：参数放在哪个地方
            · header --> 请求参数的获取：@RequestHeader
            · query --> 请求参数的获取：@RequestParam
            · path（用于restful接口）--> 请求参数的获取：@PathVariable
            · body（不常用）
            · form（不常用）    
        dataType：参数类型，默认String，其它值dataType="Integer"       
        defaultValue：参数的默认值
​
@ApiResponses：用在请求的方法上，表示一组响应
    @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
        code：数字，例如400
        message：信息，例如"请求参数没填好"
        response：抛出异常的类
​
@ApiModel：用于响应类上，表示一个返回响应数据的信息
            （这种一般用在post创建的时候，使用@RequestBody这样的场景，
            请求参数无法使用@ApiImplicitParam注解进行描述的时候）
    @ApiModelProperty：用在属性上，描述响应类的属性
```

