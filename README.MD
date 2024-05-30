# 博客编辑方法

### 导航栏内容配置

注意每一个文件夹中的文件的 `permalink: /pages/9a7ee40fc232253e` 都会关联 `docs\.vuepress\config.ts` 中 `nav:` 中的导航配置，如果需要更改导航信息，需要移步到 `docs\.vuepress\config.ts` 中配置

一般来说，导航中的下拉操作都会关联到第一个文件中的 `permalink` 尽量不要修改第一个文件中的 `permalink`

### 如何将项目中图片导入到页面

在文章中使用图片只需要使用 `public` 下任意一个文件夹路径开头的地址就可以了。比如在《深入 JavaScript 高级》笔记中，需要用到 `public` 下 `images`文件夹中 `/images/《深入 JavaScript 高级》笔记` 下的图片，就不需要使用`../xxx`切换路径到图片文件夹的目录，直接使用`/images/《深入 JavaScript 高级》笔记` 即可

### 文章目录增添

j 举例说课程学习笔记中需要新增类目,比如为深入 `JavaScript` 高级，首先就需要在 `doc` 文件夹下新建文件夹，名字随意最好带有提醒以笔记结尾用于区分。
新增完文件夹之后，需要在 `docs\01.前端\02.课程学习笔记` 文件夹下面新建 `md` 文档，可以按照其中之一进行新建。`md` 文档配置的格式如下：

- `path`：路径属性为刚才新建的文件名
- `imgUrl`：则为 `images` 文件夹下的图片 `/images/xxx.png`，当然可以是其他文件夹，比如在 `images` 同级下新建 `img` 文件夹，那么路径则为 `/img/xxx.png`。
- `title` ：属性则为左侧导航栏和打开之后标题
- `permalink`：属性则是链接到了 `docs/.vuepress/config.ts` 文件下的 `themeConfig` 属性中的路径信息。比如 `JavaScript 基础笔记` 中的 `permalink` 为 `/note/javascript/` 那么在 `docs/.vuepress/config.ts` 文件下责备配置为了 `{ text: "JavaScript 教程", link: "/note/javascript/" }` 用于指向 `103_JavaScript 基础笔记` 文件夹

### 文章目录增添

`md` 文档中需要进行配置信息，用于指定该文章的一些内容，比如作者信息、创建日期、显示优先级、是否为原创文章、文章分类、文章标签等信息，如果导入的 `md` 文档中没有该配置，进行保存之后就会自动生成该配置信息

```javascript

title: "标题"
date: 2023-10-29 15:30:51   // 时间
permalink: /pages/dcebaf/  // dcebaf后缀名可以随意起，但避免与其他文章重复
titleTag: 原创        // 可以在文章标题后面添加原创文字
sticky: 1         // 可以让文章置顶
categories:
  - 深入 JavaScript 高级
tags:
  - coderwhy
  - 深入 JavaScript 高级
author:
  name: ZhangQiLong
  link: https://github.com/ZhangQiLong2023

```

## 快速上手

## ⚡️ 未来...
