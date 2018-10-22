# 路由权限

## 使用

我们设计了权限表来控制每个接口的权限，首先您需要为某个接口指定一个code。并且存储到sys_permission表中。

接下来，您需要在接口上加入注解：

```
@RequiresPermissions("{permission_code}")
```

当然，您需要在接口文档中指出该接口所需要的code，以便前端同样控制。

我们建议您的permission_code遵循以下规范：

{业务名称}:{功能名称}

如：permission:modify代表修改权限


## 权限列表

前端在登录后，可通过/permissions接口获取当前用户的所有接口权限code。

以此来判断当前需要展示的菜单、按钮、数据等。