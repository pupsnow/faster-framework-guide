# 权限

安全是应用程序中不可或缺的一部分。

Faster提供了默认的app接口权限组件，通过jwt实现。

当然我们可以扩展自己的权限拦截器。


## AppAuthInterceptor

默认的，faster会注册满足app接口开发的权限拦截器AppAuthInterceptor。

该拦截器通过拦截请求头中的Authorization内容，反解出jwt内容，解析成功即为验证通过。

当然，您可以选择将token存储到缓存中并设置失效时间。此时解析出jwt内容后悔服务器端缓存中存储的内容进行比对。

```
@ConfigurationProperties(prefix = "faster.auth")
@Data
public class AuthProperties {
    /**
     * 是否开启
     */
    private boolean enabled = true;
    /**
     * 模式，默认为app模式
     */
    private String mode = "app";

    /**
     * token是否支持多终端，同时受缓存影响
     */
    private boolean multipartTerminal = true;
}
```

如果不支持多端登录，会存入缓存。

## @Login

您可以在需要登录的接口controller或方法上加入@Login注解，此时intceptor将会为您蓝旗。

## WebContextFacade

您可以通过WebContextFacade获取当前登录信息。

```
WebContextFacade.getRequestContext.getUserId();
```

您也可以使用autowired形式注入RequestContext。我们已经保证了其变量为线程安全的。

```
@Autowired
private RequestContext requestContext;

requestContext.getUserId();
```

## JwtService

JwtService为您提供了创建token的方法。您可以在应用的登录接口调用此方法创建token并返回给前端。
