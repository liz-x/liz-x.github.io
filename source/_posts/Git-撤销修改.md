---
title: Git 撤销修改（转）
date: 2019-02-11 12:05:00
categories:
    - 工具
tags:
    - git
---

# Git 撤销修改

原文：https://gb.yekai.net/questions/revert
本文对原文格式稍微调整，并补充部分细节。

**注：git 命令行代码，行内注释中标注对应的 zsh alias。**

------

## git clean

**新建的文件和目录，且从未提交至版本库。**

此类文件的状态为 `Untracked files` ，撤销方法如下：

```
git clean -fd . # gclean .
git clean test.txt # gclean text.txt 撤销指定文件
```

其中，`.` 表示当前目录及所有子目录中的文件，也可以直接指定对应的文件路径，以下其他情况类似。

-f 所有没有 track 过的文件

-d 所有没有 track 过的文件夹

## git checkout

**提交过版本库，但未提交至暂存区的文件（未执行 git add）。**

此类文件的状态为 `Changes not staged for commit`，撤销方法：

```
git checkout . # gco .
git checkout test.txt # gco text.txt 撤销指定文件
```

## git reset

**已提交至暂存区的文件**

此类文件的状态为 `Changes to be committed`，撤销方法：

```
git reset . # grh .
git reset test.txt # grh text.txt 撤销指定文件
```

执行之后文件将会回到以上的 1 或者 2 状态，可继续按以上步骤执行撤销，若 git reset 同时加上 `--hard` 参数（alias 为 `grhh`），将会把修改过的文件也还原成版本库中的版本。

```
--hard : 回退版本库、暂存区、工作区（因此我们修改过的代码就没了，需要谨慎使用）

--mixed: 回退版本库、暂存区(默认)

--soft: 回退版本库
```

## git reset <commit_hash>

**已提交至版本库（执行了 git commit）**

每次提交都会生成一个 commit hash，通过以下命令可查阅 hash id 并将其回滚：

```
git log # glol
git reset <commit_hash> # grh
```

如果需要「回滚至上一次提交」，可直接使用以下命令：

```
git reset head~1 # grh head~1
```

执行之后，再按照 1 或者 2 状态进行处理即可，如果回滚之后的代码同时需要提交至 origin 仓库（即回滚 origin 线上仓库的代码），需要使用 `-f` 强制提交参数，且当前用户需要具备「强制提交的权限」。

## git reset 与 revert 的区别

二者都是做回滚操作，不同之处在于：

`reset` 直接将 head 指向了回滚的记录，丢弃了这条记录之后所有的提交时间线；

`revert` 更类似 `cherry-pick` 的反向操作，它不会改变历史时间线。在选中某次特定的历史提交后，它在时间上新增一条提交记录，还原了那次特定提交的所有变更。

## 其他

**如果回滚了之后又不想回滚了怎么办？**

如果是以上的情况 1 或者 2，因为修改没入过版本库，无法回滚。

如果是情况 4，回滚之后通过 git log 将看不到回滚之前的 commit hash，但可通过 `git reflog` 命令（所有记录过的 commit hash）找到回滚之前的 commit hash，然后 `git reset <commit_hash>` 。
