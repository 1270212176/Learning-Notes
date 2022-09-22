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

### 4 nacos服务实例的权重设置



nacos提供了权重配置来控制访问频率，权重越大则访问频率越高,权重设置为0则不会被访问

设置权重方式

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos设置权重.png)



### 5 nacos环境隔离

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



### 6 nacos的临时实例与非临时实例

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos的临时实例与非临时实例.png)



```
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false #是否是临时实例
```

**临时实例down掉后，会被nacos删除，非临时实例则会等待它恢复**



### 7 nacos与eureka的区别

![](C:\Users\1270212176\Desktop\大三下实训\RabbitMq学习截图\nacos与eureka区别.png)