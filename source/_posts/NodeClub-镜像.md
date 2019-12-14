---
title: NodeClub 镜像
date: 2019-12-13 23:20:31
categories: 
	- 工具
tags:
	- 工具
	- Docker
---

# DOCKER-NODECLUB

[Git Link](https://github.com/liz-x/docker-nodeclub.git)

## 说明1

**docker-install.sh**

- 安装 Docker
- 安装 Docker-Compose

**nodeclub-install.sh**

- 生成 NodeClub 镜像

**目录**

```sh
conf                # 配置
re-nodeclub         # 重写（个性化目录）
data                # 数据
file                # 文件
log                 # 日志
script              # 脚本
```

## 搭建

### 初始化

```sh
# 拉取项目
git clone https://github.com/liz-x/docker-nodeclub.git nodeclub

cd nodeclub

# 安装 Docker（如未安装）
sudo sh docker-install.sh.sh

# 生成 NodeClub 镜像
sudo sh nodeclub-init.sh

# 启动
docker-compose up -d

# 查看容器状态
docker-compose ps
```

## Mongo 设置 （可选）

```sh
# 创建 admin 账号和数据库

# Step 1
docker exec -it mongo mongo admin
# Step 2
db.createUser({ user: 'admin', pwd: '123456', roles: [ { role: 'userAdminAnyDatabase', db: 'admin' } ] }); # 创建账号
exit # 退出

# 创建 nodeclub 账号和数据库

# Step 1
docker exec -it mongo mongo admin
# Step 2
db.auth('admin', '123456');
# Step 3
use nodeclub # 不存在会自动创建
db.createUser({ user: 'nodeclub', pwd: '123456', roles: [ { role: 'readWrite', db: 'nodeclub' } ] }); # 创建账号
```

### 其他

```sh
db.grantRolesToUser( 'userName' , [ { role: '<role>', db: '<database>' } ])             # 授予角色
db.grantRolesToUser( 'userName' , [ { role: '<role>', db: '<database>' } ])             # 取消角色
db.system.users.remove({user:'userName'})                                               # 删除用户
db.changeUserPassword('userName', 'new password')                                       # 修改密码
```