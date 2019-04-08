---
title: PHPStorm PSR-2 使用指南
date: 2019-01-13 21:22:08
categories:
    - 工具
tags:
    - PHP
---

## PHPCS

PHP Code Sniffer 简称 PHPCS，用来检查你写的 PHP 是否符合 PSR-2 ，若完全符合则没有任何结果，若有任何错误将显示错误报告。

这适合帮我们检查 Legacy PHP 是否符合 PSR-2。



1. 安装

   composer global require'squizlabs/php_codesniffer=* 或 pear install PHP_CodeSniffer

2. 测试

   ```
   root@ykj-sp-test:~# phpcs --version
   PHP_CodeSniffer version 3.0.2 (stable) by Squiz(http://www.squiz.net)
   root@ykj-sp-test:~#
   ```

   如果 MacOS 报错：

   ```
   PHP Warning: include_once(PHP/CodeSniffer/autoload.php): failed to open stream: No such file or directory in /usr/local/bin/phpcs on line 14

   PHP Warning: include_once(): Failed opening 'PHP/CodeSniffer/autoload.php' for inclusion (include_path='.:/usr/share/pear') in /usr/local/bin/phpcs on line 14

   PHP Fatal error: Uncaught Error: Class 'PHP_CodeSniffer\Runner' not found in /usr/local/bin/phpcs:17
   ```

   php.ini 引入 pear：

   ```
   echo 'include_path = ".:'`pear config-get php_dir`'"' | sudo tee -a /etc/php.ini
   ```

3. 检查 PSR-2

   `phpcs --standard=PSR2 XXX/app`

   1. XXX 为你的项目名称，一般我们写的 code 都在 app 目录下，所以直接指定 PHP Code Sniffer 帮我们检查 app 目录下所有的 .php 文件。
   2. PHP Code Sniffer 预设的 coding style 为 PEAR，因为我们用的是 PSR-2，所以要特别使用加上 --standard=PSR2。



### PHP-CS-Fixer

PHP Coding Standards Fixer 简称 PHP-CS-Fixer。

虽然 PHP Code Sniffer 可以帮我们找出哪些 code 不符合 PSR-2，若只有几个文件，我们手动改就可以，若文件太多，就得依赖 PHP-CS-Fixer 帮我们修正。



1. 安装

   `composer global require fabpot/php-cs-fixer`

2. 测试

   ```
   root@ykj-sp-test:~# php-cs-fixer --version
   PHP CS Fixer 2.5.0 Ancient Lizard by Fabien Potencier and Dariusz Ruminski (63aad57)
   root@ykj-sp-test:~#
   ```

3. 修正PSR-2

   `php-cs-fixer fix Laravel/app/ --rule=@PSR2 --verbose`

   1. rule=@PSR2 指定使用 PSR-2 标准来修正我们的 code。
   2. verbose 表示 PHP-CS-Fixer 在执行时，会显示出详细的结果。



### PHPStorm 配置

PhpStorm 对于 PSR-2 有以下支持 :

- 原生支持 PSR-2
- 可外挂 PHP Code Sniffer，让我在写 code 的同时就可提醒我们是否符合 PSR-2
- 可外挂 PHP Coding Standards Fixer，将 code 修正为 PSR-2


1、设置使用PSR-2规范

Preferences -> Editor -> Code Style -> PHP : Set from… -> Predefined Style : PSR1/PSR2

![img](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/1544515133-1.png)

还可以在 PSR-2 的基础上继续设置自己的规则。



2、设置phpcs

A. Preferences -> Languages & Frameworks -> PHP -> Code Sniffer

![img](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/1544515133-2.png)

PS：这里以我的 phpcs 路径为例：/usr/local/bin/phpcs



B. Preferences -> Editor -> Inspections

将 PHP Code Sniffer validation 打勾。

将 Coding standard 选 PSR2。

![img](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/1544515133-3.png)



3、PHP-CS-Fixer

Tools -> External Tools 添加 PHP-CS-Fixer

![img](http://yunshequ00.oss-cn-hangzhou.aliyuncs.com/ipic/1544515133-4.png)



Program:

`/usr/local/bin/php-cs-fixer`

Arguments:

`fix $FileDir$/$FileName$ --rules=@Symfony,@PSR1,@PSR2`

Working Directory:

`$ProjectFileDir$`



不实用缓存 --using-cache no

排除自己不想应用的规则，在规则前加上"-"即可
