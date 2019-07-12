---
title: ArrayList和LinkedList集合有什么区别？
tags:
  - Java
  - 基础
categories:
  - Java
  - 基础
abbrlink: cd624839
date: 2019-03-28 12:16:07
---

### Arraylist

底层是基于动态数组，根据下表随机访问数组元素的效率高，向数组尾部添加元素的效率高；但是，删除数组中的数据以及向数组中间添加数据效率低，因为需要移动数组。例如最坏的情况是删除第一个数组元素，则需要将第2至第n个数组元素各向前移动一位。

### Linkedlist
基于链表的动态数组，数据添加删除效率高，只需要改变指针指向即可，但是访问数据的平均效率低，需要对链表进行遍历。

### 总结：

1、对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。 
      对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。
2、各自效率问题：

tips：

ArrayList是线性表(数组)

- get()直接读取第几个下标，复杂度O(1)
- add(E)添加元素，直接在后面添加，复杂度O(1)
- add(inedx，E)添加元素，在第几个元素后面插入，后面的元素需要向后移动，复杂度O(n)
- remove()删除元素，后面的元素需要逐个移动，复杂度O(n)

linkedlist是链表的操作

- get()获取第几个元素，依次遍历，复杂度O(n)
- add(E)添加到末尾，复杂度O(1)
- add(index，E)添加第几个元素后，需要先查找到第几个元素，直接指针指向操作，复杂度O(1)
- remove()删除元素，直接指针指向操作，复杂度O(1)