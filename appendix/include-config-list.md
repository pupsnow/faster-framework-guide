# 可引入配置列表
用户可通过使用spring.profiles.include引入以下配置。

示例：

```
spring:
  profiles:
    include:
      - web
      - mybatis
```

配置名称|所需模块|描述
---|---|----
web|core|tomcat、mvc、日期、jackson等常用配置
mybatis|core|数据库连接池、加密、mybatis等常用配置
admin|admin|后台管理所需配置
redis|redis|redis连接池、数据库等常用配置
