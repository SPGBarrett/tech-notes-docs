# 实战-SpringCloud之Eureka注册中心组件

[TOC]

## Preface

Eureka是Spring Cloud Netflix中用于实现注册中心功能的组件。能够实现类似功能的还有：Consul、Zookeeper、Nacos等组件。本文主要对Eureka注册中心的搭建及服务下线的通知做详尽说明。



## 代码实现

### 第一步：新建项目或Module

使用IDEA的Spring Initializer新建一个Module。



### 第二步：引入依赖

在提供注册中心服务的Module中的pom.xml文件中添加Eureka组件需要的依赖：

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>	
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
```

在需要在注册中心注册的Module的pom.xml文件中，添加依赖：

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>2.2.2.RELEASE</version>
        </dependency>
```



### 第三步：开启网关功能

进入Application启动类，加入@EnableZuulProxy注解：

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy
public class ZuulGatewayApplication {

    public static void main(String[] args) {
        SpringApplication.run(ZuulGatewayApplication.class, args);
    }

}
```

由于Zuul内置Hystrix，若需要实现基本的熔断和降级，直接通过实现FallbackProvider接口实现，可以新建一个ServiceFallbackProvider类，并添加如下代码：

```java
package com.barrett.zuulgateway;

import org.springframework.cloud.netflix.zuul.filters.route.FallbackProvider;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.client.ClientHttpResponse;
import org.springframework.stereotype.Component;

import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;

/**
 * @program: smartsite-master
 * @description: The implementation of Hystrix circuit breaker mechanism
 * We should config hystrix in application.properties and implement this class
 * @author: Barrett
 * @create: 2020-05-15 16:03
 **/
@Component
public class ServiceFallbackProvider implements FallbackProvider {
    @Override
    public String getRoute() {
        //设置熔断的服务名
        //如果是所有服务则设置为*
        return "ai-alert-service";
    }

    @Override
    public ClientHttpResponse fallbackResponse(String route, Throwable cause) {
        return new ClientHttpResponse(){

            @Override
            public HttpHeaders getHeaders() {
                HttpHeaders headers = new HttpHeaders();
                headers.setContentType(MediaType.APPLICATION_JSON);
                return headers;
            }

            @Override
            public InputStream getBody() throws IOException {
                String returnString = "{'code':000,'message': server error, circuit breaker hystrix in effect}";
                return new ByteArrayInputStream(returnString.getBytes());
            }

            @Override
            public HttpStatus getStatusCode() throws IOException {
                return HttpStatus.OK;
            }

            @Override
            public int getRawStatusCode() throws IOException {
                return 200;
            }

            @Override
            public String getStatusText() throws IOException {
                    return "{code:404,message:service error =_=}";
            }

            @Override
            public void close() {

            }
        };
    }
}

```



### 第四步： 参数配置

在application.properties文件中添加参数配置，基本的参数配置及说明如下：

```properties
##Set application name and port:
spring.application.name=zuul-gateway
server.port=5801

##Eureka settings, register zuul itself to Eureka:
eureka.instance.prefer-ip-address=true
eureka.client.register-with-eureka=true
eureka.client.fetch-registry=true
eureka.client.serviceUrl.defaultZone=http://localhost:5800/eureka/

##Zuul gateway poxy settings:
zuul.host.connect-timeout-millis=10000
zuul.host.socket-timeout-millis=20000
zuul.host.max-per-route-connections=50
zuul.host.max-total-connections=400

# Circuit Breaker settings: (Using Zuul embedded Hystrix)
hystrix.command.default.execution.isolation.thread.timeout-in-milliseconds=6000

##Load Balance Settings:
# Zuul embedded Ribbon Settings:
ribbon.connection-timeout=500
ribbon.read-timeout=2000

# The route strategy: Assigning the app-name to another certain name. You can also annotate this and use the original app-name
# All the services that will be loadbalanced by zuul should be listed here
zuul.routes.ai-alert-service.path=/ai-alert-service/** 
zuul.routes.face-detect-service.path=/face-detect-service/**
zuul.routes.smartsite-service.path=/smartsite-service/**
zuul.routes.patrolmodule-service.path=/patrolmodule-service/**
#Set a prefix that will be applied to all paths:
zuul.prefix=/zb-api
# If Eureka is not working, the subsequent code should be added so that Zuul can find url by app-name
#zuul.routes.ai-alert-service.url=http://127.0.0.1:6002

#Disable original access:
#Disable all services:
zuul.ignored-services=*
#Disable specified services using app name:
#zuul.ignored-services=face-detect-service
#zuul.ignored-services=smartsite-service
```

一些简单的说明：

`zuul.routes.ai-alert-service.path=/ai-alert-service/** `这句就是说为名字为`ai-alert-service`的服务设置了访问路径，由于设置了`zuul.prefix=/zb-api`，所以路径前还需要加上这个前缀。`zuul.ignored-services="*"`这句话设置了无法通过默认路由路径（默认路由路径就是以app-name作为路径，如/ai-alert-service/** ）访问服务，只能通过网关配置的路由的方式访问（例子中自定义配置的和默认的是一样的，都是/ai-alert-service/** ，当然也可以配置成别的路径）。如果不设置这个，则两种方式都可访问到服务。另外，Zuul需要通过Eureka获取到各个服务的url，如果没有使用Eureka，则必须在.properties文件中指定每个服务的url地址。

于是，假设Zuul运行端口为6100，服务的端口运行在6000。则访问`ai-alert-service`服务的

`test`API的路径由`主机IP：6000/test`变为`主机IP：6100/zb-api/ai-alert-service/test`



## 小结

Zuul组件功能的实现，和大部分Spring Cloud组件实现基本功能的流程一致，即`创建项目->引入依赖->通过注解开启功能->参数配置`这几步。Zuul模块服务运行之后，就能实现通过Zuul来访问到其他的服务了。