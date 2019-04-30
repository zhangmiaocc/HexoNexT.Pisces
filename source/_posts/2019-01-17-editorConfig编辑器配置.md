---
title: editorConfig编辑器配置
tags:
  - editorConfig
  - 前端
categories:
  - 前端
  - tips
abbrlink: 6d832a8b
date: 2019-01-17 11:08:32
---

当多人团队进行一个项目开发时，每个人可能喜欢的编辑器不同，有人喜欢**Webstrom**、有人喜欢**sublime**、还有人喜欢**Hbuilder**。

这个时候，问题便迎面而来，如何使使用不同编辑器的开发者能够轻松惬意的遵守最基本的代码规范呢？

最后终于找到了**editorConfig**这个东东，发现在这里配置的代码规范规则优先级高于编辑器默认的代码格式化规则。比如我使用的是**Webstrom**编辑器，我每一次写完代码之后，都习惯性的按下**“Ctrl+Alt+L”**快捷键去整理代码格式。如果我没有配置**editorconfig**，执行的就是编辑器默认的代码格式化规则；如果我已经配置了**editorConfig**，则按照我设置的规则来，从而忽略浏览器的设置。

下面说说它的常用配置和使用方法：

<!--more-->

### 常用配置
- （1）**charset**  编码格式
- （2）**indent_style**  缩进方式
- （3）**indent_size**  缩进大小
- （4）**insert_final_newline** 是否让文件以空行结束
- （5）**trim_trailing_whitespace** 自动删除文件末尾空白行
- （6）**max_line_length**  疑似最大行宽

### 使用方法
- （1）下载相关编辑器的**editorconfig**插件。
- （2）在项目根目录下，新建**.editorconfig**文件。
- （3）配置规范，如下图所示：

```
# http://editorconfig.org
root = true

[*]
#缩进风格：空格
indent_style = space
#缩进大小2
indent_size = 2
#换行符lf
end_of_line = lf
#字符集utf-8
charset = utf-8
#是否删除行尾的空格
trim_trailing_whitespace = true
#是否在文件的最后插入一个空行
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false

[Makefile]
indent_style = tab

```

### 官方网站
- atom网站：[https://atom.io/packages/editorconfig](https://link.jianshu.com?t=https%3A%2F%2Fatom.io%2Fpackages%2Feditorconfig)
- GitHub：[https://github.com/sindresorhus/atom-editorconfig](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fsindresorhus%2Fatom-editorconfig)

### 使用建议
配合代码检查工具使用，比如说：**ESLint**和**TSLint**，统一代码风格。

### 支持的编辑器

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190430172555.png)

虽然editor编辑器的可用配置并不多，但目前现有的一些配置也差不多能满足最基本格式需求。