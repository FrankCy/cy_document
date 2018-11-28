# SC | Ribbon #
### 简介 ###
是Netflix公司开发的一个```负载均衡组件```

### Ribbon & 负载均衡 ###
```负载均衡（Load Balance），即利用特定的方式将流量分配到多个服务器节点上的一种实现方式```，以此来提高系统的吞吐量与系统处理能力，通常我们会想到Nginx与F5，那它们与我们说的Ribbon又有什么区别呢？<br/>
对负载均衡的分类有很多种，Nginx和F5是集中负载，也叫服务端负载均衡，服务端负载均衡如下图所示：<br/>
![blockchain](https://github.com/FrankCy/cy_document/blob/master/design-drawing/%E6%9C%8D%E5%8A%A1%E7%AB%AF%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png "服务端负载均衡")<br/>
在微服务范畴内，实例库一般存储在Eureka，Consul，Zookeeper，etcd这样的注册中心，此时负载均衡类似Ribbon的IPC（Inter-Process Communication，```进程间通信```）组件，即```进程间通信组件```,进程内负载均衡叫作客户端负载均衡，如下图所示：<br/>
![blockchain](https://github.com/FrankCy/cy_document/blob/master/design-drawing/%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png "客户端负载均衡")<br/>

- Spring Cloud Ribbon
根据官方描述大致可以理解为：Ribbon是客户端负载均衡器，赋予应用可以对HTTP与TCP进行操作，客户端负载均衡也是进程内负载均衡的一种。```在Spring Cloud中它是必不可少的，否则服务便不能横向扩展```，且，```Feign和Zuul中默认集成Ribbon```，在服务之间凡是涉及调用的，都可以集成它，以达到让服务提高伸缩性的能力。

### Ribbon 实例 ###
#### 添加Eureka Server ####
- Spring Boot启动类中添加注解@EnableEurekaServer，声明一个 Eureka Server
```
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

- EurekaServer yml配置
```
server:
  port: 8888
eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

#### 添加Eureka Client ####

- Spring Boot启动类中添加注解@EnableDiscoveryClient注解
```
@SpringBootApplication
@EnableDiscoveryClient
public class ClientAApplication {

    public static void main(String[] args) {
        SpringApplication.run(ClientAApplication.class, args);
    }
}

```

- 添加Controller，略

- EurekaClient yml配置
```
server:
  port: 7070
spring:
  application:
    name: Application_Eureka_Client
eureka:
  client:
    serviceUrl:
      defaultZone: http://${eureka.host:127.0.0.1}:${eureka.port:8888}/eureka/
  instance:
    prefer-ip-address: true
```

#### 添加Ribbon并完成客户端负载均衡 ####
- 添加Ribbon所需依赖
```
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

- Spring Boot启动类中添加注解@EnableDiscoveryClient注解，它作为Ribbon客户端负载均衡的入口
```
@SpringBootApplication
@EnableDiscoveryClient
public class RibbonLoadbalancerApplication {

    public static void main(String[] args) {
        SpringApplication.run(RibbonLoadbalancerApplication.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```
这里要注意的是 ```@LoadBalanced```，它标记是应用于Ribbon的负载均衡调度方式，方式为通过RestTemplate进行实现，这部分代码一定要注入到Spring容器当中。

- 添加Controller - API，用于调用EurekaClient，通过```@Autowired```将RestTemplate声明，并在函数内通过```http://Application-name/controller_method?parames```的方式请求客户端：
```
@RestController
public class TestController {

    @Autowired
    private RestTemplate restTemplate;

	@GetMapping("/hello")
	public String add(String param1, String param2) {
		String result = restTemplate.getForObject("http://Application_Eureka_Client/hello?param1=" + param1 + "&param2=" + param2, String.class);
		System.out.println(result);
		return result;
	}
}
```

- Ribbon yml 配置
```
spring:
  application:
    name: ribbon-loadbalancer
server:
  port: 7777
eureka:
  client:
    serviceUrl:
      defaultZone: http://${eureka.host:127.0.0.1}:${eureka.port:8888}/eureka/
  instance:
    prefer-ip-address: true
```

#### 进行测试 ####
- 启动EurekaServer
- 启动EurekaClient（分别启动多个端口）
- 启动Ribbon
- 通过访问Ribbon来进行测试客户端负载均衡是否有效


### Ribbon 实战 ###
#### 负载均衡策略 ####
Ribbon支持哪些负载均衡策略呢，例如Nginx就支持轮询（Round Robin）、权重（Weight）、ip_hash等，在不同的业务场景我们可以采用适合的策略进行使用，下面介绍Ribbon所支持的负载均衡策略：

策略类|命   名|描    述
---|:--:|:---
RandomRule|随即策略|随机选择server
RoundRobinRule|轮询策略|按顺序循环选择server
RetryRule|重试策略|在一个配置内当选择server不成功，则一直重试选择到一个可用server
BestAvailableRule|最低并发策略|server检查，如果server断路器打开，则忽略，并再去选择一个并发连接最低的server
AvailabilityFilteringRule|可用过滤策略|过滤掉一直连接失败并标记为circuit tripped的server，过滤掉那些高并发连接的server（active connections超过配置的阀值）
ResponseTimeWeightedRule|响应时间加权策略|根据server响应时间分配权重。响应时间越长，权重越低，被选择的概率就越低；响应时间越短，权重越高，被选择到的概率就越高。影响响应时常的因素有：网络、磁盘、IO等
ZoneAvoidanceRule|区域权衡策略|综合判断server所在区域的性能和server的可用性，轮询选择server，并且判断一个```AWS Zone```的运行性能是否可用，剔除不可用的Zone中的所有server

- Ribbon 全局策略设置<br/>
只需要在工程内新建一个类并注入到Spring容器当中即可：
```
@Configuration
public class TestConfigration {
    @Bean
    public IRule ribbonRule() {
        return new RandomRule();
    }
}
```
就是这么简单，只需要在启动能扫描到的包内添加这段代码指定所要使用七种策略中的某种，就可以设置全局策略
- 基于注解的方式设置策略
设置一个特有的策略供源服务使用，可以使用@RibbonClient注解：
```
@Configuration
@AvoidScan
public class TestConfigration {

    @Autowired
    IClientConfig icclientconfig;

    @Bean
    public IRule ribbonRule(IClientConfig icclientconfig) {
        return new RandomRule();
    }
}
```
这里@AvoidScan是自定义标签，例子最后会说明。注入的IClientConfig是针对客户端的配置管理器```（com.netflix.client.config）```。<br/>
修改启动类，并添加@RibbonClient注解，依此来对源服务进行负载约束：
```
@RibbonClient(name = "application-Client-a", configuration = TestConfiguration.class)
@ComponentScan(excludeFilters = {@ComponentScan.Filter(type = FilterType.ANNOTATION, value = {AvoidScan.class})})
```
@RibbonClient的意思就是对客户端Application-Client-a的策略是TestConfiguration所指定的策略。这里使用@ComponentScan注解是让Spring不去扫描被@AvoidScan注解标记的配置类，因为配置的是对单个源服务生效的，所以不能应用于全局，如果不排除，启动会报错。<br/>
同事我们也可以对多个客户端进行策略指定，在启动类上添加以下注解，可以把@RibbonClient注解删除，使用@RibbonClients注解：
```
@RibbonClients(value = {
		@RibbonClient(name = "application-Client-a", configuration = TestConfiguration.class),
		@RibbonClient(name = "application-Client-b", configuration = TestConfiguration.class)
})
```

- 基于配置文件的方式设置策略
Ribbon还支持通过配置文件设置策略


#### Ribbon超时重试 ####
F版中的Ribbon重试机制是默认开启的，添加超时时间和重试策略配置：
```
ribbon:
    ConnectTimeout: 3000
    ReadTimeout: 60000
    MaxAutoRetries: 1 #对第一次请求的服务的重试次数
    MaxAutoRetriesNextServer: 1 #要重试的下一个服务的最大数量（不包括第一个服务）
    OkToRetryOnAllOperations: true
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule#
```

#### Ribbon饥饿加载 ####
默认情况下，Ribbon对客户进行负载均衡时不是在程序启动时加载，而是在调用时加载的，那在实际使用时第一次请求服务需要等待甚至连接超时，我们可以通过配置在服务启动时加载Ribbon客户端负载均衡配置：
```
ribbon:
  eager-load:
    enabled: true
    clients: application-name-a, application-name-b, application-name-c
```

#### 利用配置文件自定义Ribbon客户端 ####
通过配置文件指定Ribbon默认加载类，从而更改Ribbon客户端的行为方式，这种优先级较高，高于@RibbonClient指定的配置和源码中加载的相关Bean。

配置项|说   明
---|:--
<clientName>.ribbon.NFLoadBalancerClassName|指定ILoadBalancer的实现类
<clientName>.ribbon.NFLoadBalancer|指定IRule的实现类
<clientName>.ribbon.NFLoadBalancer|指定IPing的实现类
<clientName>.ribbon.NIWSServerListClassName|指定ServerList实现类
<clientName>.ribbon.NIWSServerListFilterClassName|指定ServerListFilter实现类

可使用Ribbon自带的实现类，也允许自己实现。下面是一个对```Client源```服务的相关定制示例：
```
client:
    ribbon:
        NFLoadBalancerClassName: com.netflix.loadbancer.ConfigurationBaseServerList
        NFLoadBalancerRuleClassName: com.netflix.loadbancer.WeightedResponseTimeRule
```

#### Ribbon脱离Eureka使用 ####
默认情况下，Ribbon客户端从Eureka注册中心获取服务注册信息列表，在某些特定的业务情况下，Ribbon要关闭自动订阅服务注册中心的功能，那我们应该如何做到让Ribbon脱离Eureka的管理呢？示例如下：<br/>
<br/>
首先要让Ribbon禁用Eureka功能：
```
ribbon:
    eureka:
        enabled: false
```
然后对源服务设定地址列表：
```
client:
    ribbon:
        listOfServers: http://localhost:7070，http://localhost:7071
```



