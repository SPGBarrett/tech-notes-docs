# 实战-SpringCloud之Config动态配置管理

[TOC]

## Preface

SpringCloud的Config组件提供了动态获取配置文件的功能。通过构建Config-Server模组，可以实现从本地文件或者远端获取配置文件，然后其他的模块（也就是config的消费者，config-client）可以从config-server获取到这些配置信息。从而实现配置文件和jar包的分离，便可实现动态修改配置信息，而不需要重新打包或者启动服务。



## Config-Server实现

### 第一步：新建项目或Module

使用IDEA的Spring Initializer新建一个简单的项目或者Module。



### 第二步：引入依赖

在pom.xml文件中添加Zuul组件需要的依赖：

```xml
		<!-- Dependencies for config server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
            <version>2.2.2.RELEASE</version>
        </dependency>
```



### 第三步：开启Config服务功能

进入Application启动类，加入@EnableConfigServer注解：

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer
@EnableDiscoveryClient
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }

}
```

### 第四步： 建立远程仓库

使用github或者gitee建立一个远程仓库。仓库中放入.properties文件，并在其中存入相应配置。文件的命名推荐使用`{application-name}-{profile}.properties`的方式，例如`spring-demo-dev.properties`，其中`spring-demo`是一个名字，用于标记，config-server的消费者可以通过这个名字定位到相应的文件，-dev代表开发环境的参数配置文件，类似的还有-prod，-stable，-test等等。

仓库和文件都建立好了之后，记下仓库的uri，填入下一章说明的配置文件中。



### 第五步： 参数配置

在application.properties文件中添加参数配置，基本的参数配置及说明如下：

```properties
server.port=12052

spring.application.name=config-server
######################################## For Config ###################################
spring.cloud.config.server.git.uri=https://gitee.com/spgbarrett/spring-config.git
spring.cloud.config.server.git.username=xxxxxx
spring.cloud.config.server.git.password=xxxxxx

#spring.cloud.config.server.git.default-label=master #配置文件分支
#spring.cloud.config.server.git.search-paths=spring-config  #配置文件所在根目录

######################################## For Eureka ###################################
# The default URL that can access Eureka dashboard:
eureka.client.register-with-eureka=true
# Client fetch:
eureka.client.fetch-registry=true
# 每间隔5s，向服务端发送一次心跳，证明自己依然存活
eureka.instance.lease-renewal-interval-in-seconds=5
# 告诉服务端，如果我10s之内没有给你发心跳，将我踢出掉。
eureka.instance.lease-expiration-duration-in-seconds=10

eureka.client.service-url.defaultZone=http://localhost:5800/eureka/
```

一些简单的说明：

如上面的代码所示，主需要配置远程仓库的路径，登录远程仓库的账号和密码，就能实现最基本的配置了。除此之外，还可以进行默认分支选择、配置文件所在路径的配置等等。

完成配置后，启动config-server，就能通过config-server访问到远程仓库中的文件信息了。例如，远程仓库中有名为spring-demo-dev.properties的文件，那么通过http://localhost:12052/spring-demo-dev.properties这个路径就能在浏览器中访问到文件了。如果文件成功访问到，说明config-server配置成功。访问文件的url还有如下一些方式：

```
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
```

其中`application` 是应用名称，profile是配置类型，对应了dev，stable，prod等等，label 是分支，默认值是master。



### 使用本地配置文件

除了使用git的远程配置文件之外，还可以直接使用本地的配置文件。在配置文件中，用如下代码：

```.properties
spring.profiles.active=native
# While using local files, the route must leverage "/" rather than "\\"
spring.cloud.config.server.native.search-locations=E:/CodingProjects/SpringConfigRepo/spring-config
# Or you can create a config folder under the resource folder and put files in it, thus use:
#spring.cloud.config.server.native.search-locations=classpath:/configs
```

如上可以发现，可以在resource文件夹下新建一个文件夹，然后用classpath定位到这个文件。也可以使用绝对路径定位到外部的文件。



## Config-Client实现

### 第一步：新建项目或Module

使用IDEA的Spring Initializer新建一个简单的项目或者Module。



### 第二步：引入依赖

在pom.xml文件中添加Zuul组件需要的依赖：

```xml
		<!--config client依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
            <version>2.2.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```



### 第三步： 参数配置

在application.properties文件中添加参数配置，基本的参数配置及说明如下：

```properties
spring.application.name=spring-sub-demo

