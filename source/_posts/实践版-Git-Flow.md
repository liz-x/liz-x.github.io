---
title: 实践版 Git-Flow
date: 2019-01-22 11:30:30
categories:
    - 工具
tags:
    - git
---

## 迭代评审

需求分类：
1. 小需求：多个小需求归为一个 `feature` 分支；
2. 大需求：单个大需求使用独立 `feature` 分支。

**备注：**
前后端配合，必须沟通好所使用的 `feature`；
新建的 `feature` 分支需要在相关的 jira 或迭代任务上标注；
SQL 语句需备注对应的 `feature` 分支；
缺陷修复、问题处理，也需要标注对应的分支名。



## 迭代开发

1. `feature-xxxx` 分支通过 `master` 拉取；
   1. `feature-xxxx` 可含一个或多个小需求；
   2. 在 jira 或迭代任务上**标注对应的分支名**。
2. 开发完成后合并到 `sp-dev` 上测试环境自测；
   1. 代码提交 comment 必须清晰说明更新信息；
      - 标准：jira：8866 支付开发 - 优化回调、fix xxx、修改 xxx；
      - **禁止模糊、不明确和重复的 comment** ，例如：debug、测试等。
   2. 本地测试通过后再提交，不建议零零碎碎的提交代码。
3. 自测完成后，提测，且通过准入测试（自动化），由 QA 验证；
4. 测试通过后，QA 对待发布的 `feature` 归类，**（QA）决定哪天上线哪些 feature**；
   1. **QA 每天晨会同步**上线安排；
   2. 确定上线的 `feature` 后，**由 QA 指定人员新建 release 分支**，合并 `feature`；
   3. <u>开发人员不能直接合并到 release</u>。
5. 上预发布前，`master` 先合并到 `feature` ，避免上线冲突；
6. 从 `master` 拉取 `release-xxxx` ，例如：`release-20180808-01`；
7. **单个或多个** <u>feature</u>合并到 `release-20180808-01`，打 `release` tag，上预发布测试；
8. 预发布 bug 直接在 `release` 修复；
9. 预发布通过后，`release` 合并到 `master`，打 `master` tag 上生产验证；
10. 上线通过，`master` 合并到 `sp-dev`；
11. 删除 `release-xxxx`。



**备注：**
`release` 分支是唯一的，即使它有版本号，其他 `feature` 上线需要排队等待下一个 `release`。
如果存在 `hotfix` 上线，先验证 `hotfix`，合并 `hotfix`，再发布 `release`。



**tag 命名：**
release 分支：**R**20880808-01
master 分支：**M**20880808-01



## Bug 修复

1. `hotfix-xxxx` 分支通过 `master` 拉取；
2. 开发完成后在预发布验证；
3. 验证通过，`hotfix-xxxx` 合并到 `master`；
4. `master` 打 tag，上线验证；
5. 上线通过后，`hotfix-xxxx` 合并到 `sp-dev`；
6. 删除 `hotfix-xxxx`。



## 命名规则

feature-xxxx：
1. 单个大需求，以 “主责人-jira 编号”命名，例如：`feature-superman-8888`；
2. 多个小需求，以“主责人-当前时间”命名，例如：`feature-superman-20180808`；
3. 跨迭代需求，以“主责人-专项代号”命名，例如：`feature-superman-bankdeduction`。



hotfix-xxxx：
1. 小 bug，“主责人-时间”，例如：`hotfix-superman-20180808`；
2. 大 bug，“主责人-模块”，例如：`hotfix-superman-bankdeduction`。



release-xxxx：
1. “时间+版本”，例如：`release-20180808-01`。



**备注：**命名规则加上“主责人”，方便上线后各自清理分支。

