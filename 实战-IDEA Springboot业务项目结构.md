# 实战-IDEA Springboot业务项目结构

总结一个构件业务模块项目结构的总结，仅供参考：



BusinessModule 每个单一的业务模块

​	|——error 通过AOP全局捕获异常，并保存文件

​	|——log 通过AOP全局记录log，并保存文件

​	|——config 配置信息类，Springboot依赖注入类等等与配置相关的

​	|——bean Java实体类

​	|——vm 用于controller接入数据和输出数据的一些实体类

​	|——mapper 与数据库的映射，ORM框架的实现

​	|——service 业务逻辑类抽象出的接口

​	|——impl 业务逻辑接口的具体实现

​	|——controller 提供API或者网页的类

​	|——util 工厂类

​	|——http 微服务之间通信功能的封装类

​	|——async 涉及到异步的方法封装

EurekaServiceModule 实现服务发现即注册相关功能，可以有多个Module组成注册中心集群

ZuulServiceModule 网关模组，实现过滤和路由，集成SwaggerUI API文档等

ConfigServiceModule 配置中心模组，独立出配置文件，实现配置动态加载及更新

HystrixServiceModule 熔断和降级模块，处理单个或多个服务出现故障的情况，防止服务雪崩

AuthServiceModule 鉴权模块

WebSocketModule 独立的WebSocket模块

RabbitMQModule 消息总线模组