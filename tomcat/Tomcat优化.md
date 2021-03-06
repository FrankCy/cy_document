# Tomcat 优化 #
### 目录结构 ###
- bin:存放启动和关闭Tomcat的脚本文件
- conf:存放Tomcat服务器的各种配置文件
- lib:存放tomcat服务器支撑的jar包
- logs:存放Tomcat的日志文件
- temp:存放Tomcat运行时产生的临时文件
- webapps:web应用虽在目录，即供外界访问的web资源的存放目录
- work:Tomcat的工作目录

### 配置JDK和内存 ###
- 示例如下
```
JAVA_HOME=/usr/program/jdk1.8.0_72
CATALINA_HOME=/usr/program/tomcat8
CATALINA_OPTS="-server -Xms528m -Xmx528m -XX:PermSize=256m -XX:MaxPermSize=358m"
CATALINA_PID=$CATALINA_HOME/catalina.pid
```

### 配置管理员账户 /bin/conf/tomcat-users.xml ###
- 示例如下
```
<role rolename="manager"/>
<role rolename="manager-gui"/>
<role rolename="admin"/>
<role rolename="admin-gui"/>
<user username="tomcat" password="tomcat" roles="admin-gui,admin,manager-gui,manager"/>
```

### 配置服务器 server.xml ###
#### 主要标签简介 ####
-  &lt;Server&gt; <br/>
```
   <Server> 代表整个Servlet容器组件，是最顶层元素，可以包含一个或多个<Service>元素
   	<Service> 包含一个<Engine>元素以及一个或多个<Connector>元素，这些<Connector>共享一个<Engine>
   		<Connector/>代表和客户程序实际交互的组件，负责接收客户请求，以及向客户返回响应
   			<Engine>每个<Service>元素只能包含一个<Engine>元素，它处理在同一个<Service>中所有<Connector>接收到的客户请求
   				<Host>在一个<Engine>中可以包含多个<Host>,它代表一个虚拟主机(即一个服务器程序可以部署在多个有不同IP的服务器主机上)，它可以包含一个或多个应用
   					<Context>使用最频繁的元素，代表了运行在虚拟主机上的单个web应用</Context>
   				</Host>
   			</Engine>
   		</Connector>
   	</Service>
   </Server>
```
#### Executor 连接池配置 ####
- 示例如下
```
<Executor
    name="tomcatThreadPool"
    namePrefix="catalina-exec-"
    maxThreads="500"
    minSpareThreads="100"
    prestartminSpareThreads = "true"
    maxQueueSize = "100"
/>
```
- maxThreads <br/>
最大并发数，默认设置 200，一般建议在 500 ~ 800，根据硬件设施和业务来判断

- minSpareThreads <br/>
Tomcat 初始化时创建的线程数，默认设置 25

- prestartminSpareThreads <br/>
在 Tomcat 初始化的时候就初始化 minSpareThreads 的参数值，如果不等于 true，minSpareThreads 的值就没啥效果了

- maxQueueSize <br/>
最大的等待队列数，超过则拒绝请求

#### Connector 标签 ####
- 示例如下
```
<Connector
   executor="tomcatThreadPool"
   port="8080"
   protocol="org.apache.coyote.http11.Http11Nio2Protocol"
   connectionTimeout="20000"
   maxConnections="10000"
   redirectPort="8443"
   enableLookups="false"
   acceptCount="100"
   maxPostSize="10485760"
   compression="on"
   disableUploadTimeout="true"
   compressionMinSize="2048"
   acceptorThreadCount="2"
   compressableMimeType="text/html,text/xml,text/plain,text/css,text/javascript,application/javascript"
   URIEncoding="utf-8"
/>
```
- protocol <br/>
Tomcat 8 设置 nio2 更好：org.apache.coyote.http11.Http11Nio2Protocol（如果这个用不了，可用org.apache.coyote.http11.Http11NioProtocol替代）

