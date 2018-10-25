# Consul #
### Consul ###
- 简介<br/>
Consul支持多数据中心分布式高可用的服务发现和配置共享服务软件，采用```Go语言```开发<br/>
支持```健康检查```，允许```HTTP```和```DNS```协调调用API存储键值对；<br/>
采用```Raft一致性协议算法```来保证服务的高可用；<br/>
使用```GOSSIP协议```管理成员和广播消息，并且支持```ACL```访问控制；<br/>

- 使用场景<br/>

### 安装 ###
- MAC <br/>
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
``` http://localhost:8500/ ```


