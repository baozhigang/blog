---
title: Hexo遇到的坑
date: 2021-10-28
categories:
- web
tags:
- hexo
- nodejs
---

🍗 换电脑之后重新部署Hexo，遇到一些坑，整理一下

<!--more-->

1. Hexo在node高版本下，部署不成功。最后安装的node版本为 v12.0, OK了

2. 很多不同的软件都不支持node的最新版本，这是大坑。今天才知道可以用 nvm 来管理不同版本的node，可以来回切换。

3. 当hexo命令不被识别，需要重新安装：npm install hexo-cli -g
如果还没效果，那就卸载node，然后重新安装node，然后hexo

4. 博客代码部署上，使用的是Hexo的一键部署，hexo -d 把 public 目录推送到github上仓库1，覆盖静态文件。然后整个项目关联到github上仓库2.
这样项目代码由仓库2维护，git pull/push. 博客静态文件由仓库1部署展示。

5. Hexo主题themes/next 文件夹无法提交到GitHub:
    - 原因：themes/next也是从仓库里拉取下来的 他关联到了作者的git仓库, 所以提交不上去
    - 解决方法:
    a) 从暂存区删除该文件夹: git rm --cache themes/next
    b) 把 themes/next/.git文件夹重命名为themes/hexo-theme-icarus/.git.bak
    c) git add .   git commit -m "add themes" 即可

6. 具体建站，看[官网手册](https://hexo.io/zh-cn/docs/)

