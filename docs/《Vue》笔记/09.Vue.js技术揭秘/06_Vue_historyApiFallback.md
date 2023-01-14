---
title: 06_Vue_historyApiFallback
date: 2022-01-14 16:10:44
permalink: /pages/5fdf8b/
categories:
  - 《Vue》笔记
  - Vue.js技术揭秘
tags:
  -
author:
  name: Q7Long
  link: https://github.com/Q7Long
---

# 🍔vue 中的 historyApiFallback 解读

当我们在浏览器中输入一个网址(`比如说：http://www.zhangqilong.cn`)，此时会经过 `dns` 解析，拿到 ip 地址然后根据 ip 地址向该服务器发起请求，服务器接受到请求之后，然后返回相应的结果(`html、css、js`)。

![image-20230114153302737](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114153302737.png)

如果我们在前端设置了[重定向](https://so.csdn.net/so/search?q=重定向&spm=1001.2101.3001.7020)，

```js
 {
    path: "/",
    redirect: "/home",
    component: () => import("@/views/home/Home"),
    hidden: true,
  }
```

此时页面会进行跳转到 `localhost:8080/home`，在前端会进行匹配对应的组件然后将其渲染到页面上。此时如果我们刷新页面的话，浏览器会发送新的请求 `localhost:8080/home`，如果后端服务器没有 `/home`对应的接口，此时会返回 `404`

![image-20230114122727250](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114122727250.png)

## 🍕 在前端路由模式中,采用 history 的时候会出现这个问题,原因是为什么呢?

### 🍟 没有配置 historyApiFallback

如果是自己搭建的,没有在 `config.js` 里面去配置的话,就会出现这个问题

我们在本地开启服务器，此时当我们进行刷新时，浏览器会拿着该地址 `localhost:8000/home`向本地服务器发起请求，但是本地不存在 `/home` 的文件夹，所以会返回 `404`。如下图所示。

![image-20230114122727250](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114122727250.png)

究其原因是因为 `vue` 中的[historyApiFallback](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fdtq007%2Farticle%2Fdetails%2F103672974) 如果我们没有配置,那么在使用 `history` 路由模式的时候,相当于我们直接去请求服务器上当前接口,如果服务器上并没有这个接口,那么就会报错( `hash模式并不会有这个问题,因为hash #后不会被添加到url请求中`)

### 🍟 如何解决前端路由刷新 404 问题

#### 🍗 前端路由刷新 404 问题（配置 Nginx）

我们首先来了解一下，当输入 `localhost:8080/home`的时候都发生了什么

![image-20230114153242179](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114153242179.png)

1. 比如浏览器显示的路径是 `localhost:8080/home/message`

2. 用户通过 `localhost:8080/home/message` 进行了一次刷新操作，浏览器会携带 `localhost:8080/home/message` 路径解析成的 `ip地址/路径` 请求服务器资源
3. 上一次请求资源是从 `/根路径` 请求，这次是通过 `/home/message` 的路径请求资源，如果 Nginx 没有对 `/home/message` 路径配置资源的话，那么就会返回 404

![image-20230114144657726](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114144657726.png)

4. 通过 Nginx 配置其实就是配置 try_files

##### 🍠Nginx 配置

下面来引入 `connect-history-api-fallback` 这个中间件，来无痛使用优雅的 History 路由模式。

`webpack ` 默认配置了 `historyApiFallback`为 `true`、功能是通过 `connect-history-api-fallback` 库实现的

###### 🍬 引入 connect-history-api-fallback

它的介绍 `Middleware to proxy requests through a specified index page, useful for **Single Page Applications** that utilise the HTML5 History API.`
中文意思就是一个能够代理请求返回一个指定的页面的中间件，对于单页应用中使用 HTML5 History API 非常有用。

可以查看 [connect-history-api-fallback](https://gitcode.net/mirrors/bripkens/connect-history-api-fallback?utm_source=csdn_github_accelerator#connect-history-api-fallback)文档，Nginx 中配置如下

```js
location / {
    root /...
    # vue工程的路由是history模式
    // 配置 try_files 比如 /home
    // $uri  获取到当前的路径去查找服务器里面对应的相关资源
    // $uri/ 意味着在 /home 文件夹没有找到对应的资源，去 home 文件夹下面找对应的资源
    // /index.html 如果前两种都找不到的话，就返回根路径下面的 index.html
    try_files $uri $uri/ /index.html;
    index index.html index.html
}
```

所以一般配置了 Nginx 之后，那么请求 `localhost:8080/home/message` 等没有对应文件夹路径的时候就会返回根路径下的 index.html，拿到的资源和请求 `localhost:8080/home` 路径获取的资源是一样的

#### 🍗 前端路由刷新 404 问题（vue-cli 通过 webpack 默认配置 historyApiFallback:true）

1. 如果我们在开发的时候是利用 `vue-cli` 直接创建的,那么这个问题是不会出现的,因为在 `vue-cli` 直接帮我们在 `webpack` 中已经配置好了

   位置：`node_modules/@vue/cli-service/lib/commands/serve.js`(172 行)

![vue-cli默认配置historyApiFallback:true](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114114621123.png)

![image-20230114124257852](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114124257852.png)

2. 或者我们也可以自己创建 vue.config.js 进行配置

​ 我们只需要在 `vue.config.js` 中配置代理转发的地方配置即可,可以最简单的配置为 true 当在 devServer 中配置这一属性的时候，当后端路由没有命中，就会自动 render `index.html`，这时候，vue-router 就会生效，home 页面就能出来

```js
module.exports = {
  configureWebpack: {
    devServer: {
      historyApiFallback: true,
    },
  },
};
```

##### 🍨 前端路由实现效果

![image-20230114153252964](www.zhangqilong.cn/img/qlBlog_images/Vue基础/31_Vue_historyApiFallback/image-20230114153252964.png)

1. 当我们访问 localhost:8080/home 的时候，页面会显示 home 组件下面的内容，访问 /category 显示 category 组件下面的内容。但是我们并没有对其进行配置，那为什么可以返回对应的内容呢？
2. 原因就是当我们请求下来静态资源 html/css/js(app.js/···) 之后进行解析，决定页面中要显示什么内容，并且**js 会帮助做前端路由**

```js
// js代码 -> 前端路由
比如：redirect
当我们访问 localhost:8080 的时候会帮助我们重定向到 localhost:8080/home
```

3. 需要注意的是，**redirect 重定向的过程是在前端完成的**，在前端通过 history 模式帮助将路径更改成了 localhost:8080/home
4. 更改成了 localhost:8080/home 路径之后，会去找到 home 对应的组件 Component 被编译成的 js 文件，这个 js 文件要么在 app.js 文件中，分包的话就是存在于其他的 js 文件中
5. 浏览器就会将 home 组件对应的 js 文件下载下来，然后就会在页面中渲染出组件的内容
