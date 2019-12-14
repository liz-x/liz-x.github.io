---
title: Scrum 小册子 - 如何做代码审查（Code Review - Lite）
date: 2019-09-18 16:26:56
categories:
	- Agile
	- 编程
tags:
	- Agile
	- Scrum
	- 敏捷
	- 编程
---

**—— 将 Code Review 培养成文化，而非设立制度。**

## 目标

- 提高代码质量和可读性
- 共享知识与编程技巧
- 给予有意义的反馈帮助小伙伴们成长

注：因为“旁观者清，当局者迷”，所以需要伙伴的帮助来发现个人的不足。



## 时机

代码待合并且完成以下步骤：

- 代码自检 / 项目自测
- [Linter](https://www.zhihu.com/question/28421865) 检查与代码格式化（IDE）
- [Sonar](https://www.sonarqube.org) 扫描
- [自动化测试](https://www.w3cschool.cn/position/pdk20m.html)



## 原则

- 代码逻辑是否简单明了？
- 代码是否符合公司规范？
- 单次 Review 时间建议少于 30 分，不超过 60 分（可分批 Review）
- 单次查阅建议少于 400 行，不超过 1000 行
- 确保代码逻辑正确，安全可靠、可读性强（而非个人偏好）
- 不要在 Review 中讨论需求（Review 是 Review，Planning 是 Planning）
- 面对面给予正向反馈（文明用语，尊重彼此，保持互相学习的心态）

注：没有完美的代码，不要纠结。



## 清单

- 代码规范
- [设计模式](https://github.com/sohamkamani/javascript-design-patterns-for-humans) (JavaScript)
- [质量属性](https://syndicode.com/2018/05/03/12-software-architecture-quality-attributes/)
    - 可靠性
    - 可维护性
    - 可扩展性
    - 可伸缩性
    - 可读性
    - 安全
    - 性能
    - ...