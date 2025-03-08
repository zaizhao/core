# Zaizhao core

> Gel is a data layer designed to help you **figure out storage** in application - also fast.

## 为什么选择 Gel 数据库?

> One data model and a unified query-layer, NO JOINS, NO ORM, 

[Gel](https://geldata.com) 既继承 SQL 数据库的优势: 类型安全、高性能、可靠稳定以及支持事务处理等 (基于 [Postgres](https://www.postgresql.org/) 开发), 又充分利用面向对象的设计思路, **定义类型对象(包含属性和指向其它类型对象的链接)** 来表示数据 (Graph-Relational Database 的核心思想), 解决 SQL 在易用性和深度查询时的性能问题.

- Strict, strongly-typed **Schema**
- Built-in support for schema **migrations & branch** manage
- Powerful and clean **query language: EdgeQL**
- Auth & AI Solutions
- Full support for SQL, **fully Open Source**
- 单机或集群轻松部署, 高性能和网络连接健壮稳定

对整个应用开发流程的意义:

1. 简化在数据整理、领域知识梳理以及数据序列化方面的工作, 将逻辑和数据关联都变成数据库模式和查询的一部分, 应用程序的代码可以更专注于业务逻辑和问题领域的具体细节.
2. Access Policies 作为安全后端提供对象级安全, 将大量的查询过滤及访问控制都放在数据库层, 避免意外的数据泄露.
3. 内置简单强大的用户验证解决方案, 与数据库访问策略控制可以完全结合起来.
4. 客户端 SDK, HTTP Query 与 GraphQL 查询支持, 方便集成扩展.
5. 向量存储及 AI 能力的加持, 数据库扩展系统.

[📌 快速入门 Gel 数据库的学习文档传送门](https://zaizhao.github.io/core/gel/index.html)

## 产品文档

目录 **docs** 是**在招**的文档中心, 使用 [mdbook](https://rust-lang.github.io/mdBook/) 来管理和生成网站, 本地预览:

```bash
cd docs/
mdbook server --open # open in http://localhost:3000
```

## 本地开发

本地环境配置

## 生产部署

- 单机部署
- 生产环境
- 性能压测
- 集群部署(待学习整理)

## 版权 LICENSE

[MIT](./LICENSE)






