---
title: 'android:label=REPLACE and android:label=REPLACE'
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: c46aabc2
date: 2019-01-04 15:10:56
---

Multiple entries with same key: 

尝试从`tools:replace`列表中删除空格。

```
tools:replace="android:label,theme,allowBackup,android:icon,android:supportsRtl"
```

这为我修复了构建错误，但我仍在试图找出为什么忽略空格后的条目.

<!--more-->

