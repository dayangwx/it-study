# Mybatis

---

## 1、简介

> MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164652292.png" alt="image-20220529164652292" style="zoom:33%;" />

### 1-1.持久化

> 持久化：内存断电即失！将数据持久化到硬盘上

### 1-2.持久层

> 分层使的代码更具有逻辑性
>
> 使编码的思路更加清晰，每个层有每个层自己的作用。
>
> 并且日后也好维护。

### 1-3.为什么需要mybatis

> mybatis的作用就是与数据库进行数据交换。
>
> 说白了就是对数据库进行增删改查。

优点：

- 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件。易于学习，易于使用。
- 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
- 解除sql与程序代码的耦合：sql和代码的分离，提高了可维护性。
- 提供映射标签，支持对象与数据库的orm字段关系映射。
- 提供对象关系映射标签，支持对象关系组建维护。
- 提供xml标签，支持编写动态

## 2、入门

> 练一练即可，
>
> 多看官网
>
> https://mybatis.org/mybatis-3/zh/index.html

步骤：

- 导包
- 编写配置及代码
- 测试

具体过程，可以查看代码，此处就部赘述了。



注：like

```xml
and REGION_NAME like '%'||#{regionName}||'%'
或者
and REGION_NAME like concat('%,', #{regionName}, '%') 这种比较好符合sql规范。
```

## 3、核心配置

> mybatis-config.xml文件内容的讲解

### 3-1.环境配置（environments）

> MyBatis 可以配置成适应多种环境，、
>
> **不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

```xml
<!-- 可以有多套环境，但是sqlSessionFactory只能指定一套。default="" 用于指定-->
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
    <environment id="test">
        <!-- 事务 -->
        <transactionManager type="JDBC"/>
        <!-- 数据源-->
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/myemployees?useUnicode=true&amp;characterEncoding=utf-8"/>
            <property name="username" value="root"/>
            <property name="password" value="root"/>
        </dataSource>
    </environment>
</environments>
```

注意一些关键点:

- 默认使用的环境 ID（比如：default="development"）。
- 每个 environment 元素定义的环境 ID（比如：id="development"）。
- 事务管理器的配置（比如：type="JDBC"）。
- 数据源的配置（比如：type="POOLED"）。

### 3-2.属性（properties）

> 数据源配置的数据。
>
> jdbc.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/myemployees?useSSL=false&useUnicode=true&characterEncoding=utf-8
username=root
password=root
```

```xml
<properties resource="jdbc.properties"></properties>

<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

### 3-3.类型别名（typeAliases）

> 可以为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
</typeAliases>
```

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

```java
@Alias("author")
public class Author {
    ...
}
```

### 3-4.设置（settings）

> 这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。
>
> 官网连接：
>
> https://mybatis.org/mybatis-3/zh/configuration.html#:~:text=db%3Ausername%3F%3Aut_user%7D%22/%3E%0A%3C/dataSource%3E-,%E8%AE%BE%E7%BD%AE%EF%BC%88settings%EF%BC%89,-%E8%BF%99%E6%98%AF%20MyBatis

```xml
<settings>
    <!-- 不使用缓存 -->
    <setting name="cacheEnabled" value="false"/>
    <!-- 
		当数据value的值为null时，是否像map或者对象的setter中设置值。true：设置，false：不设置。
 		如：map.set("name",null)	
		存疑：似乎并没有什么作用。
	-->
    <setting name="callSettersOnNulls" value="true"/>
</settings>
```

### 3-5.映射器（mappers）

> 就是告诉 MyBatis 到哪里去找sql语句。
>
> 
>
> **注意：**
>
>   **方式二和方式三必须要满足两点：**
>
> ​    **1：xml名称与接口名称一致。 StaffMapper.xml StaffMapper.java**
>
> ​	**2:xml与接口必须在同一个包路径下**

```xml
<mappers>
    <!-- 方式一 -->
    <!-- resource路径，直接找到就可以了。这个路径一样就行，你可以把xml写到resources下，只要包路径相同即可 -->
    <mapper resource="com/xiu/mapper/StaffMapper.xml"/>
    
    <!-- 下面这两种一种是根据class找，一种是根据包路径找 -->
    <!-- 方式二 -->
    <mapper class="com.xiu.mapper.*.xml"></mapper>
    <!-- 方式三 -->
    <package name="com.xiu.mapper"/>
