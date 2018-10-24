# 快速开始

## 引入

```
dependencies {
    compile "cn.org.faster:faster-framework-spring-boot-starter-core"
    compile "cn.org.faster:faster-framework-spring-boot-starter-redis"
}
```
## 完整gradle配置

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
   compile "cn.org.faster:faster-framework-spring-boot-starter-core"
   compile "cn.org.faster:faster-framework-spring-boot-starter-redis"
}

```
5.0以下gradle版本需配置settings.gradle

```
enableFeaturePreview("IMPROVED_POM_SUPPORT")
```

## application.yml配置

引入faster所提供的默认redis配置：

```
spring:
  profiles:
    include:
      - web
      - redis
```

设置redis数据库地址，端口等：

```
spring:
  redis:
    host: 127.0.0.1
    port: 6379
```

## 使用

```
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;


    @Test
    public void pushPopTest() {
        log.info("list pop push测试开始");
        log.info("设置key为listTest。push开始");
        BoundListOperations<String, Object> boundListOperations = redisTemplate.boundListOps("listTest");
        for (int i = 0; i < 10; i++) {
            boundListOperations.leftPush(i);
        }
        log.info("push完成");
        log.info("查看结果");
        for (long i = 0; i < boundListOperations.size(); i++) {
            log.info(boundListOperations.index(i).toString());
        }
    }
```