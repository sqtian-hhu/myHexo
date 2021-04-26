---
title: '''github_upload'''
date: 2020-11-14 16:43:06
tags: 代码管理
categories: 代码管理
---

github上传项目

<!--more-->

1. 下载git工具 
	https://gitforwindows.org/
	
2. 右击本地文件夹选择Git Bash Here
3. github仓库clone or download里复制的地址
	git clone  https://github.com/sqtian-hhu/shiqing.git
4. 本地文件夹中会多出一个与仓库名一样的文件夹
	把要上传的代码文件夹复制到该仓库文件夹中
5. 进入本地仓库文件夹
	cd shiqing
6. git add .
7. git commit  -m  "项目描述"
8. git push -u origin 自己当前的分支名
	git push -u origin master
9. 输入github用户名和密码
