---
title: ubuntu下hexo+next博客搭建
categories: 博客管理
tags: [hexo,next,npm,node.js]

---
> 我使用的环境是ubuntu16.04，其他环境未做验证。

# 安装npm
```
sudo apt-get install npm
sudo apt-get install nodejs-legacy //安装后有/usr/bin/node，不安装会导致npm安装hexo失败
```
# 修改npm源
> 添加npm淘宝源，修改完在当前账户下默认使用这个源。

```
vim ~/.npmrc
registry = https://registry.npm.taobao.org
```
# 安装hexo
```
sudo npm install -g hexo-cli
```
# 初始化hexo
```
mkdir blog
cd blog
hexo init
npm install
npm install hexo-deployer-git --save
```
# 安装next主题
```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

# 修改hexo配置文件
> hexo配置文件中只有以下几项是需要关注的，其他的暂时不需要用到，如果需要配置其他功能请查看官网帮助。
    
```
//site
title:
subtitle: 
description:
author: 
language:zh-Hans
timezone:
//highlight
highlight:
  enable: true
//theme
theme: next
//deploy
deploy:
- type: git
  repo: git@github.com:user/user.github.com.git
  branch: master
```

# 修改next主题配置
> next配置文件中只有以下几项是需要关注的，其他的暂时不需要用到，如果需要配置其他功能请查看官网帮助。例如需要增加本站搜索和评论功能，需要做额外配置，在后边章节我们会介绍。

```
menu:
  home: /
  categories: /categories
  #about: /about
  archives: /archives
  tags: /tags
  #sitemap: /sitemap.xml
  #commonweal: /404.html
```
```
scheme: Muse/Mist/Pisces
```
```
toc:
  enable: true
  number: true
```
```
highlight_theme: night blue
```