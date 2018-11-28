# SC | Zuul 详解 #

### 工作原理 ###
Ribbon中的核心接口共同定义了Ribbon的行为特性，如下图所示：

接   口|描   述|默认实现类
---|:--|:--
IClientConfig|定义Ribbon中管理配置的接口|DefaultClientConfigImpl
IRule|定义Ribbon中负载均衡策略的接口|ZoneAvoidanceRule
IPing|定义定期ping服务检查可用性的接口|DummyPing
ServerList<Server>|定义获取服务列表方法的接口|ConfigurationBasedServerList
ServerListFilter<Server>|定义特定希望获取服务列表的方法的接口|ZonePreferenceServerListFilter
ILoadBalancer|定义负载均衡选择服务的核心方法接口|ZoneAwareLoadBalancer
ServerListUpdater|为DynamicServerListLoadBalancer定义动态更新服务列表的接口|PollingServerListUpdater

```它们是Ribbon的基础接口，了解它们对了解Ribbon底层实现有很大的帮助```<br/>


### 负载均衡策略源码导读 ###
