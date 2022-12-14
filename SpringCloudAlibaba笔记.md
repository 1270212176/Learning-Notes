# SpringCloudAlibaba



## Nacos



### 1 Nacos服务发现

**依赖**



父工程引入SpringCloud和SpringCloudAlibaba依赖

```
 
 <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
 <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.2.1.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

```



子工程引入nacos依赖

```
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```



nacos配置文件（默认端口就是8848，不配也可以找到）

```
spring:
  cloud:
    nacos:
      discovery:
        # 指定nacos server地址
        server-addr: localhost:8848
```



### 2 Nacos服务分级存储模型



**服务--集群--实例**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos分级存储.png)



**服务跨集群调用问题**

服务调用尽可能选择本地集群的服务，跨集群调用延迟较高。本地集群不可访问时，再去访问其他集群



nacos集群配置

```
spring:
  cloud:
    nacos:
      discovery:
        # 指定nacos server地址
        server-addr: localhost:8848
        cluster-name: XM #集群名称，自定义，XM代指厦门
```



**总结**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos分级存储与集群总结.png)

### 3 NacosRule负载均衡



在yml文件中设置负载均衡

**注意：Ribbon的负载均衡默认采用轮询算法**



//此配置表示采用随机算法，my-nacos为你要调用的服务名

```
my-nacos:
  ribbon:
    NFLoadBalancerRuleClassName: com.cyxy.config.NacosWeightedRule
```



//基于权重，需要在服务中手动添加权重配置类

```
my-nacos:
  ribbon:
    NFLoadBalancerRuleClassName: com.cyxy.config.NacosWeightedRule
```



权重配置类

```
import com.alibaba.cloud.nacos.NacosDiscoveryProperties;
import com.alibaba.cloud.nacos.ribbon.NacosServer;
import com.alibaba.nacos.api.exception.NacosException;
import com.alibaba.nacos.api.naming.NamingService;
import com.alibaba.nacos.api.naming.pojo.Instance;
import com.netflix.client.config.IClientConfig;
import com.netflix.loadbalancer.AbstractLoadBalancerRule;
import com.netflix.loadbalancer.BaseLoadBalancer;
import com.netflix.loadbalancer.ILoadBalancer;
import com.netflix.loadbalancer.Server;
import lombok.extern.slf4j.Slf4j;

import javax.annotation.Resource;

@Slf4j
public class NacosWeightedRule extends AbstractLoadBalancerRule {

    //Ribbon基于权重的算法

    @Resource
    private NacosDiscoveryProperties nacosDiscoveryProperties;

    @Override
    public void initWithNiwsConfig(IClientConfig iClientConfig) {
        //读取配置文件
    }

    @Override
    public Server choose(Object o) {
        ILoadBalancer loadBalancer = this.getLoadBalancer();
        BaseLoadBalancer baseLoadBalancer = (BaseLoadBalancer) loadBalancer;
        //获取要请求的微服务名称
        String name = baseLoadBalancer.getName();
        //获取服务发现的相关API
        NamingService namingService = nacosDiscoveryProperties.namingServiceInstance();
        try {
            Instance instance = namingService.selectOneHealthyInstance(name);
            log.info("选择的实例是port={},instance={}",instance.getPort(),instance);
            return new NacosServer(instance);
        } catch (NacosException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```





//NacosRule-----优先选择本地集群，在本地集群的多个服务中采用随机的负载均衡算法

```
my-nacos:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule
```



**注意：本地集群如果没有服务则会跨集群进行访问，同时会有警告信息出现**



总结

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacosRule负载均衡规则.png)

### 4 Nacos服务实例的权重设置



nacos提供了权重配置来控制访问频率，权重越大则访问频率越高,权重设置为0则不会被访问

设置权重方式

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos设置权重.png)



### 5 Nacos环境隔离

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos环境隔离.png)





在nacos控制台进行配置



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos配置命名空间.png)



在yml中的namespace配置命名空间的id

