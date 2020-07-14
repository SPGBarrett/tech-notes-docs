# IDEA Springboot业务项目结构

总结一个构件业务模块项目结构的参考



BusinessModule

|——error 通过AOP全局捕获异常，并保存文件

|——log 通过AOP全局记录log，并保存文件

|——config 配置信息类，Springboot依赖注入类等等与配置相关的

|——bean Java实体类

|——vm 用于controller接入数据和输出数据的一些实体类

|——mapper 与数据库的映射，ORM框架的实现

|——service 业务逻辑类抽象出的接口

|——impl 业务逻辑接口的具体实现

|——controller 提供API或者网页的类

|——util 工厂类

|——http 微服务之间通信功能的封装类

|——async 涉及到异步的方法封装



