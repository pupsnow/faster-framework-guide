# 模块列表
用户可通过使用parent标签管理引入faster依赖管理。

```
<parent>
    <groupId>cn.org.faster</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${最新版本}</version>
</parent>
```

此后用户便可直接引入依赖而无需关心版本.

```
 <dependencies>
        <dependency>
            <groupId>cn.org.faster</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>cn.org.faster</groupId>
            <artifactId>spring-boot-starter-mybatis</artifactId>
        </dependency>
</dependencies>
    ...
    ...
    ...
```

模块名称|描述
---|---
spring-boot-starter-parent| 父级引用管理，引入后版本信息交由其管理
spring-boot-starter-web|web常用配置及组件
spring-boot-starter-mybatis|mybatis基础配置
spring-boot-starter-admin|后台管理接口脚手架
spring-boot-starter-redis|redis支持
spring-boot-starter-sms|短信模块

