---
title: Mac npm、node 安装与更新
date: 2019-03-13 17:29:17
categories:
    - Mac
tags:
    - Mac
    - 工具
---

# Mac npm、node 安装与更新

## npm 安装

homebrew 方式：`brew install npm `

```bash
# 使用淘宝镜像
# 支持 npm 除了 publish 之外的所有命令
# 如果 npm 安装缓慢或失败，可使用 cnpm
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org

# 查看版本
cnpm -v
```

## 查看版本

本地版本：`npm -v`

`npm outdated` 检查模块是否已经过时

![](https://ws1.sinaimg.cn/large/006tKfTcgy1g11akrx54pj30uu05odg0.jpg)



## 更新版本

最新版本：`sudo npm -g install npm@latest `
指定版本：`sudo npm -g install npm@6.0.0`


**常用命令**

```shell
npm help # 查看某条命令的详细帮助
npm install # 安装模块
npm uninstall # 卸载模块
npm update # 更新模块
npm start # 启动模块
npm stop # 停止模块
npm restart # 重新启动模块
npm test # 测试模块
npm version # 查看模块版本
```


## node 安装
—— node 版本管理工具

### n

https://github.com/tj/n

1. 清除 node.js cache：`sudo npm cache clean -f`
2. 安装 n 工具（node.js 版本管理工具）：`sudo npm install -g n`
3. 安装稳定版：`sudo n stable`
4. 查看 node 版本：`node -v`


安装稳定版本：`sudo n stable`
安装指定版本：`sudo n v8.0.0`
删除指定版本：`sudo n rm v8.0.0`


### nvm

https://github.com/creationix/nvm

1. `brew install nvm`
2. 在 shell 配置文件（~/.bashrc 或 ~/.zshrc）中添加以下内容：
   ```shell
   # 在 shell 中使用 nvm 指令
   # 查看官方最新说明：https://github.com/creationix/nvm#manual-install
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
   ```
3. 重新source
   `. ~/.bashrc `
   `. ~/.zshrc`


安装稳定版本：`sudo nvm install stable`
安装指定版本：`sudo nvm install v8.0.0`
删除指定版本：`sudo nvm uninstall v8.0.0`


列出本地版本：`nvm ls`
切换本地版本：`nvm use v8.0.0`


### n 与 nvm

**n**

1. 不支持  win
2. node 模块，安装简单
3. n 切换版本时，共用全局的 node/npm 目录



**nvm**

1. 更加流行
2. shell 脚本，需要手动指定路径
3. 不同版本都会安装自有的 node 模块（版本间不冲突，但占用硬盘资源）



普通用户二选一即可，专业开发建议使用 nvm。
