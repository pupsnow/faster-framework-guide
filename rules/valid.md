# 验证规范

正确使用参数验证框架能够减少额外代码量，达到美化代码的效果。

我们使用javax.validation包下提供的注解进行参数验证。

1. 入参验证功能需在controller层进行，使用@Validated注解。
2. 验证注解建议在model包下的模型类中使用，我们的模型类属性名称尽量与实体类相同。我们可以使用BeanUtils进行属性的值复制。

UserAddModel.java
```
@Data
public class UserAddModel{
    private String username;
}
```

UserEntity.java

```
@Data
public class UserEntity{
    private String username;
}

```

UserController.java

```
public class UserController{
    public ResponseEntity test(@Validated UserAddModel addModel){
        UserEntity insert = new UserEntity();
        BeanUtils.copyProperties(addModel,insert);
        return null;
    }
}
```


3. 验证不需要额外抛出异常或捕获异常。
4. 如果需要，我们需要对分页参数进行验证（后续会提供自动化分页参数验证）。


