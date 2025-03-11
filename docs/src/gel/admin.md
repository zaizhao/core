# 🛠️ 系统管理

### 🎭 角色管理

```sql
# 创建角色, not possible to create non-superuser roles for now.
create superuser role project;
# 无密码登录(通常用于本地数据库开发)
configure instance insert Auth {
    comment := 'passwordless access',
    priority := 1,
    method := (insert Trust),
};

# 设置密码登录
alter role project
    set password := 'super-password';
# 增加一个优先级更高的 Auth 方法
configure instance insert Auth {
    comment := 'password is required',
    priority := 0,
    method := (insert SCRAM),
    user := 'project'
};

# 移除 Auth Method 方法
configure instance reset Auth filter Auth.method is Trust;

gel -I {INSTANCE_NAME} --user project --password
```

- **configure 命令**的[使用说明](https://docs.geldata.com/reference/reference/admin/configure#ref-eql-statements-configure);
- [Roles Reference](https://docs.geldata.com/reference/reference/admin/roles#ref-admin-roles)

### gel-server 及环境变量的数据

```bash
gel info

#  a catchall for logs and various caches.
| Cache   │ /root/.cache/edgedb/    
# contains auto-generated credentials          
│ Config  │ /root/.config/edgedb/   
│ Install │ /root/.local/bin/     
# contains the contents of all local Gel instances.          
│ Data    │ /root/.local/share/edgedb/data/
# the home for running processes/daemons. 
│ Service │ /root/.config/systemd/user/  
```

环境变量
```bash
GEL_SERVER_USER
# If set to anything other than the default username admin, the username specified will be created. The user defined here will be the one assigned the password set in GEL_SERVER_PASSWORD or the hash set in GEL_SERVER_PASSWORD_HASH.

GEL_SERVER_SECURITY
# When set to insecure_dev_mode, sets GEL_SERVER_DEFAULT_AUTH_METHOD to Trust, and GEL_SERVER_TLS_CERT_MODE to generate_self_signed (unless an explicit TLS certificate is specified). Finally, if this option is set, the server will accept plaintext HTTP connections.
GEL_SERVER_TLS_CERT_FILE/GEL_SERVER_TLS_KEY_FILE
GEL_SERVER_TLS_CERT_MODE
# require_file | generate_self_signed
```

More info: https://docs.geldata.com/reference/reference/environment

### 安全和性能层面的最佳实践

- [PostgreSQL in 100 Seconds](https://www.youtube.com/watch?v=n2Fluyr3lbc)
- Video: https://www.youtube.com/watch?v=SpfIwlAYaKk
- [This PostgreSQL tutorial helps you quickly understand PostgreSQL.](https://neon.tech/postgresql/tutorial)
- https://www.geeksforgeeks.org/postgresql-tutorial/
- https://www.crunchydata.com/developers/tutorials