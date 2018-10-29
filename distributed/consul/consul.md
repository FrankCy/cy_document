# Consul #
### Consul [官网](https://www.consul.io/) ###
- 简介<br/>
Consul支持多数据中心分布式高可用的服务发现和配置共享服务软件，采用```Go语言```开发，天然移植Linux、windows和Mac OS X<br/>
支持```健康检查```;<br/>
允许```HTTP```和```DNS```协调调用API存储键值对；<br/>
采用```Raft一致性协议算法```来保证服务的高可用；<br/>
采用```Key / Value```存储;<br/>
支持```多数据中心```方案;<br/>
使用```GOSSIP协议```管理成员和广播消息，并且支持```ACL```访问控制；<br/>

- 使用场景<br/>
  + Docker 实例的注册与配置共享
  + 与 Consul template 服务集成，动态生成 Nginx 和 HAProxy 等配置文件
  + ``` Spring Cloud Consul 服务发现和配置文件存储 ```

- Consul 优势
  + 使用 Raft 算法来保证一致性, 比 ZooKeeper 的 Paxos 算法更简单直接
  + 支持多数据中心，内外网的服务采用不同的端口进行监听。 ZooKeeper 和 etcd 均不提供多数据中心功能的支持
  + 支持健康检查，etcd 不提供此功能
  + 支持 http 和 dns 协议接口。ZooKeeper 的集成较为复杂，etcd 只支持 http 协议
  + 官方提供 web 管理界面，etcd 无此功能
  + Consul 1.2 新增 Service Mesh 解决方

- 服务发现比较:Consul vs Zookeeper vs Etcd vs Eureka <br/><br/>

  Feature|Consul|zookeeper|etcd|euerka|
  ---|:--:|---:|---:|---:
  服务健康检查|服务状态，内存，硬盘等|(弱)长连接，keepalive|连接心跳|可配支持
  多数据中心|支持|--|--|--
  kv存储服务|支持|支持|支持|--
  一致性|raft|paxos|raft|--
  cap|cp|cp|cp|ap
  使用接口(多语言能力)|支持http和dns|客户端|http/grpc|	http（sidecar）
  watch支持|全量/支持long polling|支持|支持 long polling|支持 long polling/大部分增量
  自身监控|metrics|--|metrics|metrics
  安全|acl /https|acl|https支持（弱）|--
  spring cloud集成|已支持|已支持|已支持|已支持

  [转 ：Consul vs Zookeeper vs Etcd vs Eureka](https://blog.csdn.net/dengyisheng/article/details/71215234)
  + ``` 注：文章有错误 Consul 为 CP ```

- 需要知道的概念<br/>
  + Cluster环境
  要想利用Consul提供的服务实现服务的注册与发现，我们需要搭建Consul Cluster 环境
  + agent
  在Consul方案中，每个提供服务的节点上都要部署和运行Consul的agent，所有运行Consul agent节点的集合构成Consul Cluster<br/>
     + Consul agent 两种运行模式 <br/>
     Server和Client。这里的Server和Client只是Consul集群层面的区分，与搭建在Cluster之上 的应用服务无关 <br/>
     以Server模式运行的Consul agent节点用于维护Consul集群的状态，官方建议每个Consul Cluster至少有3个或以上的运行在Server mode的Agent，Client节点不限

### MAC 安装 ###
- brew install <br/>
``` brew install consul ```

- 修改Consul启动参数 <br/>
``` vim /usr/local/opt/consul/homebrew.mxcl.consul.plist ```

- 修改ProgramArguments配置 <br/>
```
<key>ProgramArguments</key>
<array>
  <string>/usr/local/opt/consul/bin/consul</string>
  <string>agent</string>
  <string>-server</string>
  <string>-bootstrap</string>
  <string>-advertise</string>
  <string>127.0.0.1</string>
  <string>-data-dir</string>
  <string>./data</string>
  <string>-ui</string>
</array>
```

- 启动<br/>
``` brew services start consul ```

- 访问界面<br/>
[http://localhost:8500/](http://localhost:8500/)
