# 引入

我们推荐使用Maven构建您的项目。

推荐阿里镜像仓库。

http://maven.aliyun.com/nexus/content/groups/public/

您可以将其设置在~/.m2/settings.xml中作为全局仓库，也可以将其设置在项目中。

全局settings设置

```
<mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
  　　<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
</mirrors>
```

项目设置

```
<repositories>
    <repository>
        <id>alimaven</id>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </repository>
</repositories>
```

## 创建项目

通过IDEA创建一个默认的Maven项目即可。
faster使用bom管理依赖，故您需要引入parent。最新的parent版本您可以在[releases](https://github.com/faster-framework/faster-framework-project/releases)中找到。

cn.org.faster:spring-boot-starter-parent:{lasted}

### 基本配置

pom.xml文件示例如下，此处我们引入了web模块与mybatis模块，以提供默认的接口配置与数据库配置：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>cn.org.faster</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>${最新版本}</version>
    </parent>
    <groupId>cn.test</groupId>
    <artifactId>test-project-api</artifactId>
    <version>1.0-SNAPSHOT</version>
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
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
            </resource>
        </resources>
    </build>

</project>
```


> 下一节将为您展现如何运行一个普通的faster项目。