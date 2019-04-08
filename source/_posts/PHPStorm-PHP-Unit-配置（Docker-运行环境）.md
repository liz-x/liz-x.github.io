---
title: PHPStorm PHP-Unit 配置（Docker 运行环境）
date: 2019-01-05 13:30:30
categories:
    - 工具
tags:
    - PHP
---

## 准备工作

- 下载 [PHPStorm 2018](https://www.jetbrains.com/phpstorm/)
- 下载 [phpunit.phar](https://phar.phpunit.de/) (根据 PHP 版本选择下载)
- Docker 镜像要安装好 Xdebug
- docker-compose 新建一个用于 debug 的容器，避免与原 Web 容器冲突



## 配置

### 【1】确认 PHPStorm 已安装 Docker 插件

![docker 插件](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-150125@2x.png)

------

### 【2】添加 Docker 配置

Build, Execution, Deployment -> Docker

![docker 配置](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-151748@2x.png)

------

### 【3】添加解释器

Languages & Frameworks -> PHP

![cli 配置](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-152329@2x.png)

![cli 添加](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-152720@2x.png)

------

### 【4】添加 phpunit 配置

![phpunit 配置1](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-150456@2x.png)

![phpunit 配置2](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-151513@2x.png)

![phpunit 配置3](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-151553@2x.png)

![phpunit 配置4](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-151314@2x.png)

![phpunit 配置5](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-154947@2x.png)



## 运行

![phpunit 运行1](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-160254@2x.png)

![phpunit 运行2](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-160402@2x.png)

![phpunit 运行3](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/phpunit/WX20180517-160821@2x.png)
