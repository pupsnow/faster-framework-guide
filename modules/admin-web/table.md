# 表格

我们对飞冰表格进行了二次封装。您可以便捷快速的展示表格、筛选、分页、操作。

```
{/* 表格开始 */}
<TableList ref="tableList" api='/sys/permissions/tree' title="权限列表" defaultFilterParam={this.defaultFilterParam}>
    {/* 操作开始 */}
    <div key="operations">
        <Permission code="permissions:add">
            <Button type="primary" onClick={() => this.refs.addDialog.show()}>
                <Icon type="add" size="xs" />添加
            </Button>
        </Permission>
        <Permission code="permissions:delete">
            <Button type="primary" onClick={() => this.checkAndShow(this.refs.deleteDialog)}>
                <Icon type="close" size="xs" />删除
            </Button>
        </Permission>
    </div>
    {/* 操作结束 */}
    {/* 列表开始  */}
     <div key="tables" isTree={true}>
        <Table.Column title="权限名称" dataIndex="name" />
        <Table.Column title="权限编码" dataIndex="code" />
        <Table.Column title="创建时间" dataIndex="createDate" />
        <Table.Column title="操作" cell={this.renderOper} />
    </div>
    {/* 列表结束 */}
</TableList>
{/* 表格结束 */}




  /**
   * 渲染操作列
   */
  renderOper = (value, index, record) => {
    return (
      <div className="col-operation">
        <Permission code="permissions:modify">
          <Button shape="text" size="small" onClick={() => this.refs.editDialog.show()}>
            修改
          </Button>
        </Permission>
      </div>
    );
  };
```