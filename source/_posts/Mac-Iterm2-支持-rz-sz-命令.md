---
title: Mac Iterm2 支持 rz sz 命令
date: 2019-02-20 12:56:32
categories:
    - Mac
tags:
    - Mac
    - 工具
---

# Mac Iterm2 支持 rz sz 命令

## 安装 lrzsz

`brew install lrzsz`

## 配置 iterm2

### 脚本

拉取 [https://github.com/mmastrac/iterm2-zmodem](https://github.com/mmastrac/iterm2-zmodem) 两个 sh 文件，拷贝到 /usr/local/bin 文件夹中。

一定要赋予执行权限：

`sudo chmod +x /usr/local/bin/iterm2*`

### 配置

打开 iterm2，Preferences -> Profiles -> Default -> Advanced 的 tab 页 -> Triggers - Edit，添加两条规则：

```
Regular expression: rz waiting to receive.\*\*B0100
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-send-zmodem.sh

Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
```

![](https://ws2.sinaimg.cn/large/006tKfTcgy1g0aeayrm04j31cb0u0ac1.jpg)
![](https://ws4.sinaimg.cn/large/006tKfTcgy1g0ae9gpl8tj31c00u00u6.jpg)

### 命令说明

文件上传：rz

文件下载：sz filename filename1 …

