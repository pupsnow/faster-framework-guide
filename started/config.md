# 配置文件

上一小节我们编写了简单的入门程序。本节我们将介绍faster中常用的配置。

## application.yml

faster使用spring boot推荐的.yml文件作为项目配置文件。

> yml文件使用yaml语法编写。YAML发音：/ˈjæməl/（yeaimao，建议搜下yutube~)

在resources目录下新建application.yml

我们引入常用的组件：web、myabtis

```
spring:
  profiles:
    include:
      - web
      - mybatis

```

如果您引入了mybatis，则需要配置数据库连接：

```
spring:
  datasource:
    druid:
        url: jdbc:mysql://{domain}:3306/{database}?allowMultiQueries=true&useSSL=true
        username: {username}
        password: {signPwd}
        public-key: {publick-key}
```

数据库密码我们使用druid提供的加密方式进行加密，password与public-key可通过ConfigTools.main()方法获得。具体参见：[如何在Spring Boot中配置数据库密码加密](https://github.com/alibaba/druid/wiki/%E5%A6%82%E4%BD%95%E5%9C%A8Spring-Boot%E4%B8%AD%E9%85%8D%E7%BD%AE%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AF%86%E7%A0%81%E5%8A%A0%E5%AF%86%EF%BC%9F)

## 可引入的配置

faster-framework yml中目前可include的配置可参见[可引入配置列表](/appendix/include-config-list.html)

## faster框架配置

faster提供了一些内置的配置可以对faster框架本身进行配置。

您可以在application.yml文件中输入faster即可看到以faster开头的所有配置。

> 下节将为您介绍如何使用faster快速开发一个简单的增删改查接口。
