# 模块列表
用户可通过使用配置spring 插件：io.spring.dependency-management，引入parent。

```
plugins {
    id 'io.spring.dependency-management' version '1.0.5.RELEASE'
}
dependencyManagement {
    imports {
        mavenBom "cn.org.faster:faster-framework-spring-boot-starter-parent:${parentVersion}"
    }
}
```

用户可通过使用gradle dependencies compile引入子模块。

```
dependencies {
    compile "cn.org.faster:faster-framework-spring-boot-starter-core"
    compile "cn.org.faster:faster-framework-spring-boot-starter-admin"
    compile "cn.org.faster:faster-framework-spring-boot-starter-redis"
    compile "cn.org.faster:faster-framework-spring-boot-starter-sms"
    ....
    ....
}
```

模块名称|描述
---|---
faster-framework-spring-boot-starter-parent| 父级引用管理，引入后版本信息交由其管理
faster-framework-spring-boot-starter-core|核心包，所有项目必须引入
faster-framework-spring-boot-starter-admin|后台管理接口脚手架
faster-framework-spring-boot-starter-redis|redis支持
faster-framework-spring-boot-starter-sms|短信模块