- enableLookups <br/>
禁用DNS查询

- connectionTimeout <br/>
网络连接超时，单位：毫秒。设置为0表示永不超时，这样设置有隐患的。通常可设置为30000毫秒

- maxPostSize <br/>
以 FORM URL 参数方式的 POST 提交方式，限制提交最大的大小，默认是 2097152(2兆)，它使用的单位是字节。10485760 为 10M。如果要禁用限制，则可以设置为 -1

- acceptorThreadCount <br/>
用于接收连接的线程的数量，默认值是1。一般这个指需要改动的时候是因为该服务器是一个多核CPU，如果是多核 CPU 一般配置为 2.

- maxThreads="200" <br/>
表示tomcat同一时间最多可以处理200个连接

- minSpareThreads="100" <br/>
表示即使没有人使用也开这么多空线程等待

- maxSpareThreads="800" <br/>
表示如果最多可以空800个线程，例如某时刻有805人访问，之后没有人访问了，则tomcat不会保留800个空线程，而是关闭5个空的

- acceptCount="200" <br/>
指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理，默认设置 100

- keepAliveTimeout="15" <br/>
长连接最大保持时间（毫秒）

- maxKeepAliveRequests <br/>
最大长连接个数（1表示禁用，-1表示不限制个数，默认100个。一般设置在100~200之间）

- maxHttpHeaderSize <br/>
http请求头信息的最大程度，超过此长度的部分不予处理。一般8K

- URIEncoding <br/>
指定Tomcat容器的URL编码格式

- nableLookups <br/>
是否反查域名，取值为：true或false。为了提高处理能力，应设置为false

- disableUploadTimeout <br/>
上传时是否使用超时机制

#### 禁用AJP ####
- 如果你服务器没有使用 Apache <br/>
把下面这一行注释掉，默认 Tomcat 是开启的。
```
<!-- <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" /> -->
```

#### Contextpath (虚拟目标配置) ####
- 不想将项目放入webapp下时，我们指定工程路径<br/>
如下述代码段，应用程序在D盘，而tomcat在C盘，就需要配置一行这个代码段
```
<Contextpath="/myapp"docBase="D:/work/myapp"></Context>
```

#### Context（元素配置） ####
- tomcat启动项目加载流程：依次按照以下步骤进行查找对应的WEB应用程序 <br/>
  + 到Tomcat安装目录/conf/Context.xml文件中查找元素
  + 到Tomcat安装录/conf/[enginename]/[hostname]/context.xml.default文件中查找元素。
  ```
  [enginename]:表示的name属性
  [hostname]:表示d的那么属性
  ```
  + 到Tomcat安装目录/conf/[enginename]/[hostname]/[contextpath].xml文件中查找元素
  ```
  [contextpath]:表示单个Web应用的URL入口
  ```
  + 到Web应用的META-INF/context.xml文件中查找元素
  + 到Tomcat安装目录/conf/server.xml文件中查找元素。```只适用于单个Web应用```

### JVM 优化 （与Tomcat内存配置相关） ###
#### [JVM内存模型](http://xmuzyq.iteye.com/blog/599750) ####
- Java 的内存模型分为 <br/>
  + Young，年轻代（易被 GC）<br/>
  Young 区被划分为三部分，Eden 区和两个大小严格相同的 Survivor 区，其中 Survivor 区间中，某一时刻只有其中一个是被使用的，另外一个留做垃圾收集时复制对象用，在 Young 区间变满的时候，minor GC 就会将存活的对象移到空闲的Survivor 区间中，根据 JVM 的策略，在经过几次垃圾收集后，任然存活于 Survivor 的对象将被移动到 Tenured 区间
  + Tenured（终身代） <br/>
  Tenured 区主要保存生命周期长的对象，一般是一些老的对象，当一些对象在 Young 复制转移一定的次数以后，对象就会被转移到 Tenured 区，一般如果系统中用了 application 级别的缓存，缓存中的对象往往会被转移到这一区间
  + Perm（永久代） <br/>
  Perm 主要保存 class,method,filed 对象，这部门的空间一般不会溢出，除非一次性加载了很多的类，不过在涉及到热部署的应用服务器的时候，有时候会遇到 java.lang.OutOfMemoryError : PermGen space 的错误，造成这个错误的很大原因就有可能是每次都重新部署，但是重新部署后，类的 class 没有被卸载掉，这样就造成了大量的 class 对象保存在了 perm 中，这种情况下，一般重新启动应用服务器可以解决问题
