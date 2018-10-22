# 短信验证码

我们针对短信验证码进行了封装，您可以扩展此接口来调用所使用的短信SDK。

## 默认组件

默认的，当您引入短信模块，我们会自动注册调试环境的短信验证码组件，默认组件在发送和验证时，均直接返回true。

## 选择短信平台

您可以选择当前项目开启的短信平台来使用短信验证码模块。

以阿里云为例：

1. 实现短信验证码接口

```
@Service
public class AliSmsCaptchaService extends ISmsCaptchaService {
    @Autowired
    private ISmsService<SendSmsRequest> smsService;

    public AliSmsCaptchaService(SmsProperties smsProperties) {
        super(smsProperties.isDebug(), smsProperties.getCaptcha().getExpire());
    }

    @Override
    protected boolean sendCode(String phone, String code) {
        SendSmsRequest sendSmsRequest = new SendSmsRequest();
        sendSmsRequest.setPhoneNumbers(phone);
        return smsService.send(sendSmsRequest);
    }

}
```

2. 调用

```
@RestController
@RequestMapping("/smsCode")
public class SmsCodeController {
    @Autowired
    private ISmsCaptchaService smsCaptchaService;
    @GetMapping
    public ResponseEntity send() {
        smsCaptchaService.send("18764050615", "reg");
        return new ResponseEntity(HttpStatus.CREATED);
    }
}
```

