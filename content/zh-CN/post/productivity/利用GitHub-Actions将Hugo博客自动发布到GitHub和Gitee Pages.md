---
title: 利用GitHub-Actions将Hugo博客自动发布到GitHub和Gitee Pages
date: 2021-12-18
description: 介绍如何利用GitHub Actions快速发布Blog到GitHub和Gitee Pages
tags : [
    "生产力",
    "Hugo",
    "GitHub Actions"
]
categories : [
    "生产力",
]
toc : true
typora-copy-images-to: upload
---
![工作协同图片](https://gitee.com/qz757/picgo/raw/master/img/工作协同图片.jpg)

## 简介

现在个人博客通常有比较多的选择，如果不想要自己购买服务器实现静态博客的发布，可以在简书、CSDN等平台建立自己的账号并发布，但是原始数据无法在本地管理；如果想要数据完全自己管理，可以使用GitHub或者Gitee Pages功能发布自己的博客，利用Hugo或Hexo等成熟的静态博客将md文件转换成静态网站文件进行快速发布。本文就用来记录如何快速将博客发布到简书、GitHub以及Gitee Pages三个平台。

## 一、流程设计

我希望的流程当然是主要精力用在写文章，发布的流程最好完全不需要手动处理，但实际情况下手动发布博客的流程大体是：

> 编写->拷贝到简书->本地Hugo生成文件->提交到Github->提交到Gitee->Gitee页面刷新；
> 流程相对比较多，希望能够编写完md后只需要把源文件提交到GitHub即可实现GitHub和Gitee Pages的自动化部署。

之前有使用过Travis CI+GitHub+Hexo自动发布到GitHub Pages，但是GitHub的访问经常不那么靠谱，因此想着也能同步到Gitee，并且自动构建Hexo的速度受Nodejs环境部署影响，速度很慢，因此本次计划采用Hugo来构建网站文件，整体流程设计如下：

![博客自动发布流程](https://gitee.com/qz757/picgo/raw/master/img/博客自动发布流程.png)

## 二、环境准备

为了满足博客自动发布流程，首先需要在本地准备好生产环境，以满足快速发布，以此的复杂是为了长期的简单，因此在环境准备阶段我会把所有涉及的工具都记录下来，以方便大家使用以及后边自己回顾，环境准备包含：

- Typora配置
- Git配置
- Hugo配置
- Obsidian配置

### Ⅰ、Typora配置

#### 1、Typora基本配置

Obsidian完成知识积累后，按照ZK->Project->Archive->Blog的流程发布Blog，但是Obsidian的文件相互关联，且附件如何快速复用，需要对Typora和Obsidian都进行简单的配置，确保后续Blog能够快速完成并发布，并且文件仍在Obsidian工程中正常显示；

Typora非常的简洁，而且可以实时预览，在写博客的时候经常会插入一些图片，截图后可以直接粘贴进去，图片也会自动保存在本地，这里需要注意图片的保存路径，在偏好设置里，设置插入图片时复制到指定路径（./resource/），这样复制的图片就会自动保存在当前文件夹下的resource文件夹里，方便后续管理与转移。

![Typora图像设置](https://gitee.com/qz757/picgo/raw/master/img/Typora图像设置.png)

#### 2、使用Typora+picgo-core+gitee实现图床功能

1. 安装Nodejs

   - windows7安装

    1. 从[官网](https://nodejs.org/en/download/)下载对应版本，win7可用最新版本为：v13.14.0；win10/win11可下载最新版本；

    2. 运行安装即可；

   - ubuntu 20.04安装

      ubunut仓库中默认的Nodejs版本是v10.19.0，不是最新的版本，因为我用到的其他包需要依赖高级版本，因此安装v14版本；

     1. 安装NodeSource：`curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -`，这个脚本将会添加 NodeSource 的签名 key 到你的系统，创建一个 apt 源文件，安装必备的软件包，并且刷新 apt 缓存；如果你需要另外的 Node.js 版本，例如`12.x`，将`setup_14.x`修改为`setup_12.x`；

      > NodeSource 软件源提供了以下版本：
      > -   v14.x - 最新稳定版
      > -   v13.x
      > -   v12.x - 最新长期版本
      > -   v10.x - 前一个长期版本

    2. 安装Nodejs和npm：`sudo apt install nodejs`，安装的版本`node -v`可查看是`v14.18.2`；

2. 配置npm

   配置npm源为淘宝源，命令如下：

   ```shell
   npm config set registry https://registry.npm.taobao.org
   npm config get registry
   ```

3. 安装picgo

   - 方法一、在上传服务中点击PicGo-Core(command line)

     `自动下载的目录为：%AppData%\Typora\picgo`

   - 方法二：在上传服务中点击Custom Command，打开终端，输入以下命令：

```shell
sudo npm install --no-optional --verbose picgo -g #跳过可选依赖
```

4. 在Gitee创建图床仓库

    a. 注册一个码云（gitee）账号
    b. 创建一个仓库（加号）
    c. 仓库设定
    ![gitee创建图床仓库](https://gitee.com/qz757/picgo/raw/master/img/gitee创建图床仓库.jpg)

    d. 创建gitee访问token

    -  新建token，点击头像
    ![gitee私人令牌1](https://gitee.com/qz757/picgo/raw/master/img/gitee私人令牌1.jpg)
    ![gitee私人令牌2](https://gitee.com/qz757/picgo/raw/master/img/gitee私人令牌2.jpg)

    e. 配置picgo

    打开终端输入以下命令，如果是自动安装，需要将picgo目录加入path，或者在picgo目录执行命令：
    ```shell
    #安装gitee的插件    
    picgo install gitee-uploader 
    #设置配置文件    
    picgo set uploader  
    1.按上下键找到gitee，回车    
    2.repo：用户名/仓库名 （打开自己的仓库，浏览器里的网址username/reponame）    
    3.token：刚才生成的token    	
    4.path:路径，图片上传到仓库的目录，例如img，即上传到仓库的img目录    
    5.custompath:不用填，回车   
    6.customURL:不用填，回车    
    #使用配置好的文件（配置文件在~/.picgo/config.json）    
    picgo use uploader
    ```

   

7. 测试上传功能

   - 打开你typora，验证图片上传，查看是否成功；
   - 成功设置好图床，将一张图片拖到typora中，试一下能否自动上传
   - 手动上传图片可参考：[picgo gitee仓库](https://gitee.com/dengdg/PicGo-Core)；

### Ⅱ、Git配置

#### 1、Git下载安装
1. 去[官网](https://git-scm.com/)下载git;
2. 运行安装，选择安装路径，其他默认即可；
#### 2、基本配置
- 配置全局用户名和邮箱
```
git config --global user.name "用户名"
git config --global user.email "邮箱"
```
- 生成公钥
```
ssh-keygen -t rsa -C "邮箱地址"
```
在.ssh文件生成id_rsa和id_rsa.pub两个文件；
#### 3、配置公钥到github
> setting->SSH and GPG keys：new SSH Key；
#### 4、配置公钥到gitee；
> 设置->ssh公钥->添加公钥；

### Ⅲ、Hugo安装配置

Hugo 是一个基于Go语言开发的静态博客框架，号称世界上最快的构建网站工具；

#### 1、安装

- Windows安装：在[hugo github](https://github.com/gohugoio/hugo/releases)下载windows版本包，解压到目录，并添加到PATH即可；
- Ubuntu安装：`sudo apt-get install hugo`

#### 2、生成博客

命令为：`hugo new site myblog`
myblog为博客的目录名，可以修改为你自己想取的名字，生成的目录如下：
![](https://gitee.com/qz757/picgo/raw/master/img/hugo生成博客目录.png)

#### 3、主题下载

- 在[hugo主题网站](https://themes.gohugo.io)找到喜欢的主题，我选的是[hexo主题](https://themes.gohugo.io/themes/hugo-theme-next/)，可以在next主题的github地址[hugo-theme-next](https://github.com/elkan1788/hugo-theme-next)下载;
- 可以下载主题压缩包，解压到themes文件夹下，也可以直接使用git clone到themes目录下；

#### 4、主题配置

- 将【exampleSite\config】和【exampleSite\content】两个目录拷贝到站点根目录下；
- content目录下有en和zh_CN两个目录，分别放英文和中文两个页面的md文件，默认的md文件可以删除，将需要发布的文章放入这两个目录即可；
- config目录下_default目录对站点的侧栏等功能进行配置，可以根据自己需要进行配置；

#### 5、主题修改

由于默认主题生成的文章页面有些功能是不需要的，因此需要对themes下文件进行修改：
![](https://gitee.com/qz757/picgo/raw/master/img/themes修改文件列表.png)

- single.html修改，删除文章页面不需要的几个信息；
 ![](https://gitee.com/qz757/picgo/raw/master/img/single.html修改.png)
- rss.html，直接删除文件内容即可；
- comment.html修改为自己的评论功能代码，我使用的是utteranc，可以参考[5、配置评论功能](#5、配置评论功能)]；
![](https://gitee.com/qz757/picgo/raw/master/img/comment.html文件修改.png)
- foot.html，删除页脚不需要的信息
![](https://gitee.com/qz757/picgo/raw/master/img/foot.html文件修改.png)

#### 6、快速复制当前配置
将config、content和themes三个目录拷贝到新创建的站点目录，将content下替换为文章文件即可。

#### 7、配置评论功能

`utterances`是一款基于Github Issue的Github工具，优点主要是无广告、加载快、配置简单，轻量开源。
由于`utterances`是一款Github App，因此[安装utterances](https://link.zhihu.com/?target=https%3A//github.com/apps/utterances)非常简单，只需要授权特定repo权限给`utterances`就可以了,注意一个点：授权的这个repo必须是public的，可以选择多个repo，但是建议选择一个就可以了，也比较安全。

- 给出我授权的repo作为参考，我是选择博客的repo作为`utterances`评论的存放点(在博客评论的内容都会以issue的形式发布在repo下).
![utterances APP配置](https://gitee.com/qz757/picgo/raw/master/img/utterances%20APP配置.png)
- 将插入评论代码加到主题模板中：
```html
<script src="https://utteranc.es/client.js"
repo="qz757/qz757.github.io"
issue-term="title"
theme="preferred-color-scheme"
crossorigin="anonymous"
async>
</script>
```
这是当前最简单的配置方法， 也可以在[utterances官方](https://link.zhihu.com/?target=https%3A//utteranc.es/%3Finstallation_id%3D14775258%26setup_action%3Dinstall)查看其他配置方法，以及详细的配置参数说明。

#### 8、Hugo常用命令

1. `hugo server`本地启动服务进行预览，`localhost:1313`访问；
2. `hugo`生态静态网页文件到public目录；
3. `hugo -b url`，制定baseurl生成网页文件，所有文章的链接前缀都是以此生成的；
4. `hugo new post/first.md`，用模板生成md文件

### Ⅳ、Obsidian配置

Obsidian的安装和使用可参考我的另一篇文章：[Obsidian作为第二大脑工具的基本使用和配置 ](obsidian作为第二大脑工具的基本使用和配置 )

## 三、实现方案

所有环境准备好后，终于要开始启动我们的自动化流程的实现了；

### Ⅰ、GitHub和Gitee Pages配置

1. Gitee Pages配置：
	- 创建与用户名同名仓库；
	- 仓库主页->服务->Gitee Pages，选择对应的分支并开启https；
2. GitHub Pages配置：
	-  创建名称为【用户名.gthub.io】的仓库；
	-  仓库主页->setting->Pages，选择对应的分支；

### Ⅱ、GitHub同步到Gitee鉴权私钥配置
1. 参考[git配置](#Ⅱ、Git配置)生成公私钥，并将公钥配置到Gitee；
2. 将私钥配置到GitHub仓库，Pages仓库主页->Settings→Secret→New repository secre 用于GiuHub Action提交代码到Gitee的鉴权，命名为GITEE_RSA_PRIVATE_KEY，将私钥填入；
 ![](https://gitee.com/qz757/picgo/raw/master/img/GitHub添加Gitee私钥.jpg)
 ### Ⅲ、GitHub仓库代码更新Token配置
 1. 生成GitHub账号的 personal access token
![](https://gitee.com/qz757/picgo/raw/master/img/GitHub生成ACCESS%20TOKEN.jpg)
![](resource/GitHub%20ACCESS%20TOKEN配置.jpg)
2. 将仓库权限选上就行了，然后将生成的token，配到私钥配置的地方 仓库→Settings→Secret→New repository secre，命名为ACCESS_TOKEN；
### Ⅳ、配置Gitee密码到Github用于自动部署Gitee Pages工程

同之前步骤相同，将Gitee密码配置到GitHub Secrets，命名为：GITEE_PASSWORD，所有配置结果如下：
![](https://gitee.com/qz757/picgo/raw/master/img/GitHub%20Secrets所有配置.jpg)

### Ⅴ、编写Actions脚本
**synctogitee.yml**
```yaml
name: deploy blog to gitee
 
on:
  push:
    branches:
      - master    # master 分支 push 的时候触发
      
jobs:
  deploy: #执行部署Hugo生成静态代码，默认放在gh-pages分支
    runs-on: ubuntu-18.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.90.1'
          extended: true #不需要extended版本就可以注释

      - name: Build github # 我的Hugo配置baseurl为GitHub Pages路径，使用hugo命令默认为GitHub Pages生成静态网站
        run: hugo

      - name: Deploypage # 部署到GitHub Pages分支
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.ACCESS_TOKEN }}
          external_repository: qz757/qz757.github.io
          publish_branch: gh-pages  # default: gh-pages
          publish_dir: ./public

      - name: Build gitee # 指定baseurl为giee Pages的url生成Gitee网站静态文件；
        run: hugo -b https://qz757.gitee.io
          
      - name: Deploygitee # 部署到Gitee对应的分支，该步骤是部署到GitHub仓库的对应分支
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.ACCESS_TOKEN }}
          external_repository: qz757/qz757.github.io
          publish_branch: gh-pages-gitee  # default: gh-pages
          publish_dir: ./public
                
  
  sync: #同步到gitee仓库
    needs: deploy
    runs-on: ubuntu-latest
    steps:
    - name: Sync to Gitee
      uses: wearerequired/git-mirror-action@master
      env:
        SSH_PRIVATE_KEY: ${{ secrets.GITEE_RSA_PRIVATE_KEY }}
      with:
        # 来源仓库
        source-repo: "git@github.com:qz757/qz757.github.io.git"
        # 目标仓库
        destination-repo: "git@gitee.com:qz757/qz757.git"
        
  reload-pages:
    needs: sync
    runs-on: ubuntu-latest
    steps:
      - name: Build Gitee Pages
        uses: yanglbme/gitee-pages-action@main
        with:
          # 注意替换为你的 Gitee 用户名
          gitee-username: qz757
          # 注意在 Settings->Secrets 配置 GITEE_PASSWORD
          gitee-password: ${{ secrets.GITEE_PASSWORD }}
          # 注意替换为你的 Gitee 仓库，仓库名严格区分大小写，请准确填写，否则会出错
          gitee-repo: qz757/qz757
          # 要部署的分支，默认是 master，若是其他分支，则需要指定（指定的分支必须存在）
          branch: gh-pages-gitee
```
### Ⅵ、绑定Gitee账号到微信公众号
需要绑定Gitee账号到微信公众号，否则最后一步reload pages自动部署Gitee Pages会登录失败，原因是需要短信验证；绑定公众号后则不需要短信验证；其他问题解决可以参考[gitee-pages-action](https://github.com/yanglbme/gitee-pages-action/blob/main/README.md)

### Ⅶ、自动发布
1. 将Hugo生成的工程文件整体提交到GitHub Pages工程master分支；
2. 查看Actions执行结果完成后，GitHub和Gitee Pages已自动部署完成；
![](https://gitee.com/qz757/picgo/raw/master/img/Actions执行结果.png)

## 四、后续使用

在Typora完成文章编写后，一键上传所有图片到Gitee图床，拷贝发布到简书，然后提交到GitHub repo master分支，即可自动部署到GitHub和Gitee Pages；

## 结束

以上就是我利用GitHub-Actions将Hugo博客自动发布到GitHub和Gitee Pages，希望可以帮助大家快速构建自己的个人网站；后边我会继续完善我的第二大脑系统的构建思路和方法。