</mappers>
```

### 3-6.生命周期和作用域

> sqlSessionFactoryBuilder
>
> sqlSessionFactory
>
> sqlSession

#### 3-6-1.sqlSessionFactoryBuilder

> 一旦创建了 SqlSessionFactory，就不再需要它了
>
> 也就是说它是个局部变量
>
> 它的作用是创建sqlSessionFactory，而sqlSessionFactory在一个项目中一般只需要一个，
>
> 所以说创建完sqlSessionFactory后就不需要它了。

#### 3-6-2.SqlSessionFactory

> SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。
>
> 它的作用是获得sqlSession，一个项目中需要多次获得sqlSession，所以说它要一直存在，并且应该是单例的。 

#### 3-6-3.SqlSession

> 每个线程都应该有它自己的 SqlSession 实例。
>
> 换句话说，每次收到 HTTP 请求，就可以打开一个 SqlSession，返回一个响应后，就关闭它。

## 4、结果映射

> `resultMap` 元素是 MyBatis 中最重要最强大的元素

### 4-1.基本使用

```xml
注意：如果property和column一样，可以不用写，会自动把数据返回的 。
     但是，实际生产中还是建议写上，有助于日后维护!
<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>
```

### 4-2.多对一

> 使用associate  javaType
>
> 详细使用可参考代码或官网，此处不再赘述。

```xml
<resultMap id="emp" type="empInfo">
    <id property="id" column="eid"></id>
    <result property="age" column="age"></result>
    <result property="name" column="name"></result>
    <association property="deptInfo" javaType="deptInfo">
        <id property="id" column="did"></id>
        <result property="deptName" column="deptName"></result>
        <result property="address" column="address"></result>
        <result property="ceo" column="ceo"></result>
    </association>
</resultMap>
```

### 4-3.一对多

> 使用collection  ofType
>
> 详细使用可参考代码或官网，此处不再赘述。

```xml
<resultMap id="staff" type="staffInfo">
    <id property="id" column="sid"></id>
    <result property="name" column="name"></result>
    <result property="password" column="password"></result>
    <collection property="positionList" ofType="position">
        <id property="id" column="pid"></id>
        <result property="positionName" column="position_Name"></result>
        <result property="staffId" column="staff_id"></result>
    </collection>
</resultMap>
```



## 5、日志

> mybatis支持多种日志。如： SLF4J | LOG4J | LOG4J2 |JDK_LOGGING|COMMONS_LOGGING|STDOUT_LOGGING|NO_LOGGING
>
> 可在settings中进行配置。

### 5-1.LOG4J2

> 这里着重讲解LOG4J2，因为是使用最广的。
>
> 后续会出一个LOG4J2的笔记。
>
> 以下只提供基本使用。

三步走：

- 导包
- 配置文件
- 使用

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.17.2</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.17.2</version>
</dependency>
```

log4j2.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Root level="info">
            <AppenderRef ref="Console"/>
        </Root>
        <Logger name="com.xiu.mapper" level="TRACE" additivity="false">       //为dao mapper所在的包，level为TRACE
            <AppenderRef ref="Console"/>
        </Logger>
    </Loggers>
</Configuration>
```

mybatis-config.xml

```xml
<settings>
    <setting name="logImpl" value="LOG4J2"/>
</settings>
```

```java
18:59:22.151 [main] DEBUG com.xiu.mapper.StaffMapper.queryStaff - ==>  Preparing: select * from Staff order by id
18:59:22.176 [main] DEBUG com.xiu.mapper.StaffMapper.queryStaff - ==> Parameters: 
18:59:22.186 [main] TRACE com.xiu.mapper.StaffMapper.queryStaff - <==    Columns: id, name, password
18:59:22.186 [main] TRACE com.xiu.mapper.StaffMapper.queryStaff - <==        Row: 1, 刘文秀, 123456
18:59:22.188 [main] TRACE com.xiu.mapper.StaffMapper.queryStaff - <==        Row: 2, 技术部, 北京
18:59:22.188 [main] TRACE com.xiu.mapper.StaffMapper.queryStaff - <==        Row: 3, 财政部, null
18:59:22.188 [main] TRACE com.xiu.mapper.StaffMapper.queryStaff - <==        Row: 5, 罗技, luoji
18:59:22.189 [main] DEBUG com.xiu.mapper.StaffMapper.queryStaff - <==      Total: 4
```

## 6、分页插件

> 在日常开发中，避免不了需要分页。
>
> 这里使用分页插件：pageHelper
>
> 官网：https://pagehelper.github.io/docs/howtouse/

**步骤：**

- 导包
- 配置
- 使用

导包

```xml
<dependencies>
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>5.3.0</version>
    </dependency>
