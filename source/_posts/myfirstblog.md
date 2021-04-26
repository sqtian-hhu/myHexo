---
title: myfirstblog
date: 2020-02-07 15:51:53
tags: 代码管理
categories: 代码管理
---

搭建hexo并部署到github
<!--more-->

## 搭建hexo记录

Windows：下载并安装 Node.js (Node.js 版本需不低于 8.10，建议使用 Node.js 10.0 及以上版本)
				   Git					
root shift+ctrl+enter进入 管理员模式cmd

全局安装cnpm
npm install -g cnpm --registry-https://registry.npm.taobao.org 

cnpm安装hexo
cnpm install -g hexo-cli

mkdir blog
C:\Windows\System32\blog

新建hexo
hexo init
启动hexo
hexo s

ctrl +c 退出

创建第一篇博客
hexo n ‘myfirstblog’

hexo clean
hexo g

## 部署到github
github Create a new repository  sqtian-hhu.github.io

blog路径下安装
C:\Windows\System32\blog>cnpm install hexo-deployer-git--save

打开 _config.yml 进行设置
notepad _config.yml
对# Deployment做如下修改
deploy:
  type:'git'
  repo: https://github.com/sqtian-hhu/sqtian-hhu.github.io.git
  branch: master
  
部署
hexo d

## 更改主题
神海4
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

notepad _config.yml
将Extensions下theme：后改为 yilia

hexo clean
hexo g
hexo s

hexo d



