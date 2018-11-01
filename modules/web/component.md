# Web组件

Web组件为您提供了舒适的接口开发体验。

下面将一一为您介绍。

## 接口返回

我们希望您在接口返回时，使用ResponseEntity。

Faster扩展了ResponseEntity，为您提供了ErrorResponseEntity。您可以配合ErrorCode返回直观的错误信息。


## Java8日期支持

我们推荐使用Java8的日期组件，但很多三方插件都需要额外的包才可以支持LocalDate及LocalDateTime。

### Jackson

这里我们默认对Jackson的请求数据与返回数据中包含的Java8日期类型进行格式化，当然您也可以实现自己的格式化工具类。

我们通过注册Jackson提供的扩展，实现自己的Serializer和Deserializer

```
@Bean
public Module customModule() {
    SimpleModule module = new SimpleModule();
    module.addSerializer(LocalDateTime.class, new LocalDatetimeFormatter.LocalDateTimeSerializer());
    module.addDeserializer(LocalDateTime.class, new LocalDatetimeFormatter.LocalDateTimeDeserializer());
    module.addSerializer(LocalDate.class, new LocalDateFormatter.LocalDateSerializer());
    module.addDeserializer(LocalDate.class, new LocalDateFormatter.LocalDateDeserializer());
    return module;
}
```

其中：

LocalDateTime返回的序列化与请求的解析默认支持”yyyy-MM-dd HH:mm:ss“格式

LocalDate返回的序列化与请求的解析默认支持"yyyy-MM-dd"格式

### SpringMvc

同样的，SpringMVC的java8格式也需要进行自定义解析与序列化。

```
@Bean
public Converter<String, LocalDateTime> localDateTimeConvert() {
    return new LocalDatetimeFormatter.LocalDatetimeConverter();
}
@Bean
public Converter<String, LocalDate> localDateConvert() {
    return new LocalDateFormatter.LocalDateConverter();
}
```

## CORS

默认的，我们为您开启了跨域。

```
   @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.addAllowedOrigin("*");
        corsConfiguration.addAllowedHeader("*");
        corsConfiguration.addAllowedMethod("*");
        corsConfiguration.setAllowCredentials(true);
        source.registerCorsConfiguration("/**", corsConfiguration);
        return new CorsFilter(source);
    }
```

## RestTemplate

我们为您注册了RestTemplate，方便您操作简单的rest接口。

后续我们会增加专业的rest接口调用组件，如feign。

## SpringAppContextFacade

我们为您实现了SpringAppContextFacade。您可以在任意地方获取Spring容器内的bean。

```
 SpringAppContextFacade.applicationContext.getBean();
```

## ListWrapper

通常我们会遇到接口的请求参数中存在列表的形式。如果我们希望对列表的数据进行注解验证（@Valided）。我们需要对列表进行方式。

为此我们为您提供了ListWrapper。使用方式如下：

实体类中：

```
@Getter
@Setter
@Table
public class SysRolePermission extends BaseEntity {
    private Long roleId;
    @NotNull(message = "请选择权限")
    private Long permissionId;
}
```

Controller接口中：

```
public ResponseEntity choosePermissions(@Validated @RequestBody ListWrapper<SysRolePermission> list)
```

## Interceptor

我们提供了多种Interceptor简化接口操作。

### LogInteceptor

该接口打印了请求者的ip、请求的接口地址、以及get请求时传入的参数。

### ContextInterceptor

ContextInterceptor为您保存了当前请求的ip、访问的uri等信息。

您可以通过WebContextFacade在同一线程内获得这些数据。

```
WebContextFacade.getRequestContext().getIp();
```

### RequestContext

您可以使用autowired形式注入RequestContext。我们已经保证了其变量为线程安全的。

```
@Autowired
private RequestContext requestContext;
```

此处原理可百度ObjectFactory。


## 版本控制

我们针对接口提供了版本控制。方便您在开发时解耦多版本接口。

我们通过实现RequestMappingHandlerMapping和RequestCondition对请求路径进行拦截版本，分发接口。

所以您应当在您的接口地址中加入v{版本号}的标识。

您还需要使用我们提供的@ApiVersion注解。

UserController

```
@RequestMapper("/{version}/users")
@ApiVersion
public class UserController{

}
```

请求地址: http://xxx.com/v2/users

关于详细如何实现版本控制，在这里就不多做赘述。

您可以自行百度RequestMappingHandlerMapping和RequestCondition