- Linux 修改 /usr/program/tomcat7/bin/catalina.sh 文件，把下面信息添加到文件第一行。Windows 和 Linux 有点不一样的地方在于，在 Linux 下，下面的的参数值是被引号包围的，而 Windows 不需要引号包围 <br/>
  + 如果服务器只运行一个 Tomcat <br/>
  机子内存如果是 8G，一般 PermSize 配置是主要保证系统能稳定起来就行
  ```
  JAVA_OPTS="-Dfile.encoding=UTF-8 -server -Xms6144m -Xmx6144m -XX:NewSize=1024m -XX:MaxNewSize=2048m -XX:PermSize=512m -XX:MaxPermSize=512m -XX:MaxTenuringThreshold=10 -XX:NewRatio=2 -XX:+DisableExplicitGC"
  ```
  机子内存如果是 16G，一般 PermSize 配置是主要保证系统能稳定起来就行
  ```
  JAVA_OPTS="-Dfile.encoding=UTF-8 -server -Xms13312m -Xmx13312m -XX:NewSize=3072m -XX:MaxNewSize=4096m -XX:PermSize=512m -XX:MaxPermSize=512m -XX:MaxTenuringThreshold=10 -XX:NewRatio=2 -XX:+DisableExplicitGC"
  ```
  机子内存如果是 32G，一般 PermSize 配置是主要保证系统能稳定起来就行
  ```
  JAVA_OPTS="-Dfile.encoding=UTF-8 -server -Xms29696m -Xmx29696m -XX:NewSize=6144m -XX:MaxNewSize=9216m -XX:PermSize=1024m -XX:MaxPermSize=1024m -XX:MaxTenuringThreshold=10 -XX:NewRatio=2 -XX:+DisableExplicitGC"
  ```
  如果是开发机
  ```
  -Xms550m -Xmx1250m -XX:PermSize=550m -XX:MaxPermSize=1250m
  ```
  + 参考说明
  ```
  -Dfile.encoding：默认文件编码
  -server：表示这是应用于服务器的配置，JVM 内部会有特殊处理的
  -Xmx1024m：设置JVM最大可用内存为1024MB
  -Xms1024m：设置JVM最小内存为1024m。此值可以设置与-Xmx相同，以避免每次垃圾回收完成后JVM重新分配内存。
  -XX:NewSize：设置年轻代大小
  -XX:MaxNewSize：设置最大的年轻代大小
  -XX:PermSize：设置永久代大小
  -XX:MaxPermSize：设置最大永久代大小
  -XX:NewRatio=4：设置年轻代（包括 Eden 和两个 Survivor 区）与终身代的比值（除去永久代）。设置为 4，则年轻代与终身代所占比值为 1：4，年轻代占整个堆栈的 1/5
  -XX:MaxTenuringThreshold=10：设置垃圾最大年龄，默认为：15。如果设置为 0 的话，则年轻代对象不经过 Survivor 区，直接进入年老代。对于年老代比较多的应用，可以提高效率。如果将此值设置为一个较大值，则年轻代对象会在 Survivor 区进行多次复制，这样可以增加对象再年轻代的存活时间，增加在年轻代即被回收的概论。
  -XX:+DisableExplicitGC：这个将会忽略手动调用 GC 的代码使得 System.gc() 的调用就会变成一个空调用，完全不会触发任何 GC
  ```