# Filter

---

## 1、过滤器简介

> 过滤器，顾名思义就是起到过滤筛选作用的一种事物，只不过相较于现实生活中的过滤器，这里的过滤器过滤的对象是客户端访问的web资源，也可以理解为一种预处理手段，对资源进行拦截后，将其中我们认为的杂质（用户自己定义的）过滤，符合条件的放行，不符合的则拦截下来

![image-20220904172839348](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220904172839348.png)



![image-20220904172757655](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220904172757655.png)

## 2、过滤器使用场景

> - 统一设置编码
> - 过滤敏感字符
> - 登录校验
> - URL级别的访问权限控制
> - 数据压缩

### 2-1、过滤敏感字符

```java

public class BadLanguageFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        String word = servletRequest.getParameter("word");
        System.out.println("word is -->"+word);
        // 获取request
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        // 获取response
        HttpServletResponse response = (HttpServletResponse) servletResponse;
        DirtyWordsHttpServletRequest dirtyWordsHttpServletRequest = new DirtyWordsHttpServletRequest(request);

        filterChain.doFilter(dirtyWordsHttpServletRequest,response);
    }


    /**
     * 内部类脏话过滤规则
      */
    class DirtyWordsHttpServletRequest extends HttpServletRequestWrapper {
        // 脏话字典、可以直接搜索脏话字典，然后通过io流进行读取和过滤
        private String[] words = {"傻", "禽", "畜","操","尼玛","逼"};
        // 构造方法
        public DirtyWordsHttpServletRequest(HttpServletRequest request) {
            super(request);
        }
        // 重写getParameter方法
        @Override
        public String getParameter(String name) {
            // 获取传来的参数值
            String value = super.getParameter(name);
            // 判断
            if(value == null) {
                return "没有值";
            }
            // 执行脏话转换
            for (String dword : words) {
                if(value.contains(dword)) {
                    value = value.replace(dword, "**");
                }
            }
            // 返回过滤有的值
            return value;
        }
    }
}
```

```java
@Configuration
public class WebConfigure {

    @Bean
    public FilterRegistrationBean registFilter() {
        FilterRegistrationBean filterBean = new FilterRegistrationBean();
        filterBean.setFilter(new BadLanguageFilter());
        filterBean.addUrlPatterns("/*");
        filterBean.setName("BadLanguageFilter");
        filterBean.setOrder(1);
        return filterBean;
    }
}
```

```java
@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello(HttpServletRequest request) {
        System.out.println(request.getParameter("word"));
        return request.getParameter("word");
    }
}
```

![image-20220904173319441](C:\Users\97146\AppData\Roaming\Typora\typora-user-images\image-20220904173319441.png)



**如果有多个过滤器呢？**

```java
public class SecondFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("恭喜您进入了第二个过滤器。。。");
        filterChain.doFilter(servletRequest,servletResponse);
    }
}
```

```java
@Configuration
public class WebConfigure {

    @Bean
    public FilterRegistrationBean registFilter() {
        FilterRegistrationBean filterBean = new FilterRegistrationBean();
        filterBean.setFilter(new BadLanguageFilter());
        filterBean.addUrlPatterns("/*");
        filterBean.setName("BadLanguageFilter");
        filterBean.setOrder(2);
        return filterBean;
    }

    @Bean
    public FilterRegistrationBean registFilter2() {
        FilterRegistrationBean filterBean = new FilterRegistrationBean();
        filterBean.setFilter(new SecondFilter());
        filterBean.setName("SecondFilter");
        filterBean.addUrlPatterns("/*");
        filterBean.setOrder(1);
        return filterBean;

    }
}
```

![image-20220904173431837](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220904173431837.png)