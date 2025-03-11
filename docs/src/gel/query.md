## 查询语言: EdgeQL

EdgeQL 是比 SQL 强大的下一代查询语言, 设计原则:
- Compatible with modern languages;
- Strongly typed;
- Designed for programmers;
- Easy deep querying, NO JOINS;
- Composable;

简单的查询示例:

```sql
# insert, select, filtering, ordering, pagination
with new_movie := (insert Movie { title := 'Ne Zha', release_year := 2021 })
select Movie {
  id, title, release_year, actors,
  title_upper := str_upper(.title),
  cast_size := count(.actors)
} 
filter .release_year > 2020 and .title ilike "ne zha%"
order by .title
offset 0 limit 10;

# update or delete action
update Movie
filter .title = "Doctor Strange 2"
set {
  actors += (select Person filter .name = "Rachel McAdams")
};

# group 语句
group Movie { title, actors: { name }}
by .release_year;
```

#### Everything is a set

> All values in EdgeQL are actually **sets 集合**: a collection of values of a given type. All elements of a set **must have the same type**. 

```sql
select distinct {'a', 'a', 'b', 'c'};   # 集合去重
select 'd' in {'a', 'b', 'c'};          # 是否存在于一个集合
select {1, 2, 3} union {3, 4};          # 合并集合
select {1, 2, 3} intersect {3, 4};      # 集合交集
select <str>{} ?? 'default';            # 集合为空时取默认值
```
#### 事务处理 Atomic Transactions

> https://docs.geldata.com/reference/edgeql/transactions

```typescript
client.transaction(async tx => {
  await tx.execute(`update BankCustomer
    filter .name = 'Customer1'
    set { bank_balance := .bank_balance -10 }`);
  await tx.execute(`update BankCustomer
    filter .name = 'Customer2'
    set { bank_balance := .bank_balance +10 }`);
});
```
#### HTTP & Client Libraries

**EdgeQL over HTTP**

启用扩展, 记得创建并应用迁移:
```bash
# dbschema/default.gel
using extension edgeql_http;

# terminal
curl -G https://<cloud-instance-host>:<cloud-instance-port>/branch/main/edgeql \
 -H "Authorization: Bearer <secret-key>" \
 -H "X-EdgeDB-User: admin" \
 --data-urlencode "query=select Person {*};"

 # Support POST & GET Methods, fields:
 - query
 - variables
 - globals
```

[Error Code at Response](https://docs.geldata.com/reference/reference/protocol/errors#ref-protocol-error-codes)

**GraphQL**'

[GraphQL Basic](https://docs.geldata.com/reference/clients/graphql/graphql)

启用扩展, 记得创建并应用迁移:
```bash
# dbschema/default.gel
using extension graphql;

# terminal
curl \
  -H "Content-Type: application/json" \
  -X POST http://localhost:10787/branch/main/graphql \
  -d '{ "query": "query getMovie($title: String!) { Movie(filter: {title:{eq: $title}}) { id title }}", "variables": { "title": "The Batman" }, "globals": {"default::current_user": "04e52807-6835-4eaa-999b-952804ab40a5"}}'
```

Explore the GraphQL schema and write queries: https://github.com/graphql/graphiql

- Endpoint: http:/localhost:port/branch/main/graphql/explore

**Client Libraries**

不同语言的客户端 SDK 基本都会实现一个 Client 客户端类:
- Manages a pool of physical connections to your Gel instance.
- Resolving connections: [GEL_DSN](https://docs.geldata.com/reference/reference/dsn#ref-dsn)
- Executing queries, Under the hood, this query is executed using Gel's efficient binary protocol.

Client Libraries: https://docs.geldata.com/reference/clients

Connection parameters: https://docs.geldata.com/reference/reference/connection#ref-reference-connection

#### 其它

- 快速学习: https://docs.geldata.com/learn/edgeql
- Reference: https://docs.geldata.com/reference/edgeql
- 查询分析: https://docs.geldata.com/reference/cli/gel_analyze#ref-cli-gel-analyze
