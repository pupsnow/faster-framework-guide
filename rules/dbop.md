# 数据库规范

- 在对数据库进行操作时，请尽量进行单表操作，将逻辑放入java中编写，而非使用关联查询，子查询等消耗性能的方式进行。同样可方便日后的项目重构。
- 对数据库的操作尽量使用BaseMapper所提供的方法。
- 对数据库的like查询，希望您使用右侧模糊策略："like%"。（请了解B-TREE最左前缀索引匹配原则）
- 如果您需要建立额外的xml进行编写个性化sql，我们希望您在mapper包下建立xml包，存储mybatis sql文件，例: userMapper.xml
- 对于分页查询，使用faster默认提供的方式进行：IPage<UserEntity> list  = userMapper.selectPage(userEntity.toPage(), null);
- 数据库实体建立时，时间字段请使用LocalDateTime。
- 我们的BaseEntity默认提供一系列数据库字段（deleted,createDate等）。我们建议您的数据使用逻辑删除以防止意外。
- 我们希望您在对数据库进行操作时，均使用继承BaseEntity的实体。
