# 基础实体

您可以在cn.org.framework.core.entity包下看到基本的Entity。

## BaseEntity
Faster提供了基本的Entity用于操作数据库。

所有的数据库实体都应当继承BaseEntity，拥有公用字段，当然也可以按照您自己的习惯自行封装BaseEntity。

### PreInsert

BaseEntity提供了preInsert方法，您可以在插入数据库之前调用。它可以自行获取当前的创建时间、更新时间、操作用户id、创建人、更新人等。

```
public void insert(UserEntity user){
    user.preInsert();
}
```

### PreUpdate

同样，我们也提供了preUpdate方法，您可以在更新实体到数据库之前调用。

```
public void update(UserEntity user){
    user.preUpdate();
}
```


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
List<SysPermission> list = sysPermissionMapper.selectByExample(
                Example.builder(SysPermission.class)
                        .where(WeekendSqls.<SysPermission>custom().andEqualTo(SysPermission::getDeleted, 0))
                        .orderByAsc(
                                "parentIds"
                        ).build()
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
        List<SysPermission> list = sysPermissionMapper.selectByExample(
                Example.builder(SysPermission.class)
                        .where(WeekendSqls.<SysPermission>custom().andEqualTo(SysPermission::getDeleted, 0))
                        .orderByAsc(
                                "parentIds"
                        ).build()
        ).stream().sorted(Comparator.comparing(BaseEntity::getSort)).collect(Collectors.toList());
        return TreeUtils.convertToTree(list);
    }
```