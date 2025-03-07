# Zaizhao core

> Gel is a data layer designed to help you **figure out storage** in application - also fast.

## 为什么选择 Gel 数据库?

> One data model and a unified query-layer, NO JOINS, NO ORM, 

既继承 SQL 数据库的优势: 类型安全、高性能、可靠稳定以及支持事务处理等 (基于 [Postgres](https://www.postgresql.org/) 开发), 又充分利用面向对象的设计思路, **定义类型对象(包含属性和指向其它类型对象的链接)** 来表示数据 (Graph-Relational Database 的核心思想), 解决 SQL 在易用性和深度查询时的性能问题.

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

了解更多: https://geldata.com

## 命令行工具 gel

如何安装和使用:  https://docs.geldata.com/learn/cli

1) Instance: 管理数据库实例

```bash
# Action: create | list | logs
gel instance create new_instance --version 6.1

# Manage: status | start | stop | restart | destroy
gel instance status -I new_instance

# 在指定实例运行查询 Query
gel query "select 3.14" -I new_instance

gel instance --help # 更多功能
```

2) Project: 让本地开发更简单.

**Project** 可以把本地的项目文件夹和数据库实例关联起来.

```bash
gel project init # 初始化项目: link instance
# Generate: gem.toml、dbschema(*.gel & migrations)

# 可以手动指定关联数据库实例, 支持远程实例
gel instance link
gel project unlink # 取消关联, -D 参数可以同时删除实例

gel project info    # 查看项目信息
gel project upgrade --help # 升级实例版本

# 进入数据库查询 REPL
gel     # 等同于 gel -I {instance_name}
```

3) 数据库可视化管理:  ```gel ui```

## 数据模型: Schema, Migration & Branches 

**数据模型定义**是整个系统的基石, Gel 提供强类型、高级的 SDL(Schema definition language)语言, 以**在招**的业务系统为例: 

