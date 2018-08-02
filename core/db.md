# 数据库

数据库操作我们选用Mybatis框架。

您可以在com.github.faster.framework.core.mybatis包下看到我们对mybatis所做的扩展与封装。



## 数据库通用操作

当系统抽象到一定高度后，我们的开发将会回归到原始的增删改查。

所以一个好的通用查询插件能够对开发效率的提升起到很大帮助。

Faster集成并扩展了github开源的通用mybatis mapper插件。[地址](https://github.com/abel533/Mapper)

### 如何使用BaseMapper

我们基于插件创建了Faster框架的BaseMapper。位于com.github.faster.framework.core.mybatis.BaseMapper.java

在使用时我们需要继承它：


```
public interface UserMapper extends BaseMapper<T>{

}
```

### BaseMapper内置方法

BaseMapper提供了丰富默认方法用于操作数据库。如下表格所示：

方法|描述
---|---
selectPage|分页查询，传入分页参数和实体，根据实体属性"="拼接where
selectPageByExample|分页查询，传入example，根据example拼接sql
selectOne|根据实体"="拼接查询返回单条数据
select|根据实体"="拼接查询返回多条数据
selectAll|返回全部数据
selectCount|根据实体中的属性查询总数，查询条件使用等号
selectByPrimaryKey|根据主键字段进行查询，查询条件使用等号
existsWithPrimaryKey|根据主键字段查询总数，查询条件使用等号
insert|保存一个实体，null的属性也会保存，不会使用数据库默认值
insertSelective|保存一个实体，null的属性不会保存，会使用数据库默认值
updateByPrimaryKey|根据主键更新实体全部字段，null值会被更新
updateByPrimaryKeySelective|根据主键更新属性不为null的值
delete|根据实体属性作为条件进行删除，查询条件使用等号
deleteByPrimaryKey|根据主键字段进行删除
selectByExample|根据example进行查询
selectOneByExample|根据example查询单条数据
selectCountByExample|根据example查询总数
deleteByExample|根据Example条件删除数据
updateByExample|根据Example条件更新实体包含的全部属性，null值会被更新
updateByExampleSelective|根据Example条件更新实体包含的不是null的属性值
insertList|批量插入，支持批量插入的数据库可以使用
selectByIdList|根据主键列表进行查询
deleteByIdList|根据主键列表进行删除
selectByIds|根据主键字符串进行查询
deleteByIds|根据主键字符串进行删除



## 分页插件
Faster使用MybatisPagerHelper作为分页插件。[地址](https://github.com/pagehelper/Mybatis-PageHelper)

您无需关心分页count语句以及limit语句的编写，只需要正常编写select语句，插件会自动拼接与生成分页语句。

### 如何使用分页插件

由于Faster使用通用Mapper进行操作数据库，固我们选择使用分页插件的RowBounds模式进行分页查询。

我们可以将业务类继承BasePager，BasePager提供了pageNum和pageSize转换成为roueBounds的方法。

默认情况下，BaseEntity已经集成BasePager，您只需要继承BaseEntity即可，分页参数为PageNum和PageSize，您可以直接使用，当然也可以自定义。

BasePager类：

```
@Data
public class BasePager {
    //页数
    @Transient
    private Integer pageNum = 1;
    //每页数据，默认10条
    @Transient
    private Integer pageSize = 10;

    public PageRowBounds rowBounds() {
        int offset = (pageNum - 1) * pageSize;
        return new PageRowBounds(offset, pageSize);
    }
}

```

BaseEntity类：

```
@Data
@EqualsAndHashCode(callSuper = false)
public abstract class BaseEntity extends BasePager implements Serializable{
    ....
}
```

我们推荐数据库操作均使用BaseEntity进行。


```
public void test(UserEntity user){
    userMapper.selectPage(user.rowBounds(),user);
    userMapper.selectPageByExample(user.rowBounds(),example);
}
```

### 分页方法

Faster提供了两种分页方法，分别是selectPage和selectPageByExample。

前者传入实体，自动拼接实体内属性值不为空的字段。拼接符号为“=”。
后者根据example条件拼接。

### 分页扩展

由于PagerHelper的count语句存在性能问题。固我们对count语句的生成做了二次开发。您可以查看package com.github.faster.framework.core.mybatis.pagination包下的内容

我们对插件扩展，虽然解决了count性能问题，但我们的sql语句中select部分最后一个条件不可为子查询或带有括号查询，必须为字段（后续我们将修复此问题）。如下所示：

    正确写法：

    
    select username,ifNull(username,'defaultName') as nameDefault,password from tb_user
    

    错误写法:

    
    select username,password,ifNull(username,'defaultName') as nameDefault from tb_user
    