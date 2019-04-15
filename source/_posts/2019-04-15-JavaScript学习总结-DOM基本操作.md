---
title: JavaScript学习总结-DOM基本操作
tags:
  - 前端
  - JavaScript
categories:
  - 前端
  - JavaScript
abbrlink: af67cb0c
date: 2019-04-15 17:41:18
---

## 一、获取节点

### 1、document

- **getElementById**
  - 语法：**document.getElementById(元素id)**
  - 功能：通过元素**ID**获取节点
- **getElementByName**
  - 语法：**document.getElementByName(元素name属性)**
  - 功能：通过元素的**name**属性获取节点
- **getElementByTagName**
  - 语法：**document.getElementByTagName(元素标签)**
  - 功能：通过元素标签获取节点

<!--more-->

### 2、节点指针

- **firstChild**
  - 语法：**父节点.firstChild**
  - 功能：获取元素的首个子节点
- **lastChild**
  - 语法：**父节点.lastChild**
  - 功能：获取元素的最好一个子节点
- **childNodes**
  - 语法：**父节点.childNodes**
  - 功能：获取元素的子节点列表
- **previousSibling**
  - 语法：**兄弟节点.previousSibling**
  - 功能：**获取已知节点的前一个节点**
- **nextSibling**
  - 语法：**兄弟节点.nextSibling**
  - 功能：获取已知节点的后一个节点
- **parentNodes**
  - 语法：**子节点.parentNodes**
  - 功能：**获取已知节点的父节点**

## 二、节点操作

### 1、创建节点

- **createElement**
  - 语法：**document.createElement(元素标签)**
  - 功能：创建元素节点
- **createAttribute**
  - 语法：**documen.createAttribute(元素属性)**
  - 功能：创建属性节点
- **createTextNode**
  - 语法：**document.createTextNode(文本内容)**
  - 功能：创建文本节点

### 2、插入节点

- **appendChild**
  - 语法：**appendChild(所添加的新节点)**
  - 功能：向节点的子节点列表的末尾添加新的子节点
- **insertBefore**
  - 语法：**insertBefore(所要添加的新节点，已知子节点)**
  - 功能：在已知子节点钱插入一个新的子节点

### 3、替换节点

- **replaceChild**
  - 语法：**replaceChild(要插入的新元素，将被替换的老元素)**
  - 功能：将某个子节点替换为另一个

### 4、复制节点

- **cloneNode**
  - 语法：**需要被复制的节点.cloneNode(true/fasle)**
  - 功能：创建置顶节点的副本
  - 参数
    - **true**：复制当前节点及其所有子节点
    - **false**：仅复制当前节点

### 5、删除节点

- **removeChild**
  - 语法：**removeChild*(要删除的节点)**
  - 功能：删除指定的节点

## 三、属性操作

### 1、获取属性

- **getAttribute**
  - 语法：**元素节点.getAttribute(元素属性名)**
  - 功能：获取元素节点中指定属性的属性值

### 2、设置设置属性

- **setAttribute**
  - 语法：**元素节点.setAttribute(属性名,属性值)**
  - 功能：创建或改变元素节点的属性

### 3、删除属性

- **removeAttribute**
  - 语法：**元素节点.removeAttribute(属性名)**
  - 功能：删除元素中的指定属性

## 四、文本操作

- **insertData(offset,string)** ：从offset指定的位置插入string
- **appendata(sring)** ：将string插入到文本节点的末尾处
- **deleteData(offset,count)** ：从offset起删除count个字符
- **replaceData(off,count,string)** ：从off将count个字符用string替代
- **splitData(offset)**：从offset起将文本节点分成两个节点
- **substring(offset,count)**：返回由offset起的count个节点

