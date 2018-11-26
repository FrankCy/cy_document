# SC | Ribbon #
### 简介 ###
是Netflix公司开发的一个```负载均衡组件```

### Ribbon & 负载均衡 ###
```负载均衡（Load Balance），即利用特定的方式将流量分配到多个服务器节点上的一种实现方式```，以此来提高系统的吞吐量与系统处理能力，通常我们会想到Nginx与F5，那它们与我们说的Ribbon又有什么区别呢？<br/>
对负载均衡的分类有很多种，Nginx和F5是集中负载，也叫服务端负载均衡，服务端负载均衡如下图所示：
![blockchain](https://github.com/FrankCy/cy_document/blob/master/design-drawing/%E6%9C%8D%E5%8A%A1%E7%AB%AF%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png "服务端负载均衡")<br/>
在微服务范畴内，实例库一般存储在Eureka，Consul，Zookeeper，etcd这样的注册中心，此时负载均衡类似Ribbon的IPC（Inter-Process Communication，```进程间通信```）组件，即```进程间通信组件```,进程内负载均衡叫作客户端负载均衡，如下图所示：
![blockchain](https://github.com/FrankCy/cy_document/blob/master/design-drawing/%E6%9C%8D%E5%8A%A1%E7%AB%AF%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png "服务端负载均衡")<br/>



