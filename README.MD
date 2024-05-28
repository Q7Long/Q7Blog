## 博客编辑方法

```js
// 注意每一个文件夹中的文件的 permalink: /pages/9a7ee40fc232253e 都会关联 docs\.vuepress\config.ts 中  nav: 中的导航配置，如果需要更改导航信息，需要移步到 docs\.vuepress\config.ts 中配置
// 一般来说，导航中的下拉操作都会关联到第一个文件中的 permalink 尽量不要修改第一个文件中的 permalink
```

## 如何导入图片到页面

在文章中使用图片只需要使用 `public` 下任意一个文件夹路径开头的地址就可以了。比如在《深入 JavaScript 高级》笔记中，需要用到 `public` 下 `images`文件夹中 `/images/《深入 JavaScript 高级》笔记` 下的图片，就不需要使用`../xxx`切换路径到图片文件夹的目录，直接使用`/images/《深入 JavaScript 高级》笔记` 即可

## 快速上手

## ⚡️ 未来...