</dependencies>
```

配置

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

使用

​	方式一：

```java
// 不需要在xml中处理，跟在PageHelper.startPage()后的第一条查询语句会默认分页。（第二条就不会帮你分页了!）
@Test
public void test4() {
    SqlSession sqlSession = MybatisUtil.getSqlSession();
    AccountMapper mapper = sqlSession.getMapper(AccountMapper.class);
    PageHelper.startPage(2, 5);
    List<Account> accounts = mapper.listAccountByPage();
    PageInfo pageInfo=new PageInfo(accounts);
    System.out.println(pageInfo);
}
```

​	方式二：推荐使用这种方式！

```java
/**
 * 使用 RowBounds分页，这种方式侵入性最小，推荐！
*/
@Test
public void test6() {
    SqlSession sqlSession = MybatisUtil.getSqlSession();
    AccountMapper mapper = sqlSession.getMapper(AccountMapper.class);
    int pageNum = 5;
    int pageSize = 10;
    int offset = (pageNum-1) * pageSize;
    RowBounds rowbouds = new RowBounds(offset,pageSize);
    List<Account> accounts1 = mapper.selectListByPage(rowbouds);
    PageInfo pageInfo = new PageInfo(accounts1);
    System.out.println(pageInfo);
}
```

注：

  官网中还有一些其他的方式，这里只罗列几种，剩余的可以自己查看！！

## 7、动态sql

> 解决sql拼接问题！
>
> 详情请查阅官网！

### 7-1.if



### 7-2.choose、when、otherwise



### 7-3.trim、where、set



### 7-4.foreach



### 7-5.script

> 要在带注解的映射器接口类中使用动态 SQL，可以使用 *script* 元素

## 8.缓存

> 缓存的作用就是**减少数据库的交互次数，减少系统开销。**
>
> **经常查询并且不经常改变的数据适合缓存。**
>
> 在高并发的时候很有用。

### 8-1.Mybatis缓存

> MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

**MyBatis系统中默认定义了两级缓存:一级缓存和二级缓存：**

- 默认情况下，只有一级缓存开启。(SqlSession级别的缓存，也称为本地缓存）
- 二级缓存需要手动开启和配置，它是基于namespace级别的缓存。
- 为了提高扩展性，Mybatis定义了缓存接口Cache，我们可以通过实现Cache接口来自定义二级缓存。

#### 8-1-1.一级缓存

> mybatis默认使用的就是一级缓存。

- 与数据库同一次会话期间查询到的数据会放在本地缓存中。（其实就是获取sqlSession到sqlSession.close()这段之间）
- 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库;

**缓存失效：**

- 查询不同的数据。参数不一样了肯定是要去数据库中重新查的。
- 增删改操作，缓存会失效。
- 查询不同的mapper，缓存会失效。
- 调用sqlSession.clearCache()，缓存会失效。

#### 8-1-2.二级缓存

> 如果sqlSession在close的时候，我们不想让一级缓存销毁，在后续的查询中继续使用，
>
> 这种情况，我们可以把一级缓存保存到二级缓存里。
>
> 二级缓存是namespace级别的，也就是*mapper.xml级别的。

**使用：**

- 开启 其实默认是开启的（只是这个配置，并不是说mybatis默认开启二级缓存，mybatis默认开启的是一级缓存），这里写的作用只是为了后面维护，分析方便

```xml
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```

- 在xml里配置cache即可。

```xml
<cache></cache>
```

当然这种情况下是需要让pojo实现Serialize接口的。

因为，缓存底层是通过IO的，IO又是通过字节流来完成的，所以要把对象序列化。

#### 8-1-3:缓存执行过程图

![image-20220529164950129](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529164950129.png)

#### 8-1-4.序列化(拓展)

##### 什么是序列化？

> 序列化是将对象状态转换为可保持或传输的格式的过程。
>
> 与序列化相对的是反序列化，它将流转换为对象。这两个过程结合起来，可以轻松地存储和传输数据。

##### 为什么要序列化对象

> 把对象转换为字节序列的过程称为对象的序列化。
>
> 把字节序列恢复为对象的过程称为对象的反序列化。
>
> 序列化对于面向对象的编程语言来说是非常重要的，因为无论什么编程语言，其底层涉及IO操作的部分还是由操作系统其帮其完成的，而底层IO操作都是以字节流的方式进行的，所以写操作都涉及将编程语言数据类型转换为字节流，而读操作则又涉及将字节流转化为编程语言类型的特定数据类型。

##### 什么情况下需要序列化？

> 当我们需要把对象的状态信息通过网络进行传输，或者需要将对象的状态信息持久化，以便将来使用时都需要把对象进行序列化。

那为什么还要继承Serializable。那是存储对象在存储介质中，以便在下次使用的时候，可以很快捷的重建一个副本。

或许你会问，*我在开发过程中，实体并没有实现序列化，但我同样可以将数据保存到mysql、Oracle数据库中，为什么非要序列化才能存储呢？*

我们来看看Serializable到底是什么，跟进去看一下，我们发现Serializable接口里面竟然什么都没有，只是个空接口

```java
public interface Serializable {
}
```

**一个接口里面什么内容都没有，我们可以将它理解成一个标识接口。**

***比如在课堂上有位学生遇到一个问题，于是举手向老师请教，这时老师帮他解答，那么这位学生的举手其实就是一个标识，自己解决不了问题请教老师帮忙解决。在Java中的这个Serializable接口其实是给jvm看的，通知jvm，我不对这个类做序列化了，你(jvm)帮我序列化就好了。***

##### 关于serialVersionUID

> 对于JVM来说，要进行持久化的类必须要有一个标记，只有持有这个标记JVM才允许类创建的对象可以通过其IO系统转换为字节数据，从而实现持久化，而这个标记就是Serializable接口。而在反序列化的过程中则需要使用serialVersionUID来确定由那个类来加载这个对象，所以我们在实现Serializable接口的时候，一般还会要去尽量显示地定义serialVersionUID。

这个serialVersionUID的详细的工作机制是：在序列化的时候系统将serialVersionUID写入到序列化的文件中去，*当反序列化的时候系统会先去检测文件中的serialVersionUID是否跟当前的文件的serialVersionUID是否一致*，如果一直反序列化不成功，就说明当前类跟序列化后的类发生了变化，比如是成员变量的数量或者是类型发生了变化，那么在反序列化时就会发生crash，并且回报出错误：

```java
java.io.InvalidClassException: User; local class incompatible: stream classdesc serialVersionUID = -1451587475819212328, local class serialVersionUID = -3946714849072033140at 
java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)at 
java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)at 
java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)at
java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)at 
java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)at 
java.io.ObjectInputStream.readObject(ObjectInputStream.java:431)at 
Main.readUser(Main.java:32)at Main.main(Main.java:10)
```

刚开始提到了，serialVersionUID要不要指定呢？如果不指定会出现什么样的后果？如果指定了以后后边的值又代表着什么意思呢？既然系统指定了这个字段，那么肯定是有它的作用的。

如果我们在序列化中没有显示地声明serialVersionUID，则序列化运行时将会根据该类的各个方面计算该类默认的serialVersionUID值。但是，Java官方强烈建议所有要序列化的类都显示地声明serialVersionUID字段，因为如果高度依赖于JVM默认生成serialVersionUID，可能会导致其与编译器的实现细节耦合，这样可能会导致在反序列化的过程中发生意外的InvalidClassException异常。因此，为了保证跨不同Java编译器实现的serialVersionUID值的一致，实现Serializable接口的必须显示地声明serialVersionUID字段。

*此外serialVersionUID字段地声明要尽可能使用private关键字修饰*，这是因为该字段的声明只适用于声明的类，该字段作为成员变量被子类继承是没有用处的！有个特殊的地方需要注意的是，数组类是不能显示地声明serialVersionUID的，因为它们始终具有默认计算的值，不过数组类反序列化过程中也是放弃了匹配serialVersionUID值的要求。

## 9、Mybatis整合Spring



## 10、Mybatis整合SpringBoot

