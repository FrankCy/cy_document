# Tomcat 监控 #
### 常用Tomcat监控 ###
- Tomcat Manager（启动后的8080端口默认展开界面）

### 推荐 ###
![blockchain](https://raw.githubusercontent.com/psi-probe/psi-probe/master/src/site/resources/images/psi-probe-banner.jpg "Tomcat监控") <br/>
 [PsiProbe 链接 ：https://github.com/psi-probe/psi-probe](https://github.com/psi-probe/psi-probe)

### Building from Source （构建方式） ###
- Clone PSI Probe's git repository.</br>
  + Note: If you plan to contribute to PSI Probe, you should create your own fork on GitHub first and clone that. Otherwise, follow these steps to build the latest version of PSI Probe for yourself.

  + Execute the following command:

  ```
  git clone https://github.com/psi-probe/psi-probe
  ```

  + This will create directory called psi-probe. Subsequent steps will refer to this as "your PSI Probe base directory."

- Minimum JDK version required to run build is JDK7. Project still targets JDK6. The raise to JDK7 is a direct result of early Tomcat 9 support and maven plugins moving to JDK7.


- Download and install Maven 3.
  + You may download it from the [Apache Maven website.](https://maven.apache.org/download.cgi)

- Run Maven.
  + Execute the following command from your PSI Probe base directory:
  ```
  mvn package
  ```

  This will create a deployable file at ```web/target/probe.war.```<br/>


### 简介PsiProbe ###
#### 使用方法 ####
- 使用非常简单，从github的链接下载psi-probe的war包，部署到您本地的Tomcat服务器即可

#### 功能简介 ####
- 登陆界面

- 应用程序标签

- 日志

- 线程

- 系统信息

- 连接器