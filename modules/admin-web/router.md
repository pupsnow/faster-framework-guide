# 路由

您可以在src/modules/routerConfig.js中配置路由菜单信息。

```
import asyncComponent from '@components/AsyncComponent'

const moduleRouterConfig = [
  {
    path: '/demo',
    component: asyncComponent(()=>import('@modules/demo')),
  }
];

export default moduleRouterConfig;
```

此处我们使用asyncComponent组件延迟加载组件，提升加载效率。