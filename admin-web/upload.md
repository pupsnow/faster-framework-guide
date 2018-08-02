# 图片上传

对于图片上传，我们封装了Upload组件初始化所需要的属性。您需要引入imgUtils。

```
import { imgUtils, http } from '@utils';

 <FormItem {...Object.assign({}, formItemLayout, { wrapperCol: {} })} className="row-content" label="图片：">
        <ImageUpload
                {...imgUtils.initProperties}
                {...init("img",
                     imgUtils.initValid(
                        { rules: [{ required: true, message: "请上传图片" }] }
                     )
                )}
        />
</FormItem>
```

Upload组件获取的图片列表为对象数组类型，如果您需要转为逗号分隔url，则需要在保存前进行转换：

```
values.img = imgUtils.fileListToImgs(values.img);
```