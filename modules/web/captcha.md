# 图形验证码

我们引入com.github.bingoohuang:patchca组件来生成图形验证码。

## 使用流程

1. 前端请求图形验证码接口，生成base64图片字符串与token，返回给前端使用。

2. 前端通过base64展示图片，提交时携带token。

3. 通过图形验证码服务接口验证token与用户填写的验证码是否相同。

4. 图形验证码有效期为1分钟。

## token
我们通过生成JWT来验证每次图形验证码的请求有效性与用户输入的验证码有效性。

## AbstractCaptchaController

我们需要继承AbstractCaptchaController注册controller以此对外提供接口。


```
@RestController
public class CaptchaController extends AbstractCaptchaController {
}
```

## 验证

我们可以使用ICaptchaService提供的验证接口来验证用户的验证码是否有效。


```
@Autowired
private ICaptchaService captchaService;

boolean valid = captchaService.validCaptcha(inputCaptcha, captchaToken);
```