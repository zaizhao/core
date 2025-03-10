# 📄 数据模型 Schema, Migration & Branches

**数据模型定义**是业务系统的基石, Gel 提供强类型、高级的 SDL(Schema definition language)语言, 以**在招**的业务系统为例: 

[Login to Gel WebUI for dev server](https://8.140.249.106:5656/ui)

### 基础概念

数据都用 **类型对象(type Object)** 来定义:

```python
# 可以省略的关键字: property、optional、single、link
abstract property email_prop {      # Abstract property
    readonly := true;                 # 只读属性,不允许修改
}

type Player {
    property required email: str {  # str is a Scalar Type
        extending email_prop;
    }; 
    # link 代表类型对象之间的关系, 是有方向的, 1-n 或 m-n 
    # Player(source) -> multi Game(target)
    optional multi link games: Game {
        # ensures a one-to-many relationship
        constraint exclusive;       # 约束条件: 唯一
        on source delete allow;     # deletion policy 
    };
}

type Game {
    required name: str {            # required 指必填属性
        default := 'Unknown';       # 设置默认值
    };
    index on (.name) {              # 设置索引
        annotation description := 'Indexing all users by name.';
    };                  
}
```
- [Properties 属性](https://docs.geldata.com/reference/datamodel/properties) | [Links 链接](https://docs.geldata.com/reference/datamodel/links) | [Primitives 原始类型](https://docs.geldata.com/reference/datamodel/primitives)
- [Constraints 条件约束](https://docs.geldata.com/reference/stdlib/constraints#ref-std-constraints) | [ Indexes 索引](https://docs.geldata.com/reference/datamodel/indexes) 
- [Computed properties and links](https://docs.geldata.com/reference/datamodel/computeds) | [Link properties](https://docs.geldata.com/reference/datamodel/linkprops#ref-datamodel-linkprops)

### Abstract 抽象与 [Inheritance 继承](https://docs.geldata.com/reference/datamodel/inheritance)

```python
# declare reusable, user-defined constraint types.
abstract constraint in_range(min: anyreal, max: anyreal) {
  errmessage :=
    'Value must be in range [{min}, {max}].';
  using (min <= __subject__ and __subject__ < max);
}
abstract link link_with_strength {
  strength: int64 {
    constraint in_range(0, 100);
  };
  index on (__subject__@strength);
}
abstract type Person {
  name: str;
  multi friends: Person {
    extending link_with_strength;
  };
}

abstract annotation admin_note; # 自定义注释
type Student extending Person {
  overloaded name: str {    # overloaded property
    constraint exclusive;
  }
  overloaded multi friends: Student;
  annotation admin_note := 'system-critical';
}
```

### [Triggers 触发器](https://docs.geldata.com/reference/datamodel/triggers) 与 [Mutation rewrites](https://docs.geldata.com/reference/datamodel/mutation_rewrites)

用审计日志(Audit Log)的例子来学习使用触发器:

```python
type Log {
    action: str;
    timestamp: datetime {
        default := datetime_current();
    }
    target_name: str;
    change: str;
}

type Person {
    required name: str;
    # also for insert or delete
    trigger log_update after update for each
    when (<json>__old__ {**} != <json>__new__ {**})
    do (insert Log {
            action := 'update',
            target_name := __new__.name,
            change := __old__.name ++ '->' ++ __new__.name
        });
}
```
- Triggers may also be used for validation.

操作重写可以方便我们在数据插入和更新时进行拦截, 对数据进行修改;

```python
type Post {
  required title: str;
  required body: str;
  modified: datetime {
    rewrite insert, update using (datetime_of_statement())
  }
}
```

### [Access Policies](https://docs.geldata.com/reference/datamodel/access_policies) 安全策略: Object-level Security

Access policies can greatly simplify your backend code, centralizing access control logic in a single place.

```python
global current_user: uuid;
type User {
  required email: str { constraint exclusive; }
}
type Post {
  required title: str;
  required author: User;

  access policy author_has_full_access
    allow all
    using (global current_user ?= .author.id) {
      errmessage := "User does not have full access";
    }
}
# Policy types: all | select | insert | update | delete | update read | update write
```

### 工具能力: [Aliases](https://docs.geldata.com/reference/datamodel/aliases)、[Globals](https://docs.geldata.com/reference/datamodel/globals)、[Functions](https://docs.geldata.com/reference/datamodel/functions)、[Annotations](https://docs.geldata.com/reference/datamodel/annotations)

```python
# Alias
alias digits := {0,1,2,3,4,5,6,7,8,9};
alias Account := User;

# Globals
required global now := datetime_of_transaction();
global current_user_id: uuid;
global current_user := (
  select User filter .id = global current_user_id
);

# Functions
function add_user(name: str) -> User
  using (
    insert User {
      name := name,
      joined_at := std::datetime_current(),
    }
  );

# Annotations 
type Status {
  annotation title := 'Activity status';
  annotation description := 'All possible user activities';

  required name: str {
    constraint exclusive
  }
}
```

### 数据库扩展 [Extensions](https://docs.geldata.com/reference/datamodel/extensions#create-extension)

```bash
gel extension list
```

## **Migrations** 

可以记录数据库 Schema 的变更历史, 随时回滚, 并将模型 SDL 自动转换为数据库可以理解的 DDL(data definition language) 序列, 方便在开发过程中轻松的改进数据模型. 

👉 [Guide to Gel migrations](https://docs.geldata.com/resources/guides/migrations)

```bash
gel migration create    # 生成迁移记录
gel migrate             # 应用迁移到数据库
```

## **Branch** 

代码仓库分支的概念同理, 可以使用 ```gel branch``` 命令方便地创建、删除和切换分支, 简化开发协作流程. 

👉 [Branches references](https://docs.geldata.com/reference/datamodel/branches#ref-datamodel-branches)

## **Module** 

可以理解为一个命名空间, 复杂项目可以用 module 更好的划分 Schema 定义. 

👉 [Module references](https://docs.geldata.com/reference/datamodel/modules#ref-datamodel-modules)