# 后台管理接口

我们基于Faster-Framework-Core研发了一套基础的后台管理接口框架：[Faster-Framework-Admin](https://github.com/faster-framework/faster-framework-admin)。

在使用Admin时，您只需要引入我们提供的包，便可以无需关心后台管理复杂的框架逻辑，专注业务开发。

它包含了以下基础功能：

1. 认证授权。集成shiro，扩展jwt。以无状态的方式管理用户的授权状态。
2. 用户管理。基本的后台管理用户的增删改查，可为用户选择角色。
3. 角色管理。角色的增删改查，可为角色选择权限。
4. 权限管理。权限树的管理，接口端根据权限code控制接口权限。

