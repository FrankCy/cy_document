# SC | Zuul #
### 简介 ####
- Zuul是从设备和网站到后端应用程序所有请求到入口，为内部服务提供可配置的对外URL到服务的映射关系，基于```JVM的后端路由器```。其具备功能如下：<br/>
  + 认证与鉴权
  + 压力控制
  + 金丝雀测试
  + 动态路由
  + 负载消减
  + 静态响应处理
  + 主动流量管理<br/>

底层基于```servlet```，本质组件是一系列```Filter所构成的责任链```

### 典型配置 ###

#### 路由配置 ####
Zuul作为微服务"路由器"，那它都有哪些配置呢？
- 单实例serviceId映射
```
zuul:
  routes:
    client-a:
      path: /client/**
      serviceId: client-a
```
从/client/** 到 client-a服务到一个映射规则，其实我们可以把它简化成一个比较简单到配置：
```
zuul:
  routes:
    client-a: /client/**
```
此时，Zuul会为client-a添加一个默认的映射规则/client-a/**，相当于上面的配置;

- 单实例url映射<br/>
除了路由到服务之外，还可以路由到物理地址，将serviceId替换为url即可
```
zuul:
  routes:
    client-a:
      path: /client/**
      url: http://localhost:7070 #client-a的地址
```

- 多实例路由<br/>
默认情况下，Zuul会使用Eureka中集成的基本```负载均衡```功能，如果使用Ribbon的负载均衡功能，就需要```指定一个serviceId```，此操作```需要禁止Ribbon使用Eureka```，在E版之后，新增了负载均衡策略配置：
```
zuul:
  routes:
    ribbon-route:
      path: /ribbon/**
      serviceId: ribbon-route

ribbon:
  eureka:
    enabled: false  #禁止Ribbon使用Eureka

ribbon-route:
  ribbon:
    NIWSServerListClassName: com.netflix.loadbalancer.ConfigurationBasedServerList
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule     #Ribbon LB Strategy
    listOfServers: localhost:7070,localhost:7071     #client services for Ribbon LB
```

- forward本地跳转<br/>
有时候我们在Zuul中会做一些逻辑处理，在网关中写好一个接口：
```
@RestController
public class TestController {
	@GetMapping("/client")
	public String add(Integer a, Integer b){
		return "本地跳转：" + (a + b);
	}
}
```
如果我们希望Zuul跳转到这个方法来处理，就要在配置中指定：
```
########################## foward跳转本地url  ##########################
zuul:
  routes:
    client-a:
      path: /client/**
      url: forward:/client
```

- 相同路径的加载规则 <br/>
配置多个会按照加载顺序，加载最末尾的，如下代码，会加载client-a：
```
########################## 映射覆盖情况 ##########################
zuul:
  routes:
    client-b:
      path: /client/**
      serviceId: client-b
    client-a:
      path: /client/**
      serviceId: client-a
```

#### 路由通配符 ####
- path: /client/** 都是什么意思呢？如下图：

规则|释义|示例
---|:--|:---
/**|匹配任意数量的路径与字符|/client/add，/client/mul，/client/a，/client/add/a，/client/mul/a/b，
/*|匹配任意数量的字符|/client/add，/client/mul，/client/a，
/?|匹配单个字符|/client/a，/client/b，/client/c，

```开发过程中可以根据需求进行路由匹配符的选择```

### 功能配置 ###
#### 路由前缀 ####
配置路由时，我们可以配置一个统一的代理前缀：
```
########################## 服务忽略、路径忽略、前缀、重定向问题 ##########################
zuul:
  prefix: /pre                  #使用prefix指定前缀
  routes:
    client-a: /client/**
```
这样，我们在通过Zuul访问后台接口时就要加上pre前缀，请求路径即变成```/pre/client/xxx```，但实际起作用的是/client/add，即Zuul会把代理路径从请求路径中移除。可用```stripPrefix=false关闭此功能```：
```
########################## 敏感头设置 ##########################
zuul:
  prefix: /pre
  routes:
    client-a:
      path: /client/**
      serviceId: client-a
      stripPrefix: false
```
关闭后请求路径是/pre/client/xxx，实际请求路径也是/pre/client/xxx，建议实际开发中不要使用此配置

#### 服务屏蔽与路径屏蔽 ####
为了避免服务或路径被侵入，可将它们屏蔽掉：
```
zuul:
  ignored-services: client-b    #忽略的服务，防服务侵入
  ignored-patterns: /**/div/**  #忽略的接口，屏蔽接口
  prefix: /pre                  #使用prefix指定前缀
  routes:
    client-a: /client/**
```
加上```ignored-services```和```ignored-patterns```之后，Zuul在拉取服务列表，创建映射规则时候，就会忽略掉client-b服务与/**/div/**接口

#### 敏感头信息 ####
构建系统时，将信息放入http的header传值是十分方便的，协议的认证信息默认也存放在header，例如：Cookie，或通过BASE64加密后放在Authorization里面。当系统和外部系统之间关联时，就可能会出现这些信息的泄漏。Zuul配置里面可以指定敏感头，切断它与下层服务之间的交互：
```
########################## 敏感头设置 ##########################
zuul:
  prefix: /pre
  routes:
    client-a:
      path: /client/**
      sensitiveHeaders: Cookie,Set-Cookie,Authorization
      serviceId: client-a
      stripPrefix: false
```

#### 重定向问题 ####
在实际使用时，Zuul作为网关，认证后直接请求下游服务，在默认情况下会返回下游服务的host给客户端，而不是Zuul的host，这样就暴露了实际服务的host，我们通过以下配置可以解决：
```
zuul:
  add-host-header: true         #重定向header问题
  routes:
    client-a: /client/**
```

#### 重试机制 ####
实际投产后，请求会出现失败的情况，而由于业务的局限性我们不能做服务感知，只有通过重试来解决，可通过以下配置实现：
```
########################## 重试机制 ##########################
zuul:
  retryable: true #开启重试

ribbon:
  MaxAutoRetries: 1 #同一个服务重试的次数(除去首次)
  MaxAutoRetriesNextServer: 1  #切换相同服务数量
```
切记：此功能慎用，要保证接口的幂等性前提时使用。