#Set up config client settings:
#对应config server Url中的{application}, 那么这里就是访问到spring-subdemo-dev.properties或者yml文件
spring.cloud.config.name=spring-subdemo
#配置环境，对应config server Url中的{profile}
spring.cloud.config.profile=dev
#label: trunk #配置分支(不配置则默认：git则是master,svn则是trunk)，
spring.cloud.config.uri=http://localhost:12052
```



### Config-Client自动更新

Spring Cloud Config  在项目启动时加载配置内容这一机制，导致了它存在一个缺陷，修改配置文件内容后，不会自动刷新。例如我们上面的项目，当服务已经启动的时候，去修改  github 上的配置文件内容，这时候，再次刷新页面，对不起，还是旧的配置内容，新内容不会主动刷新过来。
 但是，总不能每次修改了配置后重启服务吧。如果是那样的话，还是不要用它了为好，直接用本地配置文件岂不是更快。

它提供了一个刷新机制，但是需要我们主动触发。那就是 @RefreshScope 注解并结合 actuator ，注意要引入 spring-boot-starter-actuator 包。

1、在 config client 端配置中增加 actuator 配置，上面大家可能就注意到了。

```yaml
management:
  endpoint:
    shutdown:
      enabled: false
  endpoints:
    web:
      exposure:
        include: "*"
```

其实这里主要用到的是 refresh 这个接口

2、在需要读取配置的类上增加 @RefreshScope 注解，我们是 controller 中使用配置，所以加在 controller 中。

```java
@RestController
@RefreshScope
public class GitController {

    @Autowired
    private GitConfig gitConfig;

    @Autowired
    private GitAutoRefreshConfig gitAutoRefreshConfig;

    @GetMapping(value = "show")
    public Object show(){
        return gitConfig;
    }

    @GetMapping(value = "autoShow")
    public Object autoShow(){
        return gitAutoRefreshConfig;
    }
}
```

注意，以上都是在 client 端做的修改。

之后，重启 client 端，重启后，我们修改 github 上的配置文件内容，并提交更改，再次刷新页面，没有反应。没有问题。

接下来，我们发送 POST 请求到 http://localhost:3302/actuator/refresh 这个接口，用 postman 之类的工具即可，此接口就是用来触发加载新配置的，返回内容如下:

```json
[
    "config.client.version",
    "data.env"
]
```

之后，再次访问 RESTful 接口，http://localhost:3302/autoShow 这个接口获取的数据已经是最新的了，说明 refresh 机制起作用了。

而 http://localhost:3302/show 获取的还是旧数据，这与 @Value 注解的实现有关，所以，我们在项目中就不要使用这种方式加载配置了。

*在 github 中配置 Webhook*

这就结束了吗，并没有，总不能每次改了配置后，就用 postman 访问一下 refresh 接口吧，还是不够方便呀。 github 提供了一种 webhook 的方式，当有代码变更的时候，会调用我们设置的地址，来实现我们想达到的目的。

1、进入 github 仓库配置页面，选择 Webhooks ，并点击 add webhook；
 ![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725090559970-931500557.png)

2、之后填上回调的地址，也就是上面提到的 actuator/refresh 这个地址，但是必须保证这个地址是可以被 github  访问到的。如果是内网就没办法了。这也仅仅是个演示，一般公司内的项目都会有自己的代码管理工具，例如自建的 gitlab，gitlab 也有  webhook 的功能，这样就可以调用到内网的地址了。

![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725090614229-390401707.png)

*使用 Spring Cloud Bus 来自动刷新多个端*

> Spring Cloud Bus 将分布式系统的节点与轻量级消息代理链接。这可以用于广播状态更改（例如配置更改）或其他管理指令。一个关键的想法是，Bus 就像一个扩展的 Spring Boot 应用程序的分布式执行器，但也可以用作应用程序之间的通信渠道。
>
> —— Spring Cloud Bus 官方解释

如果只有一个 client 端的话，那我们用 webhook  ，设置手动刷新都不算太费事，但是如果端比较多的话呢，一个一个去手动刷新未免有点复杂。这样的话，我们可以借助 Spring Cloud Bus  的广播功能，让 client 端都订阅配置更新事件，当配置更新时，触发其中一个端的更新事件，Spring Cloud Bus  就把此事件广播到其他订阅端，以此来达到批量更新。

1、Spring Cloud Bus 核心原理其实就是利用消息队列做广播，所以要先有个消息队列，目前官方支持 RabbitMQ 和 kafka。

这里用的是 RabbitMQ， 所以先要搭一套 RabbitMQ 环境。请自行准备环境，这里不再赘述。我是用 docker 直接创建的，然后安装了 rabbitmq-management 插件，这样就可以在浏览器访问 15672 查看 UI 管理界面了。

2、在 client 端增加相关的包，注意，只在 client 端引入就可以。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

3、在配置文件中增加 RabbitMQ 相关配置，默认的端口应该是 5672 ，因为我是用 docker 创建的，所以有所不同。

```yaml
spring:
  rabbitmq:
    host: localhost
    port: 32775
    username: guest
    password: guest
