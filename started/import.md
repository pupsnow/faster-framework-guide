# 引入

Faster-Framework通过Gradle构建，同样我们推荐使用Gradle构建您的项目。

当然您也可以使用Maven。此处我们只介绍Gradle引入方式。

不论您使用Gradle还是Maven，都需要一个快速稳定的镜像仓库。这里我们推荐阿里镜像仓库。

http://maven.aliyun.com/nexus/content/groups/public/

## Gradle

### 创建项目

通过IDEA创建一个默认的Gradle项目即可

### 基本配置

build.gradle文件中加入以下代码：

```
plugins {
    id 'java'
    id 'org.springframework.boot' version '2.0.2.RELEASE'
}

group '{your group}'
version '{your version}'

sourceCompatibility = 1.8

repositories {
    maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
    mavenCentral()
}
sourceSets.main.resources.srcDirs = ["src/main/java", "src/main/resources"]

```

### 引入仓库

build.gradle中加入以下代码：

```
dependencies {
    compile "com.github.faster-framework:faster-framework-core-spring-boot-starter:{最新版本}"
}
```

### 完整gradle配置文件示例

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
sourceSets.main.resources.srcDirs = ["src/main/java", "src/main/resources"]

dependencies {
    compile "com.github.faster-framework:faster-framework-core-spring-boot-starter:{最新版本}"
}

```

> 下一节将为您展现如何运行一个普通的faster项目。