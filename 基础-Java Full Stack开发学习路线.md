# 基础-Java Full Stack开发学习路线

[TOC]

## Preface

主要这对Java后端开发为主，兼顾部分前端和运维知识的







## 1. 编程基础

### 1.1 Java语言

#### 1.1.1 语言基础

* 基础语法
* OOP面向对象编程思想
* 接口
* 异常
* 泛型
* 反射
* 注解
* I/O
* 图形化编程（例如Swing）

#### 1.1.2 JVM

* 类加载机制
* 字节码执行机制
* JVM内存模型
* GC垃圾回收
* JVM性能监控与故障定位
* JVM调优

#### 1.1.3 并发/多线程

* 并发编程基础
* 线程池
* 锁
* 并发容器
* 原子类
* JUC并发工具类

### 1.2 数据结构和算法

#### 1.2.1 数据结构

* 线性表
* 链表
* 数组
* 字符串
* 堆
* 栈、队列
* 树、二叉树、B树、B+树、红黑树...
* 图论
* 哈希表

#### 1.2.2 算法

* BFS，DFS
* 查找
* 排序
* 贪心算法
* 分治
* 动态规划
* 回溯

### 1.3 计算机网络

* 经典的七层/五层网络模型
* ARP协议
* IP/ICMP协议
* TCP/UDP协议
* DNS/HTTP/HTTPS协议
* Session/Cookie

### 1.4 数据库

* SQL语句书写
* SQL语句优化
* 事务及隔离级别
* 索引
* 锁

### 1.5 操作系统

* 进程、线程
* 并发、锁
* 内存管理和调度
* I/O 原理

### 1.6 设计模式

* 单例
* 工厂
* 代理
* 策略
* 模板方法
* 观察者
* 适配器
* 责任链
* 建造者



## 2. 开发工具

### 2.1 集成开发环境

* Eclipse
* JetBrain Idea
* VS Code

### 2.2 Linux系统

* Linux常用命令
* Shell脚本

### 2.3 代码管理工具

* Git
* SVN

### 2.4 项目管理/构建工具

* Maven
* Gradle



## 3. 应用及框架

### 3.1 Spring家族

* Spring：IOC，AOP
* SpringMVC
* SpringCloud
* SpringBoot

### 3.2 服务器软件

#### 3.2.1 Web服务器

* IIS
* Niginx

#### 3.2.2 应用服务器

* Tomcat
* Jetty
* Undertow

### 3.3 中间件

#### 3.3.1 缓存

* Redis：5大数据类型、事务、消息通知、管道、持久化、集群
* memchache

#### 3.3.2 消息队列

* RocketMQ
* RabbitMQ
* Kafka

#### 3.3.3 RPC架构

* Dubbo
* GRPC
* Thrift
* SpringCloud
* Netty

### 3.4 数据库

#### 3.4.1 ORM框架

* MyBatis
* Hibernate
* JPA

#### 3.4.2  连接池

* Druid
* HikariCP
* C3P0

#### 3.4.3 分库分表

* PageHelper
* MyCat
* Sharding-JDBC
* Sharding-Sphere

### 3.5 搜索引擎

* Solr
* ElasticSearch

### 3.6 分布式及微服务

#### 3.6.1 服务发现及注册

* Eureka
* Consul
* Zookeeper
* Nacos

#### 3.6.2  网关

* Zuul
* Gateway

#### 3.6.3 服务调用

* RestTemplate
* Feign

#### 3.6.4 负载均衡

* Feign
* Ribbon

#### 3.6.5 熔断/降级

* Hystrix

#### 3.6.6 配置中心

* Config （Netflix）
* Apollo （携程）
* Nacos （阿里巴巴）

#### 3.6.7 认证和鉴权

* Shiro
* SpringSecurity
* OAuth2
* SSO

#### 3.6.8 分布式事务

* JTA接口-Atomikos组件
* 2PC、3PC
* XA模式
* TCC模式：tcc-transaction; ByteTcc; EasyTransaction;Seata
* SAGA模式：ServiceComb；Seata
* LCN模式：tx-lcn

#### 3.6.9 任务调度

* Quartz
* Elastic-Job

#### 3.6.10 链路追踪与监控

* Zipkin
* Sleuth
* Skywalking

#### 3.6.11 日志分析及监控

* ELK Stack：ElasticSearch + Logstash + Kibana

#### 3.6.12 虚拟化/容器化技术

* 容器技术：Docker
* 容器编排：Docker Compose；Swarm；Kubernetes

### 3.7 基础前端开发知识

#### 3.7.1 基础

* HTML + CSS + JavaScript
* Jquery + Ajax

#### 3.7.2 模板框架

* JSP、JSTL
* Thymeleaf
* FreeMarker

#### 3.7.2 组件化开发

* Node.js
* Vue.js
* React
* Angular

### 3.8 基础运维知识

#### 3.8.1  Web服务器

* Nginx
* IIS

#### 3.8.2 应用服务器

* Tomcat
* Jetty
* Undertow

#### 3.8.3 CDN加速

#### 3.8.4 CI/CD 持续集成与发布

* Jenkins

#### 3.8.5 代码质量检查

* sonar

#### 3.8.6  日志收集/分析

* ELK Stack





## 4. 参考书

Java编程思想

Java并发编程实战

深入理解Java虚拟机

函数式编程思想

鸟哥的Linux私房菜

Redis入门指南

高性能MySQL

大话数据结构

大话设计模式

算法第四版



























