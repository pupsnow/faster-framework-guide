# 分包规范
良好的分包规范使我们得到一个清晰的代码结构。有利于代码的可扩展性。

我们建议大家采用以下分包规范：

- 包的起名不要使用复数，直接使用业务表名称即可。
- 反向域名+项目名，例: com.github.faster.test。其中com.github.faster为公司域名，test为项目名称。
- 建立业务功能包名。我们期望的大家能为业务功能进行分包，为功能业务统一建立上级包modules，为项目重构（如改造为微服务）打下良好的基础。例：com.github.faster.test.modules.user,com.github.faster.test.modules.role。
- 建立层级包名。我们期望大家能为各层进行分包。如controller、service等等。常用层级分包名称如下：

包名|内容、用途
--- |---
entity|与数据库表相对应的实体
controller|接口入口，用于验证入参，调用service
service|处理业务逻辑，调用mapper
mapper|数据库查询接口，实现BaseMapper(com.github.faster.framework.core.mybatis.mapper包下的)
error|存放接口返回的错误码，实现ErroCode接口
model|对外暴露（请求、返回，调用外部接口）的模型。
aop|Spring Aop类
task|Spring 定时任务类
inteceptor|Spring 拦截器
api|外部接口类。

> 您也可以根据您自己的理解进行包的建立。

- 对于分版本接口，我们建议您在modules下建立v1、v2等能表明版本的包名。如：com.github.faster.test.modules.v1.user;
- 每个包下所建立的类的命名应增加包名后缀，如UserController、UserService、UserEntity、UserTask等。
