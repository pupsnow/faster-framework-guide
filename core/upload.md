# 上传组件

资源文件上传同样是我们应用中一个重要的功能。

Faster内置了图片上传接口，由于图片上传方式众多。

我们只提供接口与默认的本地上传实现。您可以自行实现自己的上传接口。

后续我们会集成一些默认的三方上传服务，如七牛、阿里云oss。

## LocalUploadService
默认我们将图片上传至服务器本地。

### 配置


我们需要您配置文件目录与请求前缀。

```
@ConfigurationProperties(prefix = "faster.upload.local")
@Data
public class LocalUploadProperties {
    /**
     * 文件的存储目录
     */
    private String fileDir;
    /**
     * 请求图片时的网址前缀
     */
    private String urlPrefix;
}
```

### 上传流程

为了加密接口，防止他人调用，我们首先需要请求接口获取上传token。

```
 /**
     * 预上传，返回上传所需参数
     * @param uploadRequest 上传请求
     * @return httpResponse
     */
    @GetMapping("/upload/preload")
    public ResponseEntity preload(UploadRequest uploadRequest) {
        return ResponseEntity.ok(uploadService.preload(uploadRequest));
    }
```

接下来我们便可以调用上传接口上传图片。


```
  /**
     * 上传文件
     *
     * @param uploadFile    文件
     * @param uploadRequest 上传请求
     * @param token         签名字符串
     * @return httpResponse
     */
    @PostMapping("/upload")
    public ResponseEntity upload(@RequestParam("file") MultipartFile uploadFile, UploadRequest uploadRequest,String token) {
        try {
            return ResponseEntity.ok(uploadService.upload(uploadFile, uploadRequest, token));
        } catch (IOException e) {
            return ErrorResponseEntity.error(BasisErrorCode.SERVER_ERROR, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
```

上传成功后，系统会返回上传地址。由于我们是图片存储在本地服务器，故我们需要提供请求图片接口。（当然你可以使用ngixn进行代理）。

```
 /**
     * 预览、下载上传的文件
     *
     * @param fileName 文件名称
     * @return 文件流
     */
    @GetMapping("/media/{fileName}")
    public ResponseEntity preview(@PathVariable String fileName) {
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
        return new ResponseEntity<>(uploadService.files(fileName), headers, HttpStatus.OK);
    }
```

### 接口

您需要提供一个入口供前端调用，我们提供了AbstractUploadController供您继承

```
@RestController
public class UploadController extends AbstractUploadController{}
```

继承后，您可以：

通过http://xxx.com/upload/preload  获取token

通过http://xxx.com/upload   上传图片


## 自定义上传

### IUploadService

您需要实现IUploadService提供的所有方法。

### application.yml

您需要在配置文件中修改上传模式为您自定义的名称。

```
@Data
@ConfigurationProperties(prefix = "faster.upload")
public class UploadProperties {
    /**
     * 是否开启
     */
    private boolean enabled = true;
    /**
     * 本地模式
     */
    private LocalUploadProperties local = new LocalUploadProperties();


    @ConfigurationProperties(prefix = "faster.upload.local")
    @Data
    public static class LocalUploadProperties {
        /**
         * 是否开启
         */
        private boolean enabled;
        /**
         * 文件的存储目录
         */
        private String fileDir;
        /**
         * 请求图片时的网址前缀
         */
        private String urlPrefix;
    }
}

```

### 注册

您需要将组建注册进Spring。

```
    @Configuration
    @ConditionalOnProperty(prefix = "faster.upload.{youUploadName}", name = "enabled", havingValue = "true")
    @EnableConfigurationProperties({ProjectProperties.class, UploadProperties.YourUploadProperties.class})
    @ConditionalOnMissingBean(IUploadService.class)
    public static class YourUploadConfiguration {

        @Bean
        public IUploadService localUpload(ProjectProperties projectProperties, UploadProperties.YourUploadProperties localUploadProperties) {
            return new YourUploadService();
        }
    }
```