```

4、启动两个或多个 client 端，准备来做个测试

在启动的时候分别加上 vm option：-Dserver.port=3302 和 -Dserver.port=3303 ，然后分别启动就可以了。

![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725090731099-791914304.png)

5、分别打开 http://localhost:3302/autoShow 和 http://localhost:3303/autoShow，查看内容，然后修改 github 上配置文件的内容并提交。再次访问这两个地址，数据没有变化。

6、访问其中一个的 actuator/bus-refresh 地址，注意还是要用 POST 方式访问。之后查看控制台输出，会看到这两个端都有一条这样的日志输出

```
o.s.cloud.bus.event.RefreshListener: Received remote refresh request. Keys refreshed
```

7、再次访问第 5 步的两个地址，会看到内容都已经更新为修改后的数据了。

综上所述，当我们修改配置后，使用 webhook ，或者手动触发的方式 POST 请求一个 client 端的 actuator/bus-refresh 接口，就可以更新给所有端了。



### 通过Eureka实现Config配置

*配置 Spring Cloud Config 客户端*

客户端的配置相对来说变动大一点，加入了 Eureka 之后，就不用再直接和配置中心服务端打交道了，要通过 Eureka 来访问。另外，还是要注意客户端的 application 名称要和 github 中配置文件的名称一致。

1、pom 中引入相关的包

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

2、配置文件

bootstrap.yml

```yaml
eureka:
  client:
    serviceUrl:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://test:123456@localhost:3000/eureka/
  instance:
    preferIpAddress: true


spring:
  profiles:
    active: dev

---
spring:
  profiles: prod
  application:
    name: config-eureka-client
  cloud:
     config:
       label: master
       profile: prod
       discovery:
         enabled: true
         service-id: config-eureka-server


---
spring:
  profiles: dev
  application:
    name: config-eureka-client
  cloud:
     config:
       label: master  #指定仓库分支
       profile: dev   #指定版本 本例中建立了dev 和 prod两个版本
       discovery:
          enabled: true  # 开启
          service-id: config-eureka-server # 指定配置中心服务端的server-id 
```

除了注册到 Eureka 的配置外，就是配置和配置中心服务端建立关系。

其中 service-id 也就是服务端的application name。

application.yml

```yaml
server:
  port: 3011
management:
  endpoint:
    shutdown:
      enabled: false
  endpoints:
    web:
      exposure:
        include: "*"

data:
  env: NaN
  user:
    username: NaN
    password: NaN
```

3、启动类，增加了 @EnableEurekaClient 注解，可以用 @EnableDiscoveryClient 代替

```java
@SpringBootApplication
@EnableEurekaClient
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

4、另外的配置实体类和 RESTController 和上面的一样，没有任何更改，直接参考即可。

5、启动 client 端，访问 http://localhost:3011/autoShow，即可看到配置文件内容。

这个例子只是介绍了和 Eureka 结合的最基础的情况，还可以注册到高可用的 Eureka 注册中心，另外，配置中心服务端还可以注册多个实例，同时保证注册中心的高可用。



## 小结

Config主要实现了配置信息和代码的解耦，使得更改配置的时候，无需修改代码和重新打包。

