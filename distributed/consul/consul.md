# Consul #
### Consul [官网](https://www.consul.io/) ###
- 简介<br/>
Consul支持多数据中心分布式高可用的服务发现和配置共享服务软件，采用```Go语言```开发<br/>
支持```健康检查```，允许```HTTP```和```DNS```协调调用API存储键值对；<br/>
采用```Raft一致性协议算法```来保证服务的高可用；<br/>
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

### [Spring Cloud Consul 示例](https://github.com/FrankCy/cloud) ###
- [服务调用 - cloud-consul-client](https://github.com/FrankCy/cloud/tree/master/cloud-consul-client)
- [服务发布 - cloud-consul-server](https://github.com/FrankCy/cloud/tree/master/cloud-consul-server)
