---
title: How To Do Code Reviews (Lite)
date: 2019-09-18 16:26:56
categories:
    - 记录
    - 编程
tags:
    - 记录
---

# English

**—— Growing code review culture, not system.**

## Why

- Improve code quality and readable
- Share knowledge and coding skills
- Give constructive feedback to help teammates grow

Note: Lookers-on see more than players

## When

Ready to merge and complete the following points:

- Self-Review
- Code Linting / Formatting (IDE)
- SonarQube Scan
- Automated Testing

## Principles 

- Does reviewer understand what the code does easily?
- Does the code follow our coding standards?
- Review fewer than 30 minutes
- Check no more than 400 lines at a time
- Make sure the code is correct, security, simple and readable (Not your way of doing it)
- Give feedback that helps face to face (No hurts or personal preference)

Note: There's no such thing as perfect code

## Checklist

- Coding Standards
- [Design Patterns](https://github.com/sohamkamani/javascript-design-patterns-for-humans) (JavaScript)
- [Quality Attributes](https://syndicode.com/2018/05/03/12-software-architecture-quality-attributes/)
    - Reliability
    - Maintainability
    - Extensibility
    - Scalability
    - Readability
    - Security
    - Performance
    - ...



# 中文版

**—— 将 Code Review 培养成文化，而非设立制度。**

## 目标

- 提高代码质量和可读性
- 共享知识与编程技巧
- 给予有意义的反馈帮助小伙伴们成长

注：因为“旁观者清，当局者迷”，所以需要伙伴的帮助来发现个人的不足。

## 时机

代码待合并且完成以下步骤：

- 代码自检 / 项目自测
- 代码分析与格式化（IDE）
- Sonar 扫描
- 自动化测试

## 原则

- 代码逻辑是否简单明了？
- 代码是否符合公司规范？
- Review 时间建议少于 30 分
- 单次查阅建议少于 400 行
- 确保代码逻辑正确，安全可靠、可读性强（而非个人偏好）
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