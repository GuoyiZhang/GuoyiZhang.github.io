# 【Spring Boot】SpringBoot 整合 apollo


## 简介

Apollo（阿波罗）是携程框架部门研发的分布式配置中心，能够集中化管理应用不同环境、不同集群的配置，配置修改后能够实时推送到应用端，并且具备规范的权限、流程治理等特性，适用于微服务配置管理场景。

## Apollo和Spring Cloud Config对比

![](https://img-blog.csdnimg.cn/img_convert/c68a03a7d4f4aac74fc2c79fd763a953.png)

通过对比，可以看出，生成环境中 Apollo 相比 Spring Cloud Config 更具有优势一些。

## 安装 Apollo 配置中心

### 搭建教程

参照 [https://github.com/ctripcorp/apollo/wiki/Quick-Start](https://github.com/ctripcorp/apollo/wiki/Quick-Start) 搭建 Apollo 配置中心，文档写的很清楚，这里就赘述了。

### 查看样例配置

搭建完成并启动后，访问 [http://localhost:8070](http://localhost:8070/) ，界面如下。

![](https://img-blog.csdnimg.cn/img_convert/d16609d6b75a3f9cdc2ffb84e1a7ae56.png)

输入用户名 apollo，密码 admin 后登录后，点击SampleApp进入配置界面。

![](https://img-blog.csdnimg.cn/img_convert/c3892d4560722145f2f82bf7499abf19.png)

## 与 Spring Boot 整合使用

创建一个springboot项目，主要代码如下。

### pom.xml

添加 Apollo 客户端的依赖，为了编码方便引入commons-lang3。
<dependency> <groupId>com.ctrip.framework.apollo</groupId> <artifactId>apollo-client</artifactId> <version>1.3.0</version> </dependency> <!-- 为了编码方便，并非apollo 必须的依赖 --> <dependency> <groupId>org.apache.commons</groupId> <artifactId>commons-lang3</artifactId> <version>3.8.1</version> </dependency>

### application.yml

server: port: 8761 app: id: springboot-apollo apollo: meta: http://127.0.0.1:8080 bootstrap: enabled: true eagerLoad: enabled: true logging: level: com: gf: controller: debug

配置说明：

* **app.id**：AppId是应用的身份信息，是配置中心获取配置的一个重要信息。
* **apollo.bootstrap.enabled**：在应用启动阶段，向Spring容器注入被托管的application.properties文件的配置信息。
* **apollo.bootstrap.eagerLoad.enabled**：将Apollo配置加载提到初始化日志系统之前。
* **logging.level.com.gf.controller**：调整 controller 包的 log 级别，为了后面演示在配置中心动态配置日志级别。

### HelloController

@RestController public class HelloController { private static Logger logger = LoggerFactory.getLogger( HelloController.class ); @Value( "${server.port}" ) String port; @GetMapping("hi") public String hi(String name) { logger.debug( "debug log..." ); logger.info( "info log..." ); logger.warn( "warn log..." ); return "hi " + name + " ,i am from port:" + port; } }

### 启动类

@SpringBootApplication @EnableApolloConfig public class SpringbootApolloApplication { public static void main(String[] args) { SpringApplication.run( SpringbootApolloApplication.class, args ); } }

启动项目。现在需要去配置中心做些关于这个springboot客户端的一些配置。

### 配置中心的配置

创建项目

第一步：访问http://localhost:8070 登录后，选择创建项目。

![](https://img-blog.csdnimg.cn/img_convert/c3ea9cb9999dc842ae64b76ca0272d10.png)

第二步：填写配置信息。

![](https://img-blog.csdnimg.cn/img_convert/3d9b8feff509e5a8f296bf8cab6e52a7.png)

配置说明：

* **部门**：选择应用所在的部门。（想自定义部门，参照官方文档，这里就选择样例）
* **应用AppId**：用来标识应用身份的唯一id，格式为string，需要和客户端。application.properties中配置的app.id对应。
* **应用名称**：应用名，仅用于界面展示。
* **应用负责人**：选择的人默认会成为该项目的管理员，具备项目权限管理、集群创建、Namespace创建等权限。

提交配置后会出现如下项目配置的管理页面。

添加配置项

![](https://img-blog.csdnimg.cn/img_convert/7e18aee1ce4393ae4f8ada728a602726.png)

第一步：点击 “新增配置”，配置需要管理的 application.properties 中的属性。

![](https://img-blog.csdnimg.cn/img_convert/58874a7a1fec9ccc182afce5847d1774.png)

点击提交，之后按照同样的方法，新增需要动态管理的 application.properties 中的属性。

提交后，跳转到配置的管理界面：

![](https://img-blog.csdnimg.cn/img_convert/ebbdfd441cc64c4a5884e00f32ebaf33.png)

发布配置

配置只有在发布后才会真的被应用使用到，所以在编辑完配置后，需要发布配置。点击“发布按钮”。

填写发布相关信息，点击发布 。

![](https://img-blog.csdnimg.cn/img_convert/4b370f469bc80264434cdff540f2ded8.png)

### 测试

在配置中心，修改 server.port 的值为 8762 并发布。

![](https://img-blog.csdnimg.cn/img_convert/30e7d0fa1740d6e6011a321728bea70c.png)

Postman 访问之前写个测试接口 [http://127.0.0.1:8761/hi?name=zhangsan](http://127.0.0.1:8761/hi?name=zhangsan) ，返回如下。

![](https://img-blog.csdnimg.cn/img_convert/ce2713ca07a6b9e933395eba88269d9c.png)

说明 客户端 获取到了 配置中心修改后的 server.port 的值 。

注意：

* 服务的端口依然还是 8761，这是因为 apollo 修改配置，不会像Spring Cloud Config 那样去重启应用。
* 重启应用后，客户端会加载使用 配置中心中配置的属性的值，例如我们重启我们的应用，服务端口会变成8762。

![](https://img-blog.csdnimg.cn/img_convert/886ab3a8bfe7a04759189a59ada340ca.png)

### 监听配置的变化

需求

日志模块是每个项目中必须的，用来记录程序运行中的相关信息。一般在开发环境下使用DEBUG级别的日志输出，为了方便查看问题，而在线上一般都使用INFO或者ERROR级别的日志，主要记录业务操作或者错误的日志。那么问题来了，当线上环境出现问题希望输出DEBUG日志信息辅助排查的时候怎么办呢？修改配置文件，重新打包然后上传重启线上环境，以前确实是这么做的。

虽然上面我们已经把日志的配置部署到Apollo配置中心，但在配置中心修改日志等级，依然需要重启应用才生效，下面我们就通过监听配置的变化，来达到热更新的效果。
@Configuration public class LoggerConfig { private static final Logger logger = LoggerFactory.getLogger(LoggerConfig.class); private static final String LOGGER_TAG = "logging.level."; @Autowired private LoggingSystem loggingSystem; @ApolloConfig private Config config; @ApolloConfigChangeListener private void configChangeListter(ConfigChangeEvent changeEvent) { refreshLoggingLevels(); } @PostConstruct private void refreshLoggingLevels() { Set<String> keyNames = config.getPropertyNames(); for (String key : keyNames) { if (StringUtils.containsIgnoreCase(key, LOGGER_TAG)) { String strLevel = config.getProperty(key, "info"); LogLevel level = LogLevel.valueOf(strLevel.toUpperCase()); loggingSystem.setLogLevel(key.replace(LOGGER_TAG, ""), level); logger.info("{}:{}", key, strLevel); } } } }

关键点讲解：

* **@ApolloConfig注解**：将Apollo服务端的中的配置注入这个类中。
* **@ApolloConfigChangeListener注解**：监听配置中心配置的更新事件，若该事件发生，则调用refreshLoggingLevels方法，处理该事件。
* **ConfigChangeEvent参数**：可以获取被修改配置项的key集合，以及被修改配置项的新值、旧值和修改类型等信息。

application.yml 中配置的日志级别是 debug ，访问http://127.0.0.1:8761/hi?name=zhangsan ，控制台打印如下。
2019-03-05 18:29:22.673 DEBUG 4264 --- [nio-8762-exec-1] com.gf.controller.HelloController : debug log... 2019-03-05 18:29:22.673 INFO 4264 --- [nio-8762-exec-1] com.gf.controller.HelloController : info log... 2019-03-05 18:29:22.673 WARN 4264 --- [nio-8762-exec-1] com.gf.controller.HelloController : warn log...

现在在配置中心修改日志级别为 warn。

![](https://img-blog.csdnimg.cn/img_convert/78635655a3f27bbca2a807954bfddde7.png)

再次访问http://127.0.0.1:8761/hi?name=zhangsan ，控制台打印如下。
2019-03-05 19:07:19.469 WARN 4264 --- [nio-8762-exec-3] com.gf.controller.HelloController : warn log...

说明日志级别的配置，已经支持热更新了。关于apollo 的更多应用 ，可以参照github的文档。

源码下载 : [https://github.com/gf-huanchupk/SpringBootLearning/tree/master/springboot-apollo](https://github.com/gf-huanchupk/SpringBootLearning/tree/master/springboot-apollo)

