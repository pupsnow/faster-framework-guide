# 接口文档
# 1. 基本概述
## 1.1 关于REST
### 1.1.1 关于REST
REST（Representational State Transfer）是一种轻量级的 Web Service 架构风格，可以翻译成“表述性状态转移”，实现和操作明显比 SOAP 和 XML-RPC 更为简洁，可以完全通过 HTTP 协议实现，还可以利用缓存 Cache 来提高响应速度，性能、效率和易用性上都优于 SOAP 协议。

REST 架构遵循了 CRUD 原则，CRUD 原则对于资源只需要四种行为：Create（创建）、Read（读取）、Update（更新）和Delete（删除）就可以完成对其操作和处理。这四个操作是一种原子操作，对资源的操作包括获取、创建、修改和删除资源的操作正好对应 HTTP 协议提供的 GET、POST、PUT 和 DELETE 方法，因此 REST 把 HTTP 对一个 URL 资源的操作限制在 GET、POST、PUT 和 DELETE 这四个之内。这种针对网络应用的设计和开发方式，可以降低开发的复杂性，提高系统的可伸缩性。

更多 REST API 背景知识可以参考[restful api 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html "restful api 设计指南")。

## 1.2 客户端请求规范
### 1.2.1 请求地址规范

http://domain/模块



### 1.2.2 调用规范

需要注意的是，接口获取http body中的数据是基于 JSON 的，所以在构造 HTTP 请求的时候，需要在 HTTP HEADER 中指明：

header_name|header_value|Description
---|---|---
Content-Type|application/json|客户端发送到服务器端的数据类型
Authorization|string|token，后台返回，直接使用即可


### 1.1.3 Request Parameter 与 Path Parameter

Request Parameter 相当于表单提交

```
http://xxx.com/test?name=zhangsan
```

Path Parameter

```
http://xxx.com/test/{name}
```

## 1.3 服务器端响应规范
服务器端响应状态码均使用Http状态码，错误响应时存在业务状态码。

### 1.3.1 分页返回格式

参数有点多，自己体会吧

```
{
    "pageNum": "1",
    "pageSize": "10",
    "size": "1",
    "startRow": "1",
    "endRow": "1",
    "total": "1",
    "pages": "1",
    "prePage": "0",
    "nextPage": "0",
    "isFirstPage": true,
    "isLastPage": true,
    "hasPreviousPage": false,
    "hasNextPage": false,
    "firstPage": "1",
    "lastPage": "1"
}
```

### 1.3.2 错误信息返回格式

```

{
	"timestamp":1499404172029,//请求时间戳
	"status":400,//http状态码
	"path":"/v1/version",//请求路径
	"code":"2",//业务状态码
	"message":"nickname不能为空"//错误信息
}

```

## 1.4 服务器端常见状态码

