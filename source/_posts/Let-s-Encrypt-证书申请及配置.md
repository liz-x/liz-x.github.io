---
title: Let's Encrypt 证书申请及配置
date: 2019-02-12 16:25:57
categories:
    - 工具
tags:
    - SSL
---

## 简介

[Let's Encrypt](https://letsencrypt.org/) —— 是一个由非营利性组织 [互联网安全研究小组](https://letsencrypt.org/isrg/)（ISRG）提供的免费、自动化和开放的证书颁发机构（CA），简单的说，就是为网站提供免费的 SSL/TLS 证书。



## 签发工具

Let's Encrypt 生成证书的[工具](https://letsencrypt.org/docs/client-options/)很多，[certbot](https://certbot.eff.org/) 是官方推荐的签发工具，也可以通过在线服务申请，例如：

- [FreeSSL](https://freessl.cn/)（不支持自动续签）
- [七牛](https://www.qiniu.com/ssl)（不支持 DV 泛域名）
- [又拍云](https://www.upyun.com/products/ssl)（不支持 DV 泛域名）

在线一站式服务管理方便，但可能需要绑定业务，个人用户还是推荐通过工具生成证书。
这里推荐 [acme.sh](https://github.com/Neilpang/acme.sh)，它不仅有详细的[中文文档](https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E)，操作更为方便，还支持 [Docker](https://github.com/Neilpang/acme.sh/wiki/Run-acme.sh-in-docker)。



## acme.sh

以 root 用户为例

### 在线安装

```sh
curl https://get.acme.sh | sh
```

安装过程：

1. `home`  目录下生成 `.acme.sh` 文件夹，查看文件夹内容：`ls ~/.acme.sh/`；

2. 自动添加 bash alias：

   ```sh
   # ~/.bashrc 添加：
   . "/root/.acme.sh/acme.sh.env"

   # acme.sh.env 内容：
   # alias acme.sh="/root/.acme.sh/acme.sh"
   ```

3. 自动添加定时任务（时间随机）：

   ```sh
   # 每天凌晨检查证书的有效期，如有需要，自动续签。
   30 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
   ```

### 相关命令

```sh
# 查看帮助
acme.sh -h

# 查看列表
acme.sh --list

# 卸载 acme.sh
# 编辑 ~/.bashrc，删除 acme.sh alias
acme.sh --uninstall
```

### 脚本更新

自动更新：`acme.sh --upgrade --auto-upgrade`
手动更新：`acme.sh --upgrade`
关闭更新：`acme.sh --upgrade --auto-upgrade 0`


## 证书申请

域名所有权验证方式：HTTP 和 DNS

- HTTP：在网站的根目录下添加一个文件
- DNS：在域名上添加一条 txt 解析记录

### HTTP

acme.sh 会自动生成验证文件，并放到站点的根目录，然后自动完成验证，最后自动删除文件。

- -d：同 `--domain`，域名
- -w：同 `--webroot`，站点根目录

```sh
acme.sh --issue -d mydomain.com -d www.mydomain.com -w /websvr/mydomain.com/
```

Nginx：自动从配置中获取站点的根目录

```sh
acme.sh --issue -d mydomain.com --nginx
```

**注：根据中国大陆工信部的规定，所有托管在中国大陆服务器上的网站均需要备案。**

—— 站点如未备案，80 端口处于禁用状态，acme.sh 无法使用 HTTP 验证域名所有权。

### DNS

优势：不需要服务器与公网 ip，只要配置 DNS 解析即可。
不足：必须配置 Automatic DNS API 才可以自动续签。

```sh
# https://github.com/Neilpang/acme.sh/wiki/dns-manual-mode
# 注：DNS 不支持自动续签

# 步骤：
# 1. 生成相应的解析记录
# 2. 域名解析中添加生成的 txt 记录
# 3. 等待解析其生效
acme.sh --issue --dns -d mydomain.com --yes-I-know-dns-manual-mode-enough-go-ahead-please

# 查询域名 txt 记录
nslookup -type=txt _acme-challenge.mydomain.com

# 解析生效后，重新生成证书(注：确实是 renew)
acme.sh --renew -d mydomain.com --yes-I-know-dns-manual-mode-enough-go-ahead-please
```

#### DNS API（推荐）

根据域名服务商，选择对应的 [DNS API](https://github.com/Neilpang/acme.sh/blob/master/dnsapi/README.md)。



阿里云：[控制台](https://usercenter.console.aliyun.com/#/manage/ak)

创建 Accesskey

```sh
# 获取到 Key 和 Secret 后，设置环境变量
export Ali_Key="123"
export Ali_Secret="abc"

# 生产证书
acme.sh --issue --dns dns_ali -d mydomain.com -d www.mydomain.com
```



DNSPod：[控制台](https://www.dnspod.cn/console/user/security)（注：非腾讯云控制台）

创建 API Token

```sh
# 获取到 ID 和 Key 后，设置环境变量
export DP_Id="123"
export DP_Key="abc"

# 生产证书
acme.sh --issue --dns dns_dp -d mydomain.com -d www.mydomain.com
```

### 通配符证书

 Wildcard 证书，目前只支持 `DNS-01` 验证方式。

```sh
# 阿里 DNS API
acme.sh --issue --dns dns_ali -d mydomain.com -d *.mydomain.com
```



## 证书配置

生成的证书都在 `home` 目录下: `~/.acme.sh/`

### 导出证书

使用 `--install-cert` 命令

- -d：域名
- –key-file：私钥位置
- –fullchain-file：证书位置
- –reloadcmd：重载命令

```sh
acme.sh --install-cert -d mydomain.com \
        --key-file /websvr/ssl/mydomain.key \
        --fullchain-file /websvr/ssl/fullchain.cer \
        --reloadcmd "docker exec -t nginx nginx -s reload"
```

### Nginx 配置

```sh
server {
    listen 80;
    server_name mydomain.com;
    # 重定向到 https
    rewrite ^(.*)$  https://$host$1 permanent;
}

server {
    listen 443;
    server_name mydomain.com;
    root   /websvr/www/mydomain.com;
    index  index.html index.htm;

    access_log /dev/null;
    error_log  /websvr/log/nginx/mydomain.com.error.log warn;

    # SSL 配置
    ssl on;
    ssl_certificate /websvr/ssl/fullchain.cer; # 证书文件
    ssl_certificate_key /websvr/ssl/mydomain.key; # 私钥文件
    ssl_session_timeout 5m; # 会话缓存过期时间
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # 开启 SSL 支持
    ssl_prefer_server_ciphers on; # 设置协商加密算法时，优先使用服务端的加密套件
}
```

重启 Nginx 服务：

```sh
docker exec -t nginx nginx -s reload
```

### SSL 检测

[https://myssl.com](https://myssl.com)

### 更新证书

定时任务会自动更新

强制续签证书：`acme.sh --renew -d mydomain.com --force`

