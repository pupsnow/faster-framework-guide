# 默认配置

默认的，我们为您对SpringMvc作了如下配置：

```
spring:
  output:
    ansi:
      enabled: ALWAYS
  jackson:
    default-property-inclusion: non_null
    time-zone: GMT+8
    date-format: yyyy-MM-dd HH:mm:ss
    generator:
      WRITE_NUMBERS_AS_STRINGS: true
  mvc:
    date-format: yyyy-MM-dd HH:mm:ss
  servlet:
    multipart:
      max-file-size: 100MB
      max-request-size: 100MB
server:
  tomcat:
    accept-count: 250
    max-threads: 500
    max-connections: 1000

```