# 异常

我们不建议您使用Exception控制异常业务逻辑。

我们希望您在遇到逻辑错误处理时，使用if判断，return错误信息，而不是抛出异常。

我们这里所介绍的异常处理是指程序中不可预知的运行时异常。



## 异常拦截

我们使用@ExceptionHandler拦截异常。

目前我们已默认支持以下异常拦截：

异常|描述
--|---
MethodArgumentNotValidException| 参数验证异常
BindException|参数绑定异常
TokenValidException|token过期
SQLException|sql执行出错
PersistenceException|Mybatis执行出错


## 异常返回格式

我们使用@ControllerAdvice和@ResponseBody返回接口的异常信息。

当程序抛出异常后，我们会返回以下格式数据，在http的body中。

```
{
    "timestamp":1499404172029,//请求时间戳
    "status":400,//http状态码
    "path":"/v1/version",//请求路径
    "code":"2",//业务状态码
    "message":"nickname不能为空"//错误信息
}
```


