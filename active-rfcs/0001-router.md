---
id: router
startDate: 2022-07-06
targetVersion: 1.x
implementation: # 留空
---

# 概述

为应用提供带出入参数校验的路由中间件并对外提供路由出入参类型信息。

# 介绍与示例

基于请求中间件的思想，作为一个路由中间件进行设计。
拥有对各种类型入参数信息进行校验以及对数据出口进行过滤的功能。

```ts
const router = new Router()
  .get('/users/:uid', ctx => ctx.query.uid)
  .post(Schema.User, Schema.Number, '/users', ctx => ctx.body.uid)
```

# 动机

该模块为项目的核心设计模块，在后端采取 Node.js 的情况下，可以通过该模块直接生成前端可直接使用的路由类型信息。
除此之外还可对 quester 模块的功能进行基础能力校验，并扩宽工具的影响范围。

# 详细设计

包括以下几个部分进行设计：
* 路由器的构造参数
* 路由器应当支持的 HTTP Method 类型：GET、POST、PUT、DELETE、PATCH
* 路由器对入参的校验
  * `request body`：支持 json、form、raw text、file
  * `query`：格式为 `?[name[(type=string)]]`
  * `path params`：格式为 `:name[(type)]`
* 路由器对出参的过滤，将路径响应函数的返回值根据 `schema` 进行过滤
* 对应用传入的 `Context` 的修改，如 `request.query`、`request.body` 等

```ts
interface RouterOptions<P extends string> {
  prefix?: P
}
type Method = 'get' | 'post' | 'put' | 'delete' | 'patch'
// 下面为伪代码
interface Router<P extends string, Docs> {
  docs: Docs
  new(opts?: RouterOptions<P>): Router<P, {}>
  [p in Method]: <Inn, Out, P>(
    inn: Inn, out: Out,
    path: P, middleware: Middleware<Inn, Out>
  ) => Router<P, Resolve<P, Out, Inn>>
}
```

# 缺点

设计中尽量容易让用户从原本的 Router 中切换过来，所以在设计过程中会携带一部分的负担。

# 备选项

无。

# TODO

* 对自定义参数类型的支持
* 对请求的 body 等数据进行处理
* 对 WS 的支持
