## 命令行工具 gel

如何安装和使用:  [https://docs.geldata.com/learn/cli](https://docs.geldata.com/learn/cli)

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
