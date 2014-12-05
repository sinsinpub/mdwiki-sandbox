测试Dubbo的示例应用模板
=======================

### Features

1. 一共包含2个大类4个应用，两类分别为“使用Dubbo框架前”，以及“使用Dubbo框架后”。4个应用分别为：
2. 实现了1个只是回显程序当前状态的“主页”功能的微服务，它代表业务功能桩服务；
3. 实现了1个用于向后转发负载、对外提供服务的REST风格的前端服务，还可以兼职负载均衡；
4. 分别将上面2个服务用Dubbo框架加以改写，实现前后端之间更可靠的远程过程调用，令后端服务通过横向扩展自然得到能失败切换、有负载均衡的高可用服务；
5. 4个应用均为非JEE的Web应用(打包为WAR)，可以选择部署到任意Servlet容器或企业中间件中。也可只在JDK6的支持下使用Jetty简易启动；

### Directory Structures

* before - 使用Dubbo框架前的2个应用
 * backend-micro-service - 后端业务服务源代码，示例仍然使用了spring-mvc实现的类REST服务。
 * frontend-rest-service - 前端代理服务源代码，示例允许配置多个后端实现轮询负载均衡。也可以只配置一个后端地址，委托其它工具实现HA和均衡。
* after - 使用Dubbo框架后的2个应用
 * backend-micro-service - 后端业务服务源代码，虽然使用Dubbo之后不再使用HTTP协议，可以不再需要Web容器，但示例仍然演示了在Web应用中部署Dubbo协议服务。
 * frontend-rest-service - 前端代理服务源代码，示例将原本使用HttpClient转发消息的实现改为Dubbo服务消费者端。所有后端服务发现和均衡工作由Dubbo接管。
* jetty - 简易Servlet容器，内含打好包的应用和演示配置
* zookeeper - Dubbo官方推荐使用的服务元数据注册中心

### Usage

#### 使用jetty-runner运行
* 将需要运行的WAR包更新到jetty目录。
* 进入jetty目录，修正run-jetty脚本的JDK路径，然后执行以下命令：
```
  run-jetty filename.war [port] [path]
```
* filename.war是要运行的应用WAR包文件名；port是Jetty要监听的HTTP服务端口，默认8080；path是应用将要运行的Web上下文路径，默认app。
* 注意运行要编译JSP的before/backend-micro-service时，需要使用JDK所在的JVM来运行。故一定要调整run-jetty脚本内的JDK路径。
* 要运行多个后端应用实例需要打开多个控制台会话，并使用不同的配置（服务端口、路径、实例名等）来运行。
* 例如可以为每个实例创建一个新的子目录，将jetty-runner和WAR复制到其中，并在其中创建一个etc/config.properties配置文件。注意要为dubbo.application.name使用与其它实例不同的值
* 正常启动后可以使用http://host:port1/path1/greet/hello来访问前端服务，使用http://host:port2/path2/可以访问不用Dubbo协议的后端页面
* 要让Dubbo使用zookeeper注册中心，修改dubbo.registry.address配置之后，在启动应用前事先进入zookeeper/bin用zkServer脚本启动zookeeper即可。zk配置在conf目录，并会创建data目录存储日志和数据
* 更多Dubbo的使用和配置信息可以参考官方文档：http://alibaba.github.io/dubbo-doc-static/User+Guide-zh.htm ，以及官方FAQ：http://alibaba.github.io/dubbo-doc-static/FAQ-zh.htm
* 更多jetty-runner用法详细可参考http://www.eclipse.org/jetty/documentation/current/runner.html

#### 使用Web中间件运行
* 将WAR包部署到已经装备好的中间件中即可。部署前应事先为每个应用实例修改好包中的config.properties配置。

#### 从源码使用maven即时编译运行
* 在maven2/3环境准备好的前提下，只需要在相应应用源码目录执行mvn jetty:run等goal即可完成构建并启动Jetty容器运行
* 利用jetty-maven-plugin的详细方法，版本8的可参考http://wiki.eclipse.org/Jetty/Feature/Jetty_Maven_Plugin

### License
