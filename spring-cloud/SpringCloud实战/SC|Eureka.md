# SC | Eureka #
### 简介 ####
Netflix Eureka 是由Netflix开源的一款基于REST服务发现组件，包括Eureka Server 及 Eureka Client。

### 服务发现概述 ###
- 服务发现由来<br/>
服务发现／注册中心／名字服务，都泛指服务发现，它的出现与程序架构不断演进有关。大体如下：
  + 单体架构时代

  + SOA架构时代

  + 微服务时代

- Eureka简介<br/>
该组件提供的服务发现可以为负载均衡，failover等提供支持，eureka包含```eureka server和eureka client```。<br/>
```Eureka Server提供REST服务```<br/>
```Eureka Client使用Java编写的客户端，用于简化与Eureka Server的交互```<br/>

- 服务发现技术选型<br/>

名称|类型|AP或CP|语言|依赖|集成|一致性算法
---|:--|:---|:---|:---|:---|:---
Zookeeper|General|CP|Java|JVM|Client Binding|Paxos
Doozer|General|CP|Go||Client Binding|Paxos
Consul|General|CP|Go||HTTP/DNS Library|Raft
Etcd|General|CP or Mixed(1)|Go||Client Binding / HTTP|Raft
SmartStack|Dedicated|AP|Ruby|haproxy/Zookeeper|Sidekick(nerve/synapse)|
Eureka|Dedicated|AP|Java|JVM|Java Client|
NSQ(lookupd)|Dedicated|AP|Go||Client Binding|
Serf|Dedicated|AP|Go||Local CLI|
Spotify(DNS)|Dedicated|AP|N/A|Bind|DNS Library|
SkyDNS|Dedicated|Mixed(2)|Go||HTTP/DNS Library|

- 为什么使用Eureka
  + 选择AP而不是CP
  + JAVA语言开发，JAVA团队维护起来更便利
  + Eureka是Netflix开源套件的一部分，跟Spring Cloud其它组件结合的较好（如：Zuul，Ribbon）

### Eureka Server REST API 简介 ###
- REST API 列表

- REST API 实例
  + 查询所有应用实例
  + 根据appId查询
  + 根据appId及instanceId查询
  + 根据instanceId查询
  + 注册新应用实例
  + 注销应用实例
  + 暂停／下线应用实例
  + 恢复应用实例
  + 应用实例发送心跳
  + 修改应用实例元数据


