---
title: 修改NexT_Pisces主题内容区宽度
date: 2018-07-12 02:48:25
tags:
- hexo
- blog
- markdown
- 博客
- 建站
categories:
- 建站
---

### 默认的宽度觉得有点窄，想改宽一点，手动修改样式
在`source/css/_schemes/Picses/_layout.styl`文件末尾添加如下代码。

```javascript
// 以下为新增代码
header{ width: 90% !important; }
header.post-header {
  width: auto !important;
}
.container .main-inner { width: 90%; }
.content-wrap { width: calc(100% - 260px); }

.header {
  +tablet() {
    width: auto !important;
  }
  +mobile() {
    width: auto !important;
  }
}
<!-- more -->
.container .main-inner {
  +tablet() {
    width: auto !important;
  }
  +mobile() {
    width: auto !important;
  }
}

.content-wrap {
  +tablet() {
    width: 100% !important;
  }
  +mobile() {
    width: 100% !important;
  }
}
```
