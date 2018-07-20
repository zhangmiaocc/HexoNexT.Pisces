---
title: Mac_OSX快捷键以及命令行
date: 2018-07-12 18:32:41
tags:
- blog
- markdown
- 博客 
- 快捷键
- Mac
categories:
- 快捷键
---

### Mac OSX 快捷键
快捷键|描述
---|:--:
ctrl+shift                                  |  快速放大dock的图标会暂时放大，而如果你开启了dock放大
Command+Option+W           |     将所有窗口关闭
Command+W                          |   将当前窗口关闭(可以关闭Safari标签栏,很实用) 
Command+Option+M           |     将所有窗口最小化 
Command+Q                           |  关闭当前应用程序(相当于Dock鼠标右键推出.很实用) 
Command+M                          |   将目前使用的窗口最小化 
Command+H                          |   隐藏当前窗口或者软件
Command＋tab                       |  为切换当前工作任务 
Control＋Command＋S        |     切换控制条的显示和隐藏
Command＋i                           |   常规信息（显示及设置图标属性） 
Command＋delete                |     移到废纸篓（删除） 
Optionion+鼠标                      |   拖图像或文件夹可以将图像或文件夹拷贝到其它文件夹中，而不是移动
Command+Shift+backspace  |   清空废纸篓(再加上option一起按能跳过确认对话框)
Command+N                            | 键可以建立新文件夹 “return”或“enter”或“O”键可以打开所选项目
Command+Option+esc           |  键可以强行退出死机程序 
Command+Shift+3                  |  截图(当前屏幕)
Command+Shift+4                 |  截图(自由选取范围)
Option＋F12                            | 关机窗口(能选择关机、重起、睡眠) 
Command+1                            | 以图标方式显示
Command+2                             |以分栏方式显示
Command+3                            | 以列表方式显示
Command+4                             |以Cover Flow方式显示
return或enter                           | 键可以编辑所选图像或文件夹的名称


### Mac OSX 命令行
#### 目录操作
命令名  |功能描述 |使用举例
---|:--: |
mkdir               |         创建一个目录                            |           mkdir dirname
rmdir                |         删除一个目录                            |           rmdir dirname
mvdir                |        移动或重命名一个目录               |          mvdir dir1 dir2
cd                       |     改变当前目录                                 |      cd dirname
pwd                    |      显示当前目录的路径名                |          pwd
ls                        |      显示当前目录的内容                     |        ls -la

#### 文件操作
命令名  |功能描述 |使用举例
---|:--: |
cat               |                       显示或连接文件                   |    cat filename
od                 |                      显示非文本文件的内容        |    od -c filename
cp                  |                    复制文件或目录                     |   cp file1 file2
rm                |                     删除文件或目录                     |    rm filename
mv                |                    改变文件名或所在目录           |    mv file1 file2
find                |                  使用匹配表达式查找文件         |    find . -name "*.c" -print
file                 |                 显示文件类型                              |  file filename

#### 选择操作
命令名  |功能描述 |使用举例
---|:--: |
head             |                 显示文件的最初几行                  |     head -20 filename
tail                 |                显示文件的最后几行                    |   tail -15 filename
cut                 |               显示文件每行中的某些域              |   cut -f1,7 -d: /etc/passwd
colrm             |               从标准输入中删除若干列              |    colrm 8 20 file2
diff                  |              比较并显示两个文件的差异            |    diff file1 file2
sort                 |            排序或归并文件                                  |    sort -d -f -u file1
uniq                |           去掉文件中的重复行                             |     uniq file1 file2
comm             |           显示两有序文件的公共和非公共行     |         comm file1 file2
wc                    |        统计文件的字符数、词数和行数            |        wc filename
nl                     |        给文件加上行号                                       |  nl file1 >file2

#### 进程操作
命令名  |功能描述 |使用举例
---|:--: |
ps          |                 显示进程当前状态                   |                  ps u
kill           |              终止进程                                     |                kill -9 30142

#### 时间操作
命令名  |功能描述 |使用举例
---|:--: |
date     |               显示系统的当前日期和时间         |                  date
cal         |                          显示日历                              |         cal 8 1996
time      |                   统计程序的执行时间                   |         time a.out

#### 网络与通信操作
命令名  |功能描述 |使用举例
---|:--: |
telnet      |                            远程登录                          |       telnet hpc.sp.net.edu.cn
rlogin       |                          远程登录                            |     rlogin hostname -l username
rsh            |           在远程主机执行指定命令                 |            rsh f01n03 date
ftp               |    在本地主机与远程主机之间传输文件    |            ftpftp.sp.net.edu.cn
rcp             |    在本地主机与远程主机 之间复制文件     |          rcp file1 host1:file2
ping           |        给一个网络主机发送 回应请求             |      ping hpc.sp.net.edu.cn
mail            |              阅读和发送电子邮件                       |                   mail
write           |           给另一用户发送报文                          |        write username pts/1
mesg           |        允许或拒绝接收报文                              |                   mesg n

#### Korn Shell 命令
命令名  |功能描述 |使用举例
---|:--: |
history      |         列出最近执行过的 几条命令及编号        |               history
r                  |       重复执行最近执行过的 某条命令              |             r -2
alias            |                给某个命令定义别名                         |         alias del=rm -i
unalias        |             取消对某个别名的定义                        |          unalias del

#### 其它命令
命令名  |功能描述 |使用举例
---|:--: |
uname         |            显示操作系统的有关信息                          |    uname -a
clear              |         清除屏幕或窗口内容                                    |    clear
env                 |       显示当前所有设置过的环境变量                   |      env
who                 |      列出当前登录的所有用户                               |     who
whoami         |         显示当前正进行操作的用户名                      |        whoami
tty                   |      显示终端或伪终端的名称                                 |        tty
stty                  |       显示或重置控制键定义                                   |     stty -a
du                    |              查询磁盘使用情况                                    |   du -k subdir
df /tmp            |              显示文件系统的总空间和可用空间
w                       |           显示当前系统活动的总信息