```sql
# file: dbschema/default.gel
module default {
    type Domain {
        required name: str;
    }
    type Company {
        required name: str {
            constraint exclusive;
        };
        # 比如人工智能、机器人, 公司可能在多个领域
        multi domains: Domain;
    }
    type Position { 
        required name: str;
        required link company: Company;
    }
}
```
- 基础概念: 数据都用 **类型对象(type Object)** 来定义, 包含[属性(Properties)](https://docs.geldata.com/reference/datamodel/properties)和[链接(Links)](https://docs.geldata.com/reference/datamodel/links);
    - [Primitives](https://docs.geldata.com/reference/datamodel/primitives): Scalar、Enums、Arrays、Tuples、Ranges、Sequences
    - Computed properties and links
    - Link properties
- [索引 Indexes](https://docs.geldata.com/reference/datamodel/indexes) 与[约束 Constraints](https://docs.geldata.com/reference/datamodel/constraints)
- 类型对象支持抽象(Abstract)和[继承(Inheritance)](https://docs.geldata.com/reference/datamodel/inheritance);
- [触发器 Triggers](https://docs.geldata.com/reference/datamodel/triggers) 与 [Mutation rewrites](https://docs.geldata.com/reference/datamodel/mutation_rewrites)
- 对象级安全策略: [Access Policies](https://docs.geldata.com/reference/datamodel/access_policies)
- 工具能力: [Aliases](https://docs.geldata.com/reference/datamodel/aliases)、[Globals](https://docs.geldata.com/reference/datamodel/globals)、[Functions](https://docs.geldata.com/reference/datamodel/functions)、[Annotations](https://docs.geldata.com/reference/datamodel/annotations)
- 数据库扩展 [Extensions](https://docs.geldata.com/reference/datamodel/extensions#create-extension)

**Migrations** 可以记录数据库 Schema 的变更历史, 随时回滚, 并将模型 SDL 自动转换为数据库可以理解的 DDL(data definition language) 序列, 方便在开发过程中轻松的改进数据模型.  👉 [Guide to Gel migrations](https://docs.geldata.com/resources/guides/migrations)

```bash
gel migration create    # 生成迁移记录
gel migrate             # 应用迁移到数据库
```

**Branch** 代码仓库分支的概念同理, 可以使用 ```gel branch``` 命令方便地创建、删除和切换分支, 简化开发协作流程. 👉 [Branches references](https://docs.geldata.com/reference/datamodel/branches#ref-datamodel-branches)

**Module** 可以理解为一个命名空间, 复杂项目可以用 module 更好的划分 Schema 定义. 👉 [Module references](https://docs.geldata.com/reference/datamodel/modules#ref-datamodel-modules)


## 查询语言: EdgeQL

EdgeQL 是比 SQL 强大的下一代查询语言, 设计原则:
- Compatible with modern languages;
- Strongly typed;
- Designed for programmers;
- Easy deep querying;
- Composable;

EdgeQL 不仅提供各种编程语言的 SDK, 也可以通过 HTTP 或 GraphQL 调用查询.
- 快速学习: https://docs.geldata.com/learn/edgeql
- Client Libraries: https://docs.geldata.com/reference/clients

## 内置的 Auth 解决方案 🔐

大多数应用都需某种形式的用户认证, 通常的办法是自己造轮子重复开发, 或选择 Auth0 或者 Clerk 这样的托管服务.

直接使用 Gel 内置、易于集成的 Auth 解决方案也是一个选择:
- 利用内置的身份验证 UI 可以快速搭建应用, 可随时替换为定制的 UI;
- 深度集成: 部署 Gel 意味着您同时部署了 Auth(启用扩展即可), 无需复杂的配置环节;
- access policies for role-based access control
- layer multiple authentication factors onto a single unified User type
- full control over how your authentication data is stored and managed.
- 多种用户验证方式支持(可自行扩展)
- 相对于托管服务按照活跃用户或者验证次数收费, 成本更低;

![](https://www.geldata.com/_images/_blog/b6381c0388a044cf6102402abfb02fe5a204c067-940-2x.webp)

```
using extension auth;
```

Custom Authentication API:
- Login in: POST /authenticate
    - body(json): challenge | email | password | provider | redirect_to | redirect_to_on_signup
    - response: { code }
- Register user: POST /register
    - body(json): challenge | email | password | provider | verify_url
    - response: { code, identity_id }
- Get auth token: GET /token
    - params: code | verifier
    - response: { auth_token, id_token, provider_token }
- Verify Email Link: POST /verify
    - body(json): verification_token | provider
    - response: { code }
- Send Reset-Password Email: POST /send-reset-email
    - body(json): email | provider | reset_url | challenge
    - response: { email_sent }
- Reset Password: POST /reset-password
    - body(json): reset_token | password | provider
    - response: { code }

Magic Link Auth:
- Register user: POST /magic-link/register
    - body(json): challenge | email | provider | callback_url | redirect_on_failure
- Sign In with email: POST /magic-link/email
    - body(json): challenge | email | provider | callback_url
- Get auth token

WebAuthn:
- Get the options for registering: GET /webauthn/register/options
    - params: email
- GET /webauthn/authenticate/options
    - params: email
- POST /webauthn/register
    - body(json): provider | email | credentials | verify_url | user_handle | challenge
- POST /webauthn/authenticate
    - body(json): provider | email | assertion | challenge
    - response: { code }

用户注册成功后与用户进行绑定(1-1 or 1-m):

```
type User {
    required email: str;
    name: str;

    required identity: ext::auth::Identity {
        constraint exclusive;
    };
}
...

# After POST /register response
if ("identity_id" in registerJson) {
  await client.query(`
    with
      identity := <ext::auth::Identity><uuid>$identity_id,
      emailFactor := (
        select ext::auth::EmailFactor filter .identity = identity
      ),
    insert User {
      email := emailFactor.email,
      identity := identity
    };
  `, { identity_id: registerJson.identity_id });
} 
```

> 如何增加身份验证方式? 比如验证码或者扫码登录.

相关阅读:
- OAuth 2 Explained: https://www.youtube.com/watch?v=ZV5yTm4pT8g
- How PKCE Works: https://cloudentity.com/developers/basics/oauth-extensions/authorization-code-with-pkce/
- System Design: https://bytebytego.com/
- NextJS Template: https://github.com/geldata/nextjs-gel-auth-template
- Rust Example: https://github.com/dustypomerleau/audit/blob/main/src/auth.rs

## AI 集成方案

基于 OpenAI 构建一个 Ask Bot 的 RAG 应用.
- https://docs.geldata.com/learn/guides/ai/edgeql
- https://docs.geldata.com/learn/tutorials/ai_fastapi_searchbot
- https://docs.geldata.com/resources/guides/tutorials/chatgpt_bot
- https://www.geldata.com/blog/chit-chatting-with-edgedb-docs-via-chatgpt-and-pgvector

Embedding 向量嵌入
- pgvector: https://docs.geldata.com/reference/stdlib/pgvector

## 集群部署

- 单机部署
- 生产环境
- 性能压测
- 集群部署(待学习整理)





