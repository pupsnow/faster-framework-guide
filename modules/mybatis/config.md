
# 默认配置
数据库操作我们选用Mybatis框架。并且使用mybatis-plus(http://mp.baomidou.com/)简化数据库操作。

## 默认配置
我们提供mybatis默认配置，您可以通过include:mybatis引入。

```
spring:
  datasource:
      druid:
        driver-class-name: com.mysql.jdbc.Driver
        maxActive: 100
        initialSize: 50
        minIdle: 10
        timeBetweenEvictionRunsMillis: 60000
        minEvictableIdleTimeMillis: 300000
        validationQuery: select 'x'
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        poolPreparedStatements: false
        filters: stat,slf4j
        # 配置 connection-properties，启用加密，配置公钥。
        connection-properties: config.decrypt=true;config.decrypt.key=${spring.datasource.druid.public-key}
        # 启动ConfigFilter
        filter:
          config:
            enabled: true

mybatis-plus:
  mapper-locations: classpath*:**/mapper/xml/*Mapper.xml
```