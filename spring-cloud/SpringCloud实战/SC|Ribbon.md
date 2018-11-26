# SC | Ribbon #
### 简介 ###
是Netflix公司开发的一个```负载均衡组件```

### Ribbon & 负载均衡 ###
```负载均衡（Load Balance），即利用特定的方式将流量分配到多个服务器节点上的一种实现方式```，以此来提高系统的吞吐量与系统处理能力，通常我们会想到Nginx与F5，那它们与我们说的Ribbon又有什么区别呢？<br/>
对负载均衡的分类有很多种，Nginx和F5是集中负载，也叫服务端负载均衡，服务端负载均衡如下图所示：
![blockchain](https://github.com/FrankCy/cy_document/blob/master/design-drawing/%E6%9C%8D%E5%8A%A1%E7%AB%AF%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png "服务端负载均衡")<br/>
在微服务范畴内，实例库一般存储在Eureka，Consul，Zookeeper，etcd这样的注册中心，此时负载均衡类似Ribbon的IPC（Inter-Process Communication，```进程间通信```）组件，即```进程间通信组件```,进程内负载均衡叫作客户端负载均衡，如下图所示：
![blockchain](https://github.com/FrankCy/cy_document/blob/master/design-drawing/%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png "客户端负载均衡")<br/>

- Spring Cloud Ribbon
根据官方描述大致可以理解为：Ribbon是客户端负载均衡器，赋予应用可以对HTTP与TCP进行操作，客户端负载均衡也是进程内负载均衡的一种。```在Spring Cloud中它是必不可少的，否则服务便不能横向扩展```，且，```Feign和Zuul中默认集成Ribbon```，在服务之间凡是涉及调用的，都可以集成它，以达到让服务提高伸缩性的能力。

### Ribbon 实战 ###
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
    name: client-a
eureka:
  client:
    serviceUrl:
      defaultZone: http://${eureka.host:127.0.0.1}:${eureka.port:8888}/eureka/
  instance:
    prefer-ip-address: true
```

#### 添加Ribbon并完成客户端负载均衡####
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




