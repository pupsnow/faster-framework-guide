# 菜单

您可以在src/modules/menuConfig.js中配置业务菜单信息。

```
const moduleMenuConfig = [
    {
      name: 'demo',
      path: '/demo',
      icon: 'home',
    },
];
export default moduleMenuConfig;
```

您可以为菜单设置code，以此达到控制菜单权限。如下：

```
{
    name: '系统管理',
    icon: 'yonghu',
    path: '/sys',
    children: [
      { name: '用户管理', path: '/user', code: 'users:manage' },
      { name: '角色管理', path: '/role', code: 'roles:manage' },
      { name: '权限管理', path: '/permission', code: 'users:manage' },
    ],
}
```


仅有菜单还无法展示内容，您还需配置路由信息。