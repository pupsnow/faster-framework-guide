# 快速开始

```
plugins {
    id 'java'
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
   implementation "cn.org.faster:faster-framework-spring-boot-starter-parent:${lastedVersion}"
   ...
   ...
}
```
5.0以下gradle版本需配置settings.gradle

```
enableFeaturePreview("IMPROVED_POM_SUPPORT")
```

当您完成以上配置后，您便可查看支持的模块，随意组装starter。
