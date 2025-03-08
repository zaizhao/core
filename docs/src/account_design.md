# 账号体系 

在招平台会有两大类账号 **求职者用户 User 与管理账号 Account** , 未来会有两个不同的应用入口:

| 账号类型 | 应用入口 |
|----------|----------|
| User     | zaizhao.ren |
| Account  | platform.zaizhao.ren |

但是两类账户会由同一个 **Auth Service** 提供用户验证服务及资源访问控制策略;

## 用户角色 User

用户可以访问前端应用, 但是无法登录管理后台, 与 Account 账号数据是独立的;

## 管理账号角色 Account

### 平台 Platform

系统初始化时自动生成**唯一**的平台角色账号, 提供重置账号的接口能力; 

平台账号等同于超级管理员角色;

### 企业 Company

平台账号可以对企业账号申请入驻流程进行审核管理;

企业可自行进行发起注销企业流程;

### 账号 Employer

用户主动在平台注册账号即可以成为普通管理账号(员工👋);

可以申请进行企业账号认证, 审核通过后该账号可以升级为企业角色账号;

## 群组 Group

平台账号和企业账号均可以创建群组 Group, 邀请其他人加入为群组成员;

群组成员 Member 可以拥有不同的数据权限级别:

| 群组成员 Member | 数据权限级别 |
|-----------------|--------------|
| Admin           | 高级权限      |
| Normal          | 普通权限      |

## Access Policies 资源访问策略

| 账号类型 | 角色 🎭 | 资源  | 访问控制 |
|---------|--------|------|---------|
| Account | Platform  |  Company  |  Read,Edit |
| - | Company  |  Company  |  Read,Edit |
| - | Employer  |  Company  |  Read |
| - | Employer  |  Company  |  Read |
| - | Member(admin)  |  Company  |  Read |
| - | Member(normal)  |  Company  |  Read |
| User |    User   |  Company   |  Read  |


## 其它

暂无.
