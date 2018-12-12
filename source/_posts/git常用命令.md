---
title: git常用命令
date: 2018-07-18 16:05:02
tags:
- 快捷键
- Git
categories:
- 快捷键
---

#### 常用git命令

切换到git路径
```cmd
$ cd <folder>
```
git命令 | 描述
---|---
git branch  |查看本地分支
git branch -r |查看远程分支
git branch zm-develop |建立本地分支
git push origin zm-develop:zm-develop|将本地新的分支推送到git远程分支
git fetch origin zm-develop:zm-develop|拉取远程分支并创建本地分支
git fetch origin zm-develop:zm-develop|拉取远程分支并创建本地分支
git checkout zm-develop|切换到本地分支
git remote -v|查看当前在哪一个远程仓库  
git branch -d zm-develop|删除分支zm-develop
git config --list|查看配置信息  
git status|查看项目状态信息  
git log |查看提交日志
git reset --hard commit_id | 完成撤销,同时将代码恢复到前一commit_id 对应的版本
git reset commit_id  |  完成Commit命令的撤销，但是不对代码修改进行撤销，可以直接通过git commit 重新提交对本地代码的修改

<!--more-->