# SC | Zuul 精进 #
### Spring Cloud Zuul 多层负载 ###
- 痛点 <br/>
实际部署时，为保证Zuul的高可用性，一般不会是单节点，会部署多节点，他们通过Nginx进行负载均衡，可是会出现一个问题，当某一台Zuul宕机，Nginx是不会得到通知的，还会将请求分发到Zuul集群这个宕机的服务上，那对平台会造成很大的损失！

- 解决痛点 <br/>
使用OpenResty整合Nginx和Lua，通过OpenResty+Lua

### Spring Cloud Zuul 应用优化 ###

### Spring Cloud Zuul 原理&核心源码解析 ###

