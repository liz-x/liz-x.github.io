---
title: Travis CI 构建
date: 2019-04-07 22:51:50
categories:
    - 工具
tags:
---

## 环境准备

### GitHub Token

登陆 `GitHub`–`Settings` 选项，找到 `Personal access tokens`页面：
1. [https://github.com/settings/tokens](https://github.com/settings/tokens)
2. [Oauth 权限说明](https://developer.github.com/apps/building-oauth-apps/understanding-scopes-for-oauth-apps/)

Token 只显示一次，需要记录好，如果丢失只能重新生成。

![](https://ws3.sinaimg.cn/large/006tNc79gy1g02lydhjsjj31hm0u0jug.jpg)

### Travis CI 添加 Token

添加 `Environment Variables`：
Name：变量名，建议与 GitHub 保持一致
Token：GitHub personal access token

**不勾选** `Display value in build log`，否则会在日志文件中暴露 `token` 信息，且日志文件是公开可见。

![](https://ws4.sinaimg.cn/large/006tNc79gy1g02lz55e9bj31y00eugmc.jpg)

![](https://ws2.sinaimg.cn/large/006tNc79gy1g02lyosx7zj31xo0g0aal.jpg)

### Travis SSH Key

**本地安装 Travis**

先安装 `ruby`，[安装教程](./install-ruby.md)。

```sh
# 安装 travis
gem install travis

# travis 登录，使用 GitHub 用户名及密码
# --auto 自动登录
travis login --auto
```

**生成 ssh key**

1. 生成 `travis`，`travis.pub`

   ```sh
   cd ~/.ssh
   ssh-keygen -t rsa -C "travis" -f travis
   ```

2. 拷贝公钥 `travis.pub` 的内容到服务器 `/home/www-data/.ssh/authorized_keys`（使用非 root 账户）

3. 通过 `travis` 加密私钥

   ```sh
   # -r：仓库
   travis encrypt-file /home/www-data/.ssh/travis -r username/repository

   # 输出配置：openssl aes-256-cbc -K $encrypted_xxx_key -iv $encrypted_xxx_iv -in travis.enc -out ~\/.ssh/travis -d
   # 去掉“\”，复制到 .travis.yml
   ```

   **Travis** `Environment Variables` 会生成两个参数：

   - encrypted_xxx_key
   - encrypted_xxx_iv

4. 将 `travis.enc`，添加到 `master` 分支的根目录下



## travis.yml 配置

[官方文档](https://docs.travis-ci.com/)

`master` 分支根目录下，创建 **.travis.yml**：

```yaml
# 说明
# $TRAVIS_BUILD_DIR：默认，Travis 的全局变量
# $DEPLOY_HOST：改成你的域名或 IP
# $DEPLOY_HOST：改成你的构建用户名，例如：www-data
# $DEPLOY_PATH：改成你的构建路径，例如：/websvr/www/blog
# $encrypted_xxx_key、$encrypted_xxx_iv：改成你 travis 的环境变量 key，例如：$encrypted_123_key、$encrypted_123_iv
# 示例：https://github.com/liz-x/liz-x.github.io/blob/master/.travis.yml

language: node_js # 语言
node_js: stable # node 版本
branches:
    only:
        - master # 监测 master 分支
install:
    - true # 跳过 install 阶段
script:
    - true # 跳过 script 阶段
before_deploy:
    - openssl aes-256-cbc -K $encrypted_xxx_key -iv $encrypted_xxx_iv -in travis.enc -out ~/.ssh/id_rsa -d
    - chmod 600 ~/.ssh/id_rsa
addons:
    ssh_known_hosts: $DEPLOY_HOST
deploy:
    on:
        branch: master
    provider: script
    skip_cleanup: true
    script: rsync -r --delete-after --quiet $TRAVIS_BUILD_DIR/* $DEPLOY_USER@$DEPLOY_HOST:$DEPLOY_PATH
```

