# 版本控制

本节介绍接口的版本控制，以达到版本继承，版本隔离的作用。

## @ApiVersion

我们使用@ApiVersion注解控制版本权限，在Controller类上增加注解，值为版本号即可实现版本隔离，继承。

@ApiVersion默认值为1

## 建立包

```
cn.faster.test.modules.v1.user;
cn.faster.test.modules.v2.user;
```

## 建立类

### V1版本的Controller
cn.faster.test.modules.v1.user.controller.UserController;

```
package cn.faster.test.modules.v1.user;


/**
 * @author zhangbowen
 */
@RequestMapping("/{version}/user")
@RestController
public class UserController {
    @GetMapping("/extend")
    public ResponseEntity extend() {
        return ResponseEntity.ok("测试可以继承的接口");
    }

    @GetMapping("/hello")
    public ResponseEntity hello() {
        return ResponseEntity.ok("这是版本1的hello接口");
    }
}


```

### V2版本的Controller

cn.faster.test.modules.v2.user.controller.UserController;

```

/**
 * @author zhangbowen
 */
@RequestMapping("/{version}/user")
@RestController
@ApiVersion(2)
public class UserController {
    @GetMapping("/hello")
    public ResponseEntity hello() {
        return ResponseEntity.ok("测试版本2的hello接口");
    }

    @GetMapping("/new")
    public ResponseEntity newApi() {
        return ResponseEntity.ok("测试版本2的新接口");
    }
}

```


## 验证

至此，我们版本1拥有接口：
extend、hello
版本2拥有接口：
extend、hello、new

### 测试版本1接口


```
http://localhost:8080/v1/extend

输出：测试可以继承的接口
```

```
http://localhost:8080/v1/hello

输出：这是版本1的hello接口
```

### 测试版本2接口

```
http://localhost:8080/v2/extend

输出：测试可以继承的接口
```

```
http://localhost:8080/v2/hello

输出：测试版本2的hello接口
```


```
http://localhost:8080/v2/new

输出：测试版本2的新接口
```