Code|状态|描述
---|---|---
200|OK |[GET]：服务器成功返回用户请求的数据。
201|CREATED |[POST/PUT/PATCH]：用户新建或修改数据成功。
202 |Accepted| [*]：表示一个请求已经进入后台排队（异步任务)
204 | NO CONTENT | [DELETE]：用户删除数据成功
400 | INVALID REQUEST |[POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作。
401 |Unauthorized|[*]：表示用户没有权限（令牌、用户名、密码错误）。
403 |Forbidden|[*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 |NOT FOUND|[*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作。
406 |Not Acceptable|[GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 |Gone|[GET]：用户请求的资源被永久删除，且不会再得到的。
422 |Unprocesable entity|[POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 |INTERNAL SERVER ERROR|[*]：服务器发生错误，用户将无法判断发出的请求是否成功。

# 2. 授权模块
## 2.1 登录

- /login
- post
- request

```
{
	"account":"用户名",
	"password":"密码"
}
```

- reponse

```
{
	"token":"存入header",
	"name":"姓名"
}
```

## 2.2 权限列表

- /permissions
- get
- 根据获取的权限列表，显示相应菜单、按钮，以控制权限
- reponse

```
[
	"users:list（权限code）",
	"users:add",
	"users:modify"
]
```

## 2.3 注销

- /logout
- delete
- response


# 3. 系统模块
## 3.1 权限管理

- permissions:manage

### 3.1.1 权限树

- /sys/permissions
- get
- permissions:list
- response

```
[
	{
		"id":"主键id",
		"name":"权限名称",
		"code":"权限编码",
		"parentId":"父id",
		"parentIds":"父id列表",
		"createBy":"创建人id",
		"updateBy":"最后更新人时间",
		"createDate":"创建时间",
		"updateDate":"最后更新时间"，
		"children":[
			{
				self
			}
		]
	}
]
```

### 3.1.2 权限详情
- /sys/permissions/{permissionId}
- get
- permissions:info
- response

```
{
	"id":"主键id",
	"name":"权限名称",
	"code":"权限编码"
}
```

### 3.1.3 新增权限

- /sys/permissions
- post
- permissions:add
- request

```
{
	"name":"权限名称",
	"code":"权限编码",
	"parentId":"父级id"
}
```

### 3.1.4 编辑权限

- /sys/permissions/{permissionId}
- put
- permissions:edit
- request

```
{
	"name":"权限名称",
	"code":"权限编码"
}
```

### 3.1.5 删除权限

- /sys/permissions/{permissionId}
- permissions:delete
- delete

## 3.2 角色管理
- roles:manage
### 3.2.1 角色列表
- /sys/roles
- get
- roles:list
- Request Parameter

字段|是否必填| 描述
---|---|----
pageSize | Y | 每页数量
pageNum|Y|当前页数
name|N|角色名称

- response

```
{
	---公用分页参数,
	"list":[
		{
			"id":"",
			"name":"角色名称",
			"createBy":"创建人id",
			"updateBy":"最后更新人时间",
			"createDate":"创建时间",
			"updateDate":"最后更新时间"，
		}
	]
}
```

### 3.2.2 角色详情
- /sys/roles/{roleId}
- get
- roles:info
- response

```
{
	"id":"",
	"name":"角色名称"
}
```

### 3.2.3 新增角色

- /sys/roles
- post
- roles:add
- request

```
{
	"name":"昵称"
}
```

### 3.2.4 更新角色
- /sys/roles/{roleId}
- put
- roles:modify
- response

```
{
	"name":"角色名称"
}
```

### 3.2.5 删除角色

- /sys/roles/{roleId}
- roles:delete
- delete

### 3.2.6 选择权限

- /sys/roles/{roleId}/permissions/choose
- put
- roles:permissions:choose
- request

```
{
	"list":[
		{
			"permissionId":"权限id"
		}
	]
}
```

## 3.3 用户管理

- users:manage

### 3.3.1 用户列表

- /sys/users
- get
- users:list
- Request Parameter

字段|是否必填| 描述
---|---|----
pageSize | Y | 每页数量
pageNum|Y|当前页数
name|N|用户姓名

- response

```
{
	---公用分页参数,
	"list":[
		{
			"id":"",
			"account":"用户名",
			"name":"用户姓名",
			"createBy":"创建人id",
			"updateBy":"最后更新人时间",
			"createDate":"创建时间",
			"updateDate":"最后更新时间"，
		}
	]
}
```

### 3.3.2 用户详情
- /sys/users/{userId}
- get
- users:info
- response

```
{
	"id":"",
	"account":"用户名",
	"name":"用户姓名"
}
```

### 3.2.3 新增用户

- /sys/users
- post
- users:add
- request

```
{
	"account":"账号",
	"password":"密码",
	"name":"姓名"
}
```

### 3.2.4 更新用户
- /sys/users/{userId}
- put
- users:modify
- response

```
{
	"name":"用户姓名"
}
```

### 3.2.5 删除用户

- /sys/users/{userId}
- users:delete
- delete

### 3.2.6 修改密码

- /sys/{userId}/password/change
- put
- users:password:change
- request

```
{
	"oldPwd":"旧密码",
	"password":"新密码"
}
```

### 3.2.7 重置密码

- /sys/{userId}/password/reset
- users:password:reset
- put

### 3.2.8 选择角色

- /sys/users/{userId}/roles/choose
- put
- users:roles:choose
- request

```
{
	"list":[
		{
			"roleId":"角色id"
		}
	]
}
```


#4. 基础模块

## 4.1 上传图片
### 4.1.1 获取签名
- /upload/preload
- get
- response

```
{
	"sign":"加密token",
	"timestamp":"时间戳"
}
```

### 4.1.2 上传图片
- /upload
- post
- header

header_name|header_value|Description
---|---|---
content-type|multipart/form-data| 多格式表单

- Form Parameter

key|value|Description
---|---|--
file|文件|文件流
timestamp|时间戳|预上传接口返回
token|加密字符串|预上传接口返回的sign字段

- reponse

```
{
	"url":"上传成功后图片地址"
}
```
