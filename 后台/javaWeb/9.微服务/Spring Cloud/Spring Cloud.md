# Spring Cloud

---

## 1、Spring Cloud概述

> **Spring Cloud就是为了简化分布式微服务架构的开发。**
>
> 首先我们把all in one的服务按照一定规则划分成一个个的微服务(主要用springboot实现)，
>
> 这些微服务可以部署到不同的服务器上，那么问题也就接踵而来：
>
> - 这么多服务，客户端该怎么访问？
> - 这么多服务，服务直接如何通信？
> - 这么多服务，服务如何治理？
> - 这么多服务，万一挂了怎么办？
>
> 基于以上的问题，市面上主流的有三种解决方案。

### 1-1.三种解决方案

| 方案名称                               | 客户如何访问       | 服务间如何通信    | 服务如何治理 | 服务挂了怎么办 |
| -------------------------------------- | ------------------ | ----------------- | ------------ | -------------- |
| Spring Cloud Netflix(一站式解决)       | Zuul 网关          | Feign + Eureka    |              | HyStrix        |
| Apache Dubbo Zookeeper(半站)           | 没有，找第三方组件 | Dubbo + Zookeeper |              | 借助HyStrix    |
| Spring Cloud Alibaba(一站式，最近出现) |                    |                   |              |                |

微服务技术栈：

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220911153239181.png" alt="image-20220911153239181" style="zoom:50%;" />

万变不离其宗：

1. API
2. HTTP,RPC
3. 注册和发现
4. 熔断机制

### 1-2.Spring Boot与Spring Cloud的关系

> Spring Boot与spring Cloud是一种渐进性关系。
>
> Spring Boot专注于快速、便捷的开发单个个体微服务，Spring Cloud关注全局的服务治理。
>
> Spring Cloud的管理的是Spring Boot开发出来的一个个单体微服务。
>
> Spring Boot可以离开Spring Cloud独立使用，但是Spring Cloud不能离开Spring Boot。



## 2、Eureka

> 服务注册与发现