```
spring:
  cloud:
    nacos:
      discovery:
        # 指定nacos server地址
        server-addr: localhost:8848
        cluster-name: XM #集群名称，自定义，XM代指厦门
        namespace: 492a71383-dhjds7864e-adw2 #配置命名空间的id，此id为新建命名空间时nacos控制台自动生成的
```



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos环境隔离总结.png)

**注意：不同命名空间下的服务是不可以调用的**



### 6 Nacos的临时实例与非临时实例

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos的临时实例与非临时实例.png)



```
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false #是否是临时实例
```

**临时实例down掉后，会被nacos删除，非临时实例则会等待它恢复**



### 7 Nacos与Eureka的区别

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos与eureka区别.png)



### 8 Nacos实现配置管理

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos配置管理.png)

在nacos终端中添加配置文件

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos配置文件管理1.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos配置文件2.png)

**注意配置文件中的内容应该是经常有需求变化的内容**



SpringBoot中读取统一配置文件

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\读取配置文件.png)



引入客户端依赖

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\配置管理依赖.png)

配置bootstrap.yaml

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\bootstrap.yaml.png)

```
spring:
  application:
    name: my-nacos #服务名称
  profiles:
    active: dev #开发环境，这里是dev
  cloud:
    nacos:
      server-addr: localhost:8848 #nacos地址
      config:
        file-extension: yaml #文件后缀名
```



此时的application.yml文件中就可以将nacos地址和服务名称删除

```
spring:
  cloud:
    nacos:
      discovery:
        # 指定nacos server地址
#        server-addr: localhost:8848
        cluster-name: XM #集群名称，自定义，XM代指厦门
        namespace: 492a71383-dhjds7864e-adw2 #配置命名空间的id，此id为新建命名空间时nacos控制台自动生成的
        ephemeral: false #是否是临时实例
#  application:
#    name: my-nacos
server:
  port: 8081

```



拉取配置文件测试

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\拉取配置文件测试.png)



### 9 Nacos配置热更新(配置自动更新)



两种方式实现



**方式1**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\配置热更新1.png)



**方式2**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\配置热更新2.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\配置文件总结.png)

### 10 Nacos 多环境配置共享



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\多环境配置共享思路.png)

如果本地配置文件与nacos客户端的两个配置文件有相同配置时，优先级为

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\多环境配置优先级.png)



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\多环境配置相关知识.png)

11 Nacos集群配置

看Nacos集群搭建配置文档配合黑马视频使用。

黑马视频地址

```
https://www.bilibili.com/video/BV1LQ4y127n4?p=29&vd_source=21e0104c53b895468585eecd2498df15
```



## Gateway



### 1 为什么需要网关



**网关功能：**

**身份认证和权限校验**

**服务路由和负载均衡**

**请求限流**



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\网关的功能.png)



SpringCloud实现网关的两种方式：

Gateway

Zuul



**为什么选择gateway**

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\为什么选择gateway.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\网关作用.png)

### 2 搭建网关服务



**gateway依赖和nacos依赖**

```
  <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```



路由的yml

```
server:
  port: 9999
spring:
  application:
    name: gateway-service
  cloud:
    nacos:
      server-addr: localhost:8848
    gateway:
      routes:
        - id: user-service # 路由标识，必须唯一，自定义
          uri: lb://userservice # lb表示负载均衡，userservice为服务名称，路由到目标地址
          predicates: #路由断言，判断请求是否符合要求
            -Path=/user/** #路径断言，判断路径是否以user开头
        - id: order-service
          uri: lb://orderservice
          predicates:
            -Path=/order/**
```



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\网关的yml.png)



网关映射过程

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\网关的具体流程.png)

### 3 路由断言工厂



```
  predicates: #路由断言，判断请求是否符合要求
            -Path=/user/** #路径断言，判断路径是否以user开头
```



配置文件中断言只是简单的字符串，需要到断言工厂中进一步转换

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\Spring提供的11种断言工厂.png)



**注意：断言条件可以有多个，并且条件间是逻辑且的关系**



PredicateFactory的作用是什么？

读取用户定义的断言条件，对请求做出判断。



