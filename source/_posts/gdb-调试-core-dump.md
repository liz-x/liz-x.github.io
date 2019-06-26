---
title: gdb 调试 core dump
date: 2019-06-26 20:15:41
categories:
    - 工具
    - Linux
    - PHP
tags:
    - PHP
    - Linux
---

## 何为 core dump

**core dump** 中文翻译为“核心转储”，它是进程运行时突然崩溃的那一刻的内存快照。

> 操作系统在程序发生异常而异常在进程内部又没有被捕获的情况下，会把进程此刻内存、寄存器状态、运行堆栈等信息转储保存在一个文件里，可使用 gdb 工具来分析。
>
> core dump 生产环境一般处于禁用状态，对于内存消耗性的进程，core dump 时会占用很多系统资源，磁盘空间也可能被写满。



查看核心转储文件大小的最大值：`ulimit -c`，如果是 0，则表示禁止核心转储。



## 开启 core dump

#### 临时开启

- **开启 core dump**：
  - `ulimit -c 1024`（限制文件大小为 1024k）
  - `ulimit -c unlimited`（不限制文件大小）
- **关闭 core dump**：`ulimit -c 0`



#### 自动开启

1. 打开 `/etc/profile`，尾部追加 `ulimit -S -c unlimited > /dev/null 2>&1`；
2. 执行 `source /etc/profile` 使配置生效；
3. 通过 `ulimit -c` 查看下是否已经打开。



**备注：**由于核心转储文件比较大，因此建议不要限制 core 文件的大小，当调试完毕之后关闭 core dump 即可。



打开 core 后，如果进程发生 crash，默认情况下，会在应用所在位置，产生一个【core.pid】文件。

查看正在使用的 core 文件路径和格式 `more /proc/sys/kernel/core_pattern`，后面自动添加 pid 的配置是在 `more /proc/sys/kernel/core_uses_pid` 里面配置的，如果为 1 就是自动添加。



**修改 core 文件的保存路径和文件名格式：**

修改 /etc/sysctl.conf 文件，添加需要保存的路径 `kernel.core_pattern = /tmp/corefile/core.%e.%t`（要有写的权限），再执行命令 `sysctl -p` 即可生效。

core 文件支持的格式列表：

```shell
%p – insert pid into filename
%u – insert current uid into filename
%g – insert current gid into filename
%s – insert signal that caused the coredump into the filename
%t – insert UNIX time that the coredump occurred into filename
%h – insert hostname where the coredump happened into filename
%e – insert coredumping executable name into filename
```



以 PHP 为例，开启 core dump 一段时间的运行之后，调试生成的 core 文件：

> 进入存放 core 文件的目录，假设 php 安装在 /usr/local/php/ 下面，core 文件为 core.666：
>
> gdb /usr/local/php/bin/php -c core.666



## 调试 core dump

gdb 的 3 种使用方式：

- 跟踪正在运行的 PHP 程序，使用 gdb -p 进程 ID
- 使用 gdb 运行并调试 PHP 程序，使用 `gdb php -> run server.php` 进行调试
- PHP 程序发生 core dump 后使用 gdb 加载 core 内存镜像进行调试 `gdb php corefile`



执行 gdb 后，进程会变成 T 的状态，表示正在 Trace，这个是独占的，所以不能再使用 strace/gdb 或者其他 ptrace 工具对此进程进行调试。另外此进程会中断执行，gdb 输入 c 后，程序继续向下运行，按下 ctrl + c 则会中断程序，通过 bt 命令查看进程的调用栈。



### 常用指令

- `p`：print，打印 C 变量的值
- `c`：continue，继续运行被中止的程序
- `b`：breakpoint，设置断点，可以按照函数名设置，如 `b zif_php_function`，也可以按照源代码的行数指定断点，如 `b src/networker/Server.c:1000`
- `t`：thread，切换线程，如果进程拥有多个线程，可以使用 t 指令，切换到不同的线程
- `ctrl + c`：中断当前正在运行的程序，和 c 指令配合使用
- `n`：next，执行下一行，单步调试
- `info threads`：查看运行的所有线程
- `l`：list，查看源码，可以使用 `l 函数名` 或者  `l 行号`
- `bt`：backtrace，查看运行时的函数调用栈
- `finish`：完成当前函数
- `f`：frame，与 bt 配合使用，可以切换到函数调用栈的某一层
- `r`：run，运行程序



## zbacktrace

zbacktrace 是 PHP 源码包提供的一个 gdb 自定义指令，功能与 bt 指令类似，与 bt 不同的是 zbacktrace 看到的调用栈是 PHP 函数调用栈，而不是 C 函数。

下载 php-src，解压后从根目录中找到一个 `.gdbinit` 文件，在 gdb shell 中输入

```
source .gdbinit
zbacktrace
```

.gdbinit 还提供了其他更多指令，可以查看源码了解详细的信息。



### 使用 gdb + zbacktrace 跟踪死循环问题

```
gdb -p 进程ID
```

- 使用 `ps aux` 工具找出需要调试的进程 ID
- `gdb -p` 跟踪指定的进程
- 反复调用 `ctrl + c` 、`zbacktrace`、`c` 查看程序在哪段 PHP 代码发生异常
- 找到对应的 PHP 代码进行解决



**参考：**

http://imhuchao.com/1018.html

https://wiki.swoole.com/wiki/page/442.html

https://www.cnblogs.com/paul8339/p/8967689.html
