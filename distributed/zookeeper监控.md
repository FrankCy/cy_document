# ZooKeeper 监控 #
### zkui ###
- 简介<br/>
一个开源的允许对Zookeeper进行CRUD操作对可视化界面

### 搭建步骤 ###
- 下载源码 <br/>
[github：https://github.com/DeemOpen/zkui](https://github.com/DeemOpen/zkui)

- 通过maven编译生成target(```/工程目录/zkui/target```) <br/>
- 将项目中```config.cfg``` 拷贝至target下
- 修改 config.cfg 内容<br/>
```
zkServer=localhost:2181
```
- 保存并关闭config.cfg
- target下执行
``` java -jar zkui-2.0-SNAPSHOT-jar-with-dependencies.jar  ```
- 访问[http://localhost:9090](http://localhost:9090)
- 账号密码
```
username: admin
password: manager
```