# 工具包

Faster提供了一系列常用的工具类，供您使用。

后续我们会不断增加与完善。

这些工具在cn.org.faster.framework.core.utils包下，您可以自行查看。




## TreeNode

对于无限级树状结构，您可以使实体实现TreeNode接口。

之后您便可以通过faster提供的工具将数据库中的列表数据转换为树形结构（无限级）。

如下：

```
@Getter
@Setter
@Table
public class SysPermission extends BaseEntity implements TreeNode {
    private String name;
    private String code;
    private Long parentId;
    private String parentIds;
    private List<TreeNode> children;
}
```


### 前提条件

由于递归操作会影响性能，所以faster不使用递归处理树形结构的组建。

所以我们需要您的列表数据，按照层级从低到高的顺序排列。以便对数据进行循环。

所以您需要在您的数据结构中建立类似level、parent_ids等可以表示层级的字段，并在取出数据时（后）对数据进行正序排序。

您可以参照下方代码：


```
  List<SysPermission> list = super.baseMapper.selectList(
                new LambdaQueryWrapper<SysPermission>().orderByAsc(SysPermission::getParentIds)
        ).stream().sorted(Comparator.comparing(BaseEntity::getSort)).collect(Collectors.toList());
```

### 转换为树形结构

在获取数据后，您便可以通过faster提供的另外一个工具类，转换成我们所需的树形结构列表。

```
TreeUtils.convertToTree(list);
```

完整代码如下：

```
    /**
     * @return 权限树
     */
    public List<TreeNode> treeList() {
        List<SysPermission> list = super.baseMapper.selectList(
                new LambdaQueryWrapper<SysPermission>().orderByAsc(SysPermission::getParentIds)
        ).stream().sorted(Comparator.comparing(BaseEntity::getSort)).collect(Collectors.toList());
        return TreeUtils.convertToTree(list);
    }
```