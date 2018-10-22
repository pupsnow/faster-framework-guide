# 富文本编辑器

我们选用BraftEditor作为富文本编辑器。

```
import BraftEditor from '@components/BraftEditor';
<FormItem  {...Object.assign({}, formItemLayout, { wrapperCol: {} })} className="row-content" label="内容：">
        <BraftEditor {...init("content")}/>
</FormItem>
```

您需要手动在保存时验证富文本内容是否为空：

```
if (this.refs.content.isEmpty()) {
    this.field.setError("content", "请输入富文本内容");
    return false;
}
```

您需要手动获取富文本内容：

```
values.content = this.refs.content.getContent();
```