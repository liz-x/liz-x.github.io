---
title: PHP 编程规范
date: 2019-01-25 18:10:30
categories:
    - 编程
    - 规范
tags:
    - PHP
    - 后端
    - 规范
---

## 代码要求

1. 代码分层 —— 整洁、面向对象
   - module-controller-service-repository-model
   - module：模块 —— 业务分层
   - controller：控制器 —— 输入处理，调用 service
   - service：服务 —— 业务逻辑处理，调用 repository
   - repository：仓库 —— 数据持久化封装、聚合根（切割实体的相依性），调用 model
   - model：数据访问层（entity/实体）
2. 代码格式 —— 基于语言规范格式化
   [**PHP FIG**](https://psr.phphub.org/)
   - [PSR-1](https://laravel-china.org/topics/2078)
   - [PSR-2](https://laravel-china.org/topics/2078)
3. 代码规范 —— sonar 监控
4. 关键目录（非框架内目录）
   - config：项目配置
   - bootstrap：框架启动（可选）
   - commands：控制台命令、脚本运行
   - utils：工具类（静态），与业务无关
   - components：业务组件
   - events：事件处理
   - sdk：第三方工具包
   - tests：单元测试
5. 增量数据禁止联表查询 —— 水平扩展（分表）
6. 命名方式：
   驼峰式：Camel case
   蛇式：Snake Case
   烤肉串式：Kebab Case
   1、 类名命名：**大**驼峰，如：class RoomService {}；
   2、 方法命名：小驼峰，如：public function getRoomById($id = '') {}；
   3、 对象命名：小驼峰，如：$this->baseService = new BaseService()；
   4、 常量命名：**大**蛇式，如：const MAX_NUM = 100、define('MAX_NUM', 100)；
   5、 变量命名：小驼峰，如：$roomId；
   6、 函数命名：小蛇式，如：function random_str($length = 10) {}
   7、 URL 命名：烤肉串，如：https://xxx.com/user/get-info-by-id
