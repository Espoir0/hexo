---
title: Thymeleaf
date: 2019-08-02
tags:
- front-end
categories:
- front-end
---
Thymeleaf
<!-- more -->
标准表达式语法
大多数Thymeleaf属性允许将它们的值设置为或包含表达式，由于它们使用的方言，我们将其称为标准表达式。这些表达式可以有五种类型:

    ${...} : 变量表达式。
    *{...} : 选择表达式。
    #{...} : 消息 (i18n) 表达式。
    @{...} : 链接 (URL) 表达式。
    ~{...} : 片段表达式。