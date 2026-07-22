# Git基础

## 一、Git介绍

Git是一种`分布式版本控制系统`，广泛用于软件开发项目的`版本管理`。它由Linus Torvalds为了帮助管理Linux内核开发而创建，并已成为许多其他项目及开发团队使用的标准工具。

Linus Torvalds（林纳斯·托瓦兹）是一名著名的计算机程序员，他最为人所知的成就是创建了Linux操作系统内核。Linux内核是一个开放源代码的Unix-like操作系统内核，广泛应用于服务器、个人电脑和嵌入式系统等各种领域。

![99408881-8440-4adc-8224-f9a374ec6a62](./../assets/1-Git%E5%9F%BA%E7%A1%80/99408881-8440-4adc-8224-f9a374ec6a62.png)

## 二、Git配置

在commit提交之前需要先配置name和email

`.gitignore`文件用于说明哪些文件应该被Git版本管理忽略

## 三、Git开发流程

### 1、add、commit和git状态

初始化本地版本库：

```bash
	git init
```

将文件纳入Git管理：

```bash
	git add  文件名 （git add . ）
```

提交到本地（提交到本地仓库）：

```bash
	git commit -m "第一次提交"
```

查看文件当前状态：

```bash
	git status
```

![0283f33d-94d6-41f1-9fcb-b49cae2db266](./../assets/1-Git%E5%9F%BA%E7%A1%80/0283f33d-94d6-41f1-9fcb-b49cae2db266.png)

三个矩形分别代表`工作目录、暂存区域和本地仓库`，工作目录是本地`正在编写`的代码，commit命令是提交到`本地仓库`。

打开gitk图形化工具：

```bash
	gitk& #加一个&用于保持命令行页面不消失
```

![d04c31e7-430e-4fe8-b31f-5886eb5d6186](./../assets/1-Git%E5%9F%BA%E7%A1%80/d04c31e7-430e-4fe8-b31f-5886eb5d6186.png)

### 2、撤销操作

恢复所有的更改：

```bash
	git checkout	#没有做git add操作时，该命令会恢复到上一次提交，做了add操作后，会恢复到暂存区
```

恢复单个的文件：

```bash
	git checkout file
```

恢复某个目录下面所有文件：

```bash
	git checkout 目录
```

### 3、更多操作

![6d9ddab7-a5f3-40e8-bc14-9cbd4fdf2934](./../assets/1-Git%E5%9F%BA%E7%A1%80/6d9ddab7-a5f3-40e8-bc14-9cbd4fdf2934.png)

### 4、Push操作

在Push操作前，需要先设置好push地址，该地址需要从Gitee、Github或其他在线仓库获取；

设置地址完成后执行以下操作：

```bash‘
	git push -u origin "master"
```

## 四、问题解决

### 解决控制台中文乱码问题

将全局配置为`utf-8`编码：

```bash
	git config --global gui.encoding utf-8
```

而且要求Eclipse或其他编辑器的编码也是`utf-8`，只要文件的编码和git的编码都是utf-8就不会出现中文乱码的问题。

2000000000000000000002220

