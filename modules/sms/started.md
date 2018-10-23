# 快速开始

## 引入

```
dependencies {
    compile "cn.org.faster:faster-framework-spring-boot-starter-core"
    compile "cn.org.faster:faster-framework-spring-boot-starter-sms"
}
```
## 完整gradle配置

```
plugins {
    id 'java'
    id 'io.spring.dependency-management' version '1.0.5.RELEASE'
}

group 'test-framework'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
    mavenCentral()
}
sourceSets.main.resources.srcDirs = ["src/main/java", "src/main/resources"]
dependencyManagement {
    imports {
        mavenBom "cn.org.faster:faster-framework-spring-boot-starter-parent:${lastedVersion}"
    }
}
dependencies {
   compile "cn.org.faster:faster-framework-spring-boot-starter-core"
   compile "cn.org.faster:faster-framework-spring-boot-starter-sms"
}

```

## 配置

您需要选择您想要使用的短信平台。以阿里云短信功能为例。您需要在yml文件中启用以下配置：

```
faster:
  sms:
    ali:
        access-key-id: 111
        access-key-secret: 222
    captcha:
        expire: 60 //超时时间（s），默认15分钟
```


## 使用

当您需要使用短信功能时，只需注入所引入的服务即可，其中泛型为相应服务所需参数，继承ISmsService时指定。

```
    @Autowired
    private ISmsService<?> smsService;

    smsService.send(?);
```

我们仅对sdk进行了整合，使用方式还需自行查看三方使用说明文档。


## 扩展

您可以扩展三方短信平台SDK，以下为阿里云短信扩展实现方式，可参考

1. 实现ISmsService短信接口
```
@Slf4j
public class AliSmsService implements ISmsService<SendSmsRequest> {
    private IClientProfile profile;

    public AliSmsService(String accessKeyId, String accessKeySecret) {
        try {
            //初始化ascClient,暂时不支持多region（请勿修改）
            profile = DefaultProfile.getProfile("cn-hangzhou", accessKeyId,
                    accessKeySecret);
            DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", "Dysmsapi", "dysmsapi.aliyuncs.com");
        } catch (ClientException e) {
            e.printStackTrace();
        }
    }

    @Override
    public boolean send(SendSmsRequest request) {
        try {
            IAcsClient acsClient = new DefaultAcsClient(profile);
            request.setMethod(MethodType.POST);
            SendSmsResponse sendSmsResponse = acsClient.getAcsResponse(request);
            if (sendSmsResponse.getCode() != null && sendSmsResponse.getCode().
                    equals("OK")) {
                return true;
            }
        } catch (ClientException e) {
            log.error(e.getMessage());
            return false;
        }
        return false;
    }
}
```

2. 创建阿里短信配置

```
@Data
@ConfigurationProperties(prefix = "faster.sms")
public class SmsProperties {
    /**
     * 是否开启
     */
    private boolean enabled;
    /**
     * 是否为调试环境
     */
    private boolean debug;
    /**
     * 模式
     */
    private String mode;
    /**
     * 阿里短信
     */
    private AliProperties ali = new AliProperties();
    /**
     * 验证码
     */
    private SmsCaptchaProperties captcha = new SmsCaptchaProperties();

    @Data
    @ConfigurationProperties(prefix = "ali")
    public static class AliProperties {
        /**
         * 阿里key
         */
        private String accessKeyId;
        /**
         * 阿里secret
         */
        private String accessKeySecret;
    }
}
```

3. 注册阿里短信模块

```
    /**
     * 阿里云短信发送配置
     * @param smsProperties 短信配置
     * @return AliSmsService
     */
    @Bean
    @ConditionalOnProperty(prefix = "faster.sms", name = "mode", havingValue = "ali")
    @ConditionalOnMissingBean(ISmsService.class)
    public ISmsService aliSmsCode(SmsProperties smsProperties) {
        return new AliSmsService(smsProperties.getAli().getAccessKeyId(), smsProperties.getAli().getAccessKeySecret());
    }
```