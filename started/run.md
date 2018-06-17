# 运行

上一节您已经学会将Faster引入到您的项目中，本节介绍如何运行Faster以及基本的配置文件配置。

## 建立包名

由于Faster基于Spring Boot创建，要求必须提供包名。

您可以按照规范创建一个普通的包。

此处我们建立cn.test

## 建立入口类

在cn.test下建立TestApplication.java，具体代码如下：

```
package cn.test;

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
    @RestController
    @RequestMapping("/test")
    public static class TestController {
        @GetMapping("/hello")
        public ResponseEntity hello(){
            return ResponseEntity.ok("hello faster");
        }
    }
}
```

## 运行

在main方法上右键run，终端输出Tomcat started on port(s): 8080 (http) with context path说明运行成功。

我们可以通过请求 http://localhost:8080/test/hello 进行验证。

> 如果您的项目包含数据库或者需要一些常用的web配置如日期格式化，您可能需要修改配置文件。下节将为您介绍faster提供的默认配置文件。