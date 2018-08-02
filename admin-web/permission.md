# 权限

我们自行封装了权限组件。以达到控制菜单、按钮权限。

菜单权限您可以在菜单一节中看到。此处我们介绍按钮权限。

您可以通过Permission标签包裹元素来控制元素的显示与隐藏。

```
<Permission code=""></Permission>
```

我们使用redux存储当前用户权限列表。

示例：

```
 <Permission code="permissions:delete">
    <Button type="primary" onClick={() => this.checkAndShow(this.refs.deleteDialog)}>
        <Icon type="close" size="xs" />删除
    </Button>
</Permission>
```


