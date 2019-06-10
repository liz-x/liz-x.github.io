---
title: Git 仓库完整迁移
date: 2019-04-19 16:16:45
categories:
    - 工具
tags:
    - git
---

**包括分支、标签、日志**

```shell
cd /tmp

# 克隆仓库镜像(--mirror 裸版本库)
git clone --mirror https://github.com/xxx/oldProject.git
cd oldProject.git

# 设置 origin
git remote set-url origin https://github.com/xxx/newProject.git
# 推送镜像
git push --mirror

# 修改原仓库远程地址
cd /www/oldProject
git remote set-url origin https://github.com/xxx/newProject.git
git fetch origin

# 更新项目名（如有改动）
cd ..
mv oldProject newProject
```

### 裸版本库
—— 不包含工作区

1. git clone --bare <URL>
2. git clone --mirror  <URL>

--mirror 可以在裸版本库中使用 git fetch 命令进行同步
