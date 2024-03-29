---
title: Obsidian作为第二大脑工具的基本使用和配置 
date: 2021-12-11
description: 本文用来说明Obsidian作为第二大脑工具的基本使用和配置 
tags : [
    "Obsidian",
]
categories : [
    "生产力",
]
toc : true
typora-copy-images-to: upload
---


作为第二大脑系统的笔记软件，本片我们来讲一下Obsidian的基本使用和配置。关于“第二大脑系统笔记方法选择和软件选型”我会单独写一篇Bolg进行说明。作为第二大脑系统的笔记，需要满足日常笔记的快速编写，并且能够支持PC、手机、Pad多平台同步，同时Obsidian支持通过插件进行扩展，因此我将分基本功能使用和配置、核心插件配置和使用、第三方插件配置和使用、工作区布局几个维度进行说明。

<!--more--> 

![Obsidian图标](https://gitee.com/qz757/picgo/raw/master/img/Obsidian图标.jpg)

## 一、为什么选择Obsidian

关于我的第二大脑系统构筑的思路、方案以及选型，我会单独写一篇《第二大脑系统笔记方法选择和软件选型》进行说明，可以参考这边文章。

## 二、Obsidian基本功能使用和配置

### Ⅰ、下载安装

- 下载：[Obsidian官网](https://obsidian.md/)；
- 选择对应的版本下载安装；

### Ⅱ、打开工程

- 打开文件夹当做工程，如果是空文件夹，则会将文件夹名当做工程名；
- 也可以新建工程；
- 配置工程语言为中文；

![Obisidian创建或打开工程](https://gitee.com/qz757/picgo/raw/master/img/Obisidian创建或打开工程.png)

### Ⅲ、创建新文件夹和文件

- 与其他编辑器类似，可以直接创建新文件夹和文件，创建的文件为md格式；

### Ⅳ、对Obsidian进行基本配置

####   1、文件与链接配置

> 修改文件链接的描述使用MarkDown标准语法，确保和Typora一致，同时配置附件拷贝目录为当前文件坐在resource目录，可以同Typora一致；

![文件与链接配置](https://gitee.com/qz757/picgo/raw/master/img/文件与链接配置.png)

#### 2、外观配置

1. 在外观配置页签可以选择黑暗或者明亮模式；
2. 主题配置：
> 1、将下载好的Minimal.css文件放到.obsidian/themes，没有该文件夹则手动创建，修改后在外观标签页更改主题为minimal；
> 2、通过Minimal-theme-setting插件对主题进行调整，安装后默认配置即可，可参考 ：[第三方插件配置和使用](#四、第三方插件配置和使用)；

#### 3、快捷键配置

> 在快捷键页签修改或增加快捷键：
> ALT+S 文件列表：在文件列表中显示当前文件
> ALT+Z 打开局部关系图
> CTL+SHIFT+X：切换工作区

#### 4、关于页签（语言、自动更新）

> 在关于页签对软件自动更新、语言等进行修改配置；

#### 5、核心插件配置

> 核心插件的配置和使用可参考：[核心插件配置和使用](#三、核心插件配置和使用)

#### 6、第三方插件配置

> 第三方插件的配置和使用可参考：[第三方插件配置和使用](#四、第三方插件配置和使用)

#### 7、快速切换配置

> 快速切换可根据需要进行配置

![快速切换配置](https://gitee.com/qz757/picgo/raw/master/img/快速切换配置.png)

#### 8、命令面板配置

>  在命令面板添加置顶命令：

![置顶命令](https://gitee.com/qz757/picgo/raw/master/img/置顶命令.png)

### Ⅴ、常用命令

- CTL + P：打开命令面板；
- CTL + E：切换编辑和预览视图；
- ALT+S ：文件列表：在文件列表中显示当前文件；
- ALT+Z ：打开局部关系图；
- CTL+SHIFT+X：切换工作区；
- CTL+O：快速切换编辑文件；

## 三、核心插件配置和使用

### Ⅰ、核心插件使用列表

![Obsidian核心插件配置1](https://gitee.com/qz757/picgo/raw/master/img/Obsidian核心插件配置1.png)

![Obsidian核心插件配置2](https://gitee.com/qz757/picgo/raw/master/img/Obsidian核心插件配置2.png)

![Obsidian核心插件配置3](https://gitee.com/qz757/picgo/raw/master/img/Obsidian核心插件配置3.png)

### Ⅱ、主要插件使用说明

#### 1、关系图谱

> 关系图谱是选择Obsidian作为知识系统构建团建的主要原因之一，他可以清晰地看出来文章之间的联系，相信选择Obsidian的同学不会对这个功能陌生；关系图谱的颜色可以根据标签、文件夹等维度进行配置，我是以文件夹为粒度进行配置的；

#### 2、模板

> 模板功能是在写文章过程中可以插入模板文件夹中某个模板的内容到当前文件，或者在创建文件时直接以模板为基础创建，可与 Calendar、Periodic Notes、QuickAdd、ZK卡片等插件配合使用，快速创建出不同维度的笔记；

#### 3、ZK卡片

> ZK卡片插件是支持卢曼卡片盒笔记法的插件，可以方便创建出卡片笔记；

#### 4、其他核心插件

> 其他核心插件功能很简单，可以根据需要随时打开或关闭，插件打开后，都会在配置页面的左边列表出来一个对应的配置选项，对每个插件进行配置。

## 四、第三方插件配置和使用

Obsidian的开放性吸引了大量开发人员开发了很多实用的第三方插件，接下来就讲一下第三方插件的安装和配置使用。

### Ⅰ、第三方插件安装

#### 1、插件下载

> 我的插件下载地址：[宏沉一笑 / obsidian-plugin](https://gitee.com/whghcyx/obsidian-plugin)，这是从github定期同步到gitee的obsidian插件源；

#### 2、插件安装

> 将对应插件目录拷贝到obsidian安装目录下的.obsidian/plugins下，同时关闭【设置】->【第三方插件】->【安全模式】，打开对应的插件即可，每个插件的配置路径与核心插件一样，打开后会在配置列表多一个插件的配置选项；

### Ⅱ、使用的插件列表

以下是我当前在用的插件列表：

![第三方插件列表目录截图](https://gitee.com/qz757/picgo/raw/master/img/第三方插件列表目录截图.png)

#### 1、外观美化类

-  cm-editor-syntax-highlight-obsidian
这是一款用于高亮代码块中的代码的插件；
- obsiidan-minimal-settings
支持对主题进行设置，没有深度使用，直接打开了对minimal主题的优化（ps：当前在用的主题是minimal）；

#### 2、编辑增强类

- dataview

  > 1. 基本语法
  >
  > ![dataview语法](https://gitee.com/qz757/picgo/raw/master/img/dataview语法.jpg)
  >
  > 2. 页面元数据
  >
  > ![DataView页面元数据](https://gitee.com/qz757/picgo/raw/master/img/DataView页面元数据.png)
  >
  > 3. yml语法
  >
  > YML必须写在文件最上方，需要写在 6 个横杠符号之间，yml 就可以被识别，例如：
  > ```author: 鲁迅 ```
  > where 和 sort 就可以直接使用你在 yml 中设置的 key 了。
  >
  > 4. 目录索引
  >
  > 先看下基本效果：
  >
  > ![DataView全局索引](https://gitee.com/qz757/picgo/raw/master/img/DataView全局索引.png)
  >
  > table后边的字段是表格的列；
  > where是过滤条件，可以使用页面的元数据，也可以使用yml的key；
  > sort是对列表或表格数据进行排序；
  >
  > 5. 标签索引
  >
  > where的过滤条件可以以标签为索引进行过滤：
  >
  > ![DataView以标签为条件过滤](https://gitee.com/qz757/picgo/raw/master/img/DataView以标签为条件过滤.png)

  - media-extended
  在笔记中可以插入音频、视频等文件并在文件中打开；
  - mx-bili-plugin
  笔记中支持插入bilibili视频链接；
  - obsidian-checklist-plugin
  支持通过标签筛选待办在列表中显示；

#### 3、功能扩展类

- advanced-toolbar
手机工具栏扩展，手机编辑时有两排特殊符号按钮；
- calendar
日历插件，并且能够配合periodic-notes插件创建日记和周记，通过点击日历的日期或者周快速创建；
- periodic-notes
可以通过模板快速创建日、周、月、年记文件，并且可以配合calendar插件提高效率；
- homepage
为Obsidian工具栏增加一个homepage的按钮，可以快速退回到homepage页面(文件)；
- obsidian-mind-map
支持以脑图的方式，显示文章结构；
- quickadd
支持通过模板快速创建笔记；
- quick-explorer
在上方工具栏显示当前文件目录，并且支持快速求换；
- recent-files-obsidian
支持显示最近打开的文件列表；
-  remember-cursor-position
记住打开的文件上次编辑所在位置，打开后仍在记录的位置显示；
- recent files
显示最近打开的文件，配合工作区功能可以提高笔记管理效率；

## 五、工作区布局

通过固定不同模式下的工作区布局，提高不同模式的工作效率；当前我常用的工作区有主工作台，和编辑模式；

### Ⅰ、主工作台

先看看布局：

![主工作台布局](https://gitee.com/qz757/picgo/raw/master/img/主工作台布局.png)

布局说明：
- 1：文件列表；
- 2：最近打开的文件；
- 3：编辑区；
- 4：局部链接；
- 5：笔记的脑图显示；
- 6：预览区；
- 7：笔记大纲；
- 8：日历；

### Ⅱ、编辑工作台

编辑工作台关闭所有小窗口，只有左右一个编辑区，一个预览区；通过CTL+O快速切换文件进行编辑；

### Ⅲ、工作区保存和切换

1. 通过左边工作区按钮进行保存和切换；
2. 参考[快捷键配置](#3、快捷键配置)增加工作区切换快捷键，CTL+SHIFT+X：切换工作区；

## 结束

以上就是我使用Obsidian作为我的第二大脑系统的基本配置和使用，希望可以帮助大家了解Obsidian的使用；后边我会继续完善我的第二大脑系统的构建思路和方法。

## 标签

Index： #生产力-Index

Info： #Obsidian