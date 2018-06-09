# gitbook——知识积累发布工具

在积累平时所学形成文档时，会遇到很多痛点：

* 好用的markdown编辑器：书写更容易
* 版本控制，和云端存储：能够多人多终端书写
* 尽量简单的发布流程：最好点一下就能发布到网上

本文接下来就介绍一下我是如何解决以上痛点的，主要操作流程是什么。

## gitbook

[gitbook](https://www.gitbook.com/)是一个适用于小团队文档分享、个人写书的这样一个网站。他配合github可以很好的解决版本控制和简化发布的痛点。接下来大概介绍一下如何快速使用gitbook，以及如何关联github

### 初始化

首先是比较无脑的过程，注册&gt;&gt;新建组织&gt;&gt;新建空间

如果是写书的话，一个空间就对应一本书，如果是知识积累的话，可以按主题分类等等

### 关联github

每个空间都可以被关联到一个github的一个分支上。所以你有两种策略，一种是为每本书创建一个github仓库，另一种策略是每本书是一个分支。我采用了第二种策略，每本书是一个分支。

按照下图关联github仓库和空间（github先要建立一个空的repo）

![关联github](/assets/gitbook-1.png)

接下来就是对github中文档的书写了，每次repo发生变化都会自动触发gitbook的webhook，你的博客也会自动更新。

### 文档结构约定

使用gitbook的过程还是很简单的，主要的文档约定也很简洁。空间根目录下一定要有以下两个文件：

* README.md  这个是整本书的封面
* SUMMARY.md 这个是书的目录

## 编辑器的选择

### 1、gitbook editor

[gitbook editor](https://legacy.gitbook.com/editor)是和gitbook一同发布的一个所见即所得的markdown编辑器，可以在gitbook官网下载。主要优点是，他可以一键保存一键提交，然后就是所见即所得。大概介绍一下他如何关联一个github仓库，进行修改。

1. 创建一本书，随意起名字
2. 在book &gt; Repository Settings中，添加git仓库地址，然后点击book &gt; sync同步远端结果
3. 如果你的书在仓库的非master分支上，editor中可以创建这个分支，你或许需要找到finder中这个书的位置，然后进去手动关联一下远程分支和你创建的分支。

### 2、vscode

vscode被称作宇宙第一编辑器嘛，所以配合它上面的一些插件，也可以很好的编写markdown文档。

主要的markdown插件及其作用见下表：

| 插件名称 | 作用 |
| :--- | :--- |
| markdown-all-in-one | 文档排版 |
| markdownlint | markdown语法检测器 |
| markdown-preview-enhanced | 更好看的预览、插图助手 |



