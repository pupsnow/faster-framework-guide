# 快速开始

本章将为您介绍如果快速集成Admin框架，简单快捷的开发一个后台管理接口。

## 引入


```
<dependency>
    <groupId>cn.org.faster</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>cn.org.faster</groupId>
    <artifactId>spring-boot-starter-mybatis</artifactId>
</dependency>
<dependency>
    <groupId>cn.org.faster</groupId>
    <artifactId>spring-boot-starter-admin</artifactId>
</dependency>
```


## 配置

您需要在application.yml中引入admin组件的配置文件。如下：

```
spring:
  profiles:
    include:
      - web
      - mybatis
      - admin
```

## 运行

接下来您只需要建立一个普通的SpringBoot启动类即可：

```
@SpringBootApplication
public class TestApplication {
    public static void main(String[] args) {
        SpringApplication.run(TestApplication.class,args);
    }
}
```

## 验证

项目启动后，您可以通过访问
http://127.0.0.1:8080/sys/users
验证权限系统是否生效。

您可以通过POST请求访问http://127.0.0.1:8080/login进行登录

## 接口

由于默认提供的接口过多，我们整理了一份接口文档供您查阅。

您可以进入[接口文档](api-doc.md)了解admin所提供的所有接口。

## 数据库

您可以通过查看[github源码](https://github.com/faster-framework/faster-framework-project/tree/master/faster-framework/faster-admin/db)中的sql文件，来创建Admin所需的数据表。

