# 快速开始

## 项目关键结构及介绍

```
--config
    ---.webpackrc.dev.js #可在此文件中配置开发环境接口地址
    ---.webpackrc.prod.js #可在此文件中配置生产环境接口地址
--src
    ---common
    ---modules #业务模块包，您只需要在此包下开发业务即可
        ---menuConfig.js #菜单配置
        ---routerConfig.js #路由配置
```


## 业务开发

当您进行业务开发时，您只需要在modules目录下建议业务包。编写业务组件即可。

如：

```
---src
    ---modules
        ---permission
            ---PermissionAdd.jsx
            ---PermissionEdit.jsx
            ---PermissionList.jsx
            ---index.js
```

## 菜单配置

如果您希望展示菜单，仅需要在modules下的menuConfig.js中配置菜单信息以及在routerConfig.js中配置路由信息即可。

