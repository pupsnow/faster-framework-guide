# 快速开始

## 引入

```
<dependency>
    <groupId>cn.org.faster</groupId>
    <artifactId>spring-boot-starter-redis</artifactId>
</dependency>
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