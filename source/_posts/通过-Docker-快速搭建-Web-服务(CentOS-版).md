---
title: 通过 Docker 快速搭建 Web 服务(CentOS 版)
date: 2019-02-25 18:00:37
categories:
    - Linux
tags:
    - Docker
---

[git 仓库](https://github.com/liz-x/docker-server)

## 文件说明

**centos-init.sh：**
—— 系统初始化

- 更换 YUM 源
- 安装 Docker
- 安装 Docker-Compose —— 通过 YMAL 配置，定义和运行多个 Docker 应用的工具


**Dockerfile：**

dockerfile/php72/Dockerfile
本地构建镜像：docker build -t php-fpm:7.2 .


**websvr 目录：**

```sh
# 项目目录
/websvr/www # web 站点
/websvr/ssl # cert key
/websvr/log # svr 日志
/websvr/vhost # 虚拟主机
/websvr/script # 脚本

# docker-compose
/websvr/compose # compose 根目录
/websvr/compose/conf # 基础配置
/websvr/compose/demo # demo 服务

# NMP LOG 目录
/websvr/log/nginx # nginx 日志
/websvr/log/mysql # mysql 日志
/websvr/log/php # php 日志
```


**/websvr/compose：**

存放 docker-compose.ymal，在 compose 目录中可以建立不同的服务，例如：

- /websvr/compose/demo
- /websvr/compose/nginx-proxy
- /websvr/compose/api-gateway


## 搭建服务

### 初始化

```sh
cd /

# 拉取项目
git clone https://github.com/liz-x/docker-server.git websvr
cd websvr

# 删除 git 信息
rm -rf .git*

# 系统初始化（ECS 版）
sudo sh ./centos-init.sh
```

### 启动 Demo

```sh
# Demo 服务
cd /websrv/compose/demo
docker-compose up -d

# 查看容器状态
[root@zsky sky] docker-compose ps

# 测试
[root@zsky ~] curl localhost
Hello World.
```
