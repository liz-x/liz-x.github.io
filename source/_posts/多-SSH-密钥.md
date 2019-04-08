---
title: 多 SSH 密钥
date: 2019-01-30 23:57:58
categories:
    - Linux
tags:
    - Linux
    - git
---

如果已经有了一套名为 `id_rsa` 的公秘钥，还要生成另外一个公钥，比如 sky

1. 生成新公钥：
   `ssh-keygen -t rsa -C "sky" -f ~/.ssh/sky`
   -C "email" -f 公钥名
   执行完成后，会在 ~/.ssh/目录下生成一个 sky 和 sky.pub 文件。
2. 添加私钥
   `ssh-add ~/.ssh/sky`
3. 在 SSH 用户配置文件 ~/.ssh/config 中指定对应服务所使用的公秘钥名称，如果没有 config 文件的话就新建一个，并输入以下内容：

以 git 为例：

```ssh
# 默认(可不添加，仅做对比说明)
Host github.com
    IdentityFile ~/.ssh/id_rsa

# sky
Host github.com
    IdentityFile ~/.ssh/sky

# gitlab
Host gitlab.com
    IdentityFile ~/.ssh/gitlab
```

1. 添加 sky.pub 到 git 服务器网站上
2. 测试配置文件是否正常工作：ssh -T git@github.com

成功则返回：`You've successfully authenticated, but GitHub does not provide shell access.`



## 自定义 host

```shell
Host github-sky # 别名
HostName github.com # 域名
User git
IdentityFile ~/.ssh/sky
```

git remote add test git@github-sky:liz-x/test.git



## ssh-agent 代理转发

ssh-agent：ssh 代理，临时性的 session 服务
ssh-add 将私钥交给 ssh-agent 保管
说明：http://wiki.jikexueyuan.com/project/github-developer-guides/using-ssh-agent.html



查看代理： `ssh-add -l`
添加私钥： `ssh-add ~/.ssh/sky`
删除私钥： `ssh-add -d ~/.ssh/sky`
清空私钥： `ssh-add -D`
添加私钥： `ssh-add ~/.ssh/id_rsa ~/.ssh/sky`


**备注：**使用 ssh-add ~/.ssh/id_rsa 如果报异常：`Could not open a connection to your authentication agent.`，则需要先运行一下 ssh-agent bash，再执行 ssh-add ...等命令。
