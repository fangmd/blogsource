---
title: vim -- 基本操作 
date: 2016-03-15 21:38:14
tags: vim
categories: program

---

# vim 学习记录

## 0 在终端

	vimtutor

打开vim教程

vim命令行

	：h

查看帮助

## 1 光标移动

	hjkl

## 2 vim的进入和退出

- `:q!`:退出不保存
- `:wq`:退出保存

## 3 文本删除
在正常模式下：

	x

## 4 文本编辑之插入

	i
	A

## 5 编辑文件

	:wq //保存

## 6 删除类命令
在正常模式下

- `dw`:删除一个单词
- `d$`:从当前位置删除到末行


## 7 关于命令和对象

	d motion

d	- 删除操作符
motion	- 操作符的操作对象

- `w`：从当前位置到下个单词的起始处
- `e`：当前光标到单词末尾
- `$`：从当前光标到当前行末尾

## 8 使用计数删除更多

	d number motion


## 整行操作


## 跳转
- 0: to end of line
- $: to start of line
