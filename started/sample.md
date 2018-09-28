# 简单示例

本节将通过连接数据库为您展示如何通过faster快速开发接口。

## 新建项目

我们依然通过idea创建gradle项目，build.gradle文件如下：

```
plugins {
    id 'java'
    id 'org.springframework.boot' version '2.0.2.RELEASE'
}

group 'test-framework'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
    mavenCentral()
}
processResources {
    from('src/main/java') {
        include '**/*.xml'
    }
}

dependencies {
    compile "cn.org.faster:faster-framework-core-spring-boot-starter:1.0.0.RELEASE"
}

```

## 新建启动类

首先在java目录下创建cn.faster.test包，在其下创建TestApplication.java，代码如下：

```
package cn.faster.test;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author zhangbowen
 */
@SpringBootApplication
public class TestApplication {
    public static void main(String[] args) {
        SpringApplication.run(TestApplication.class);
    }
}


```

## 新建配置文件

在resources目录下新建application.yml配置文件，由于我们需要连接数据库，固需要配置数据库地址。代码如下：

```
spring:
  profiles:
    include:
      - web
      - mybatis
    active: dev
---
spring:
  profiles: dev
  datasource:
    druid:
        url: jdbc:mysql://{domain}:3306/{database}?allowMultiQueries=true&useSSL=true
        username: {username}
        password: {password}
```


## 建立业务包

包的建立我们希望您遵循良好的开发习惯，为此我们提供[开发规范](../rules/README.md)供您参考。您可以选择忽略。

我们建立功能user，对应功能包名如下：

```
cn.faster.test.modules.user.controller
cn.faster.test.modules.user.mapper
cn.faster.test.modules.user.mapper.xml
cn.faster.test.modules.user.entity
```

## 建立类

### 实体类

cn.faster.test.modules.user.entity.UserEntity.java

```
package cn.faster.test.modules.user.entity;

import cn.org.faster.framework.core.entity.BaseEntity;
import lombok.Getter;
import lombok.Setter;

/**
 * @author zhangbowen
 */
@Getter
@Setter
public class UserEntity extends BaseEntity {
    private String username;
}

```

### Mapper

cn.faster.test.modules.user.mapper.UserMapper.java

```
package cn.faster.test.modules.user.mapper;

import cn.faster.test.modules.user.entity.UserEntity;
import cn.org.faster.framework.core.mybatis.mapper.BaseMapper;

/**
 * @author zhangbowen
 */
public interface UserMapper extends BaseMapper<UserEntity> {
}

```

### Controller

cn.faster.test.modules.user.controller.UserController.java

```
package cn.faster.test.modules.test.controller;

import cn.faster.test.modules.user.entity.UserEntity;
import cn.faster.test.modules.user.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

/**
 * @author zhangbowen
 */
@RestController
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserMapper userMapper;

    @GetMapping
    public ResponseEntity list() {
        return ResponseEntity.ok(userMapper.selectAll());
    }

    @PostMapping
    public ResponseEntity add(@RequestBody UserEntity userEntity) {
        userEntity.preInsert();
        userMapper.insert(userEntity);
        return new ResponseEntity(HttpStatus.CREATED);
    }

    @PutMapping("/{userId}")
    public ResponseEntity modify(@PathVariable Long userId, @RequestBody UserEntity userEntity) {
        userEntity.setId(userId);
        userEntity.preUpdate();
        userMapper.updateByPrimaryKey(userEntity);
        return new ResponseEntity(HttpStatus.CREATED);
    }

    @GetMapping("/{userId}")
    public ResponseEntity info(@PathVariable Long userId) {
        return ResponseEntity.ok(userMapper.selectByPrimaryKey(userId));
    }

    @DeleteMapping("/{userId}")
    public ResponseEntity delete(@PathVariable Long userId) {
        UserEntity update = new UserEntity();
        update.setId(userId);
        update.setDeleted(1);
        update.preUpdate();
        userMapper.updateByPrimaryKey(update);
        return new ResponseEntity(HttpStatus.NO_CONTENT);
    }
}

```


> 如果所有版本的接口代码都写在同一个controller中，那无疑是一场灾难。下一节我们将为您介绍如何对接口进行版本控制，保证您在编写代码时不会感到痛苦。