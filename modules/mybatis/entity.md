# 基础实体


## BaseEntity
Faster提供了基本的BaseEntity用于操作数据库。

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
