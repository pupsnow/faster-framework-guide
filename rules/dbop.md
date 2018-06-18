# 数据库规范

- 在对数据库进行操作时，请尽量进行单表操作，方便日后的项目重构。
- 对数据库的操作尽量使用BaseMapper所提供的方法，faster集成github开源组件[通用mapper](https://github.com/abel533/Mapper/wiki)，您可以学习并使用。通常来说，我们使用其提供的example（mybatis提供example，BaseMapper进行了扩展）增加限制条件。我们建议使用lambda表达式操作，如下：

    ```
    List<Country> selectByWeekendSql =     mapper.selectByExample(new Example.Builder(Country.class)
        .where(WeekendSqls.<Country>custom().andLike(Country::getCountryname, "a%")
                .andGreaterThan(Country::getCountrycode, "123"))
        .build());
    ```

- 对数据库的like查询，希望您使用右侧模糊策略："like%"。（请了解B-TREE最左前缀索引匹配原则）
- 如果您需要建立额外的xml进行编写个性化sql，我们希望您在mapper包下建立xml包，存储mybatis sql文件，例: userMapper.xml
- 对于分页查询，faster默认集成的PagerHelper插件，但由于其查询总数时存在bug，固我们重写了其count方法。但我们的sql语句中select部分最后一个条件不可为子查询或带有括号查询，必须为字段（后续我们将修复此问题）。如下所示：

    正确写法：

    ```
    select username,ifNull(username,'defaultName') as nameDefault,password from tb_user
    ```

    错误写法:

    ```
    select username,password,ifNull(username,'defaultName') as nameDefault from tb_user
    ```

- 数据库实体建立时，时间字段请使用LocalDateTime。
- 我们的BaseEntity默认提供一系列数据库字段（deleted,createDate等）。我们建议您的数据使用逻辑删除以防止意外。
- 我们希望您在对数据库进行操作时，均使用继承BaseEntity的实体。