### 4 路由的过滤器配置（网关过滤器）



GatewayFilter是网关提供的一种过滤器，可以对进入网关的请求和微服务返回的响应做处理



在yml中给userservice的路由添加过滤器

```
server:
  port: 9999
spring:
  application:
    name: gateway-service
  cloud:
    nacos:
      server-addr: localhost:8848
    gateway:
      routes:
        - id: user-service # 路由标识，必须唯一，自定义
          uri: lb://userservice # lb表示负载均衡，userservice为服务名称，路由到目标地址
          predicates: #路由断言，判断请求是否符合要求
            -Path=/user/** #路径断言，判断路径是否以user开头
          filters:
            - AddRequestHeader=Truth,SpringNB #过滤器，给userservice添加一个请求头 Truth为key
```



配置默认过滤器

```
server:
  port: 9999
spring:
  application:
    name: gateway-service
  cloud:
    nacos:
      server-addr: localhost:8848
    gateway:
      routes:
        - id: user-service # 路由标识，必须唯一，自定义
          uri: lb://userservice # lb表示负载均衡，userservice为服务名称，路由到目标地址
          predicates: #路由断言，判断请求是否符合要求
            -Path=/user/** #路径断言，判断路径是否以user开头
#          filters:
#            - AddRequestHeader=Truth,SpringNB #过滤器，给userservice添加一个请求头
        - id: order-service
          uri: lb://orderservice
          predicates:
            -Path=/order/**
      default-filters: #默认过滤器，所有路由都会执行
        - AddRequestHeader=Truth,SpringNB

```



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\过滤器总结.png)

### 5 全局过滤器（GlobalFilter）

全局过滤器的作用也是处理一切进入网关的请求和微服务响应，与GatewayFilter的作用一样

区别在于Gateway是通过配置定义，处理逻辑是固定的，而GlobalFilter的逻辑需要自己手写代码去实现。



定义方式实现GlobalFilter接口

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\Gloabal接口.png)



```
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.annotation.Order;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.stereotype.Component;
import org.springframework.util.MultiValueMap;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Order(-1)//设置过滤器执行顺序
@Component
public class AuthorizedFilter implements GlobalFilter {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        //1 获取请求参数

        ServerHttpRequest request = exchange.getRequest();
        MultiValueMap<String, String> params = request.getQueryParams();


        //2 获取参数中的authorization参数
        String auth = params.getFirst("authorization");

        //3 判断参数是否等于admin
        if (auth.equals("admin")){
            //4 是，放行
            return chain.filter(exchange);
        }




        //5 否，拦截

        //5.1 设置拦截码
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);

        //5.2 拦截请求
        return exchange.getResponse().setComplete();
    }
}
```



![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\全局过滤器的作用.png)



### 6 过滤器执行顺序

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\过滤器执行顺序.png)

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\过滤器执行顺序1.png)

### 7 网关的跨域配置



跨域

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\gateway\跨域.png)

跨域问题：浏览器禁止请求的发起者与服务端发送跨域ajax请求，请求被浏览器拦截的问题



cors跨域

```
server:
  port: 8686
spring:
  application:
    name: gateway
  cloud:
    gateway:
      globalcors:
        cors-configurations:
          '[/**]':
            allowed-origins:
              - "http://localhost:8383"
              - "http://localhost:8282"
            allowed-headers: "*"
            allowed-methods: "*"
            max-age: 3600
      discovery:
        locator:
          enabled: true
```





```
spring:
  cloud:
    gateway:
      globalcors:
        add-to-simple-url-handler-mapping: true
        cors-configurations:
          '[/**]':
            allowedOrigins: #允许哪些网站的跨域请求
              - "http://localhost:8383"
              - "http://localhost:8282"
            allowedMethods: #允许的跨域ajax的请求方式
              - "GET"
              - "POST"
              - "PUT"
              - "DELETE"
              - "OPTIONS"
            allowedHeaders: "*" #允许在请求中携带的头信息
            allowCredentials: true #是否允许携带cookie
            maxAge: 360000 #这次跨域检测的有效期
```



