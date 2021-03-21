---
title: vue仿网易云步骤
date:

type:
description:
top_img:
mathjax:
katex:
---

## 前言

今天偶然间在 Github 上发现了一个 Star 1.4k 的 [基于 Vue2、Vue-CLI3 的高仿网易云 mac 客户端播放器](https://github.com/sl1673495/vue-netease-music) 的项目

但是我看他 [README.md](https://github.com/sl1673495/vue-netease-music/blob/master/README.md) 也没有详细的介绍如何打包部署，这就导致劝退了好多小白，又有的人看了一眼担心要服务器和域名，就默默的离开了，而本文教程就是无需服务器和域名快速搭建部署 Vue 仿网易云，也就一首歌的时间就搭建好了，`仅个人玩玩`

[ 演示地址](https://music.imzjw.cn/)

## 前期准备

1. Github 账号，没有的自行 [Google](https://www.google.com/)，Github 的账号 📧 主邮箱推荐绑定 [Gmail](https://mail.google.com/)
2. node、Git 环境
3. Vercel 账号

## 正式开始

首先先去 clone 项目，`git clone https://github.com/sl1673495/vue-netease-music`

如果 clone 较慢的话用这个：`git clone https://github.com.cnpmjs.org/sl1673495/vue-netease-music.git`

clone 到本地之后先执行 `npm i` 安装依赖，你就会发现项目里多了个 `node_modules`，这里面其实就是装了这个项目所依赖的所有依赖包

npm 慢的话可以设置代理

```
SH
npm config set https-proxy "http://127.0.0.1:7890" # 端口是根据你的代理是啥端口就是啥端口
```

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228141202222.webp#align=left&display=inline&height=164&margin=%5Bobject%20Object%5D&originHeight=164&originWidth=885&status=done&style=none&width=885)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228141202222.webp)

取消代理

```
SH
npm config delete https-proxy
```

很不推荐 `cnpm`

当安装完依赖之后，我们输入 `npm run dev`，可以在本地运行调式，默认端口是 8888，当然你也可以在 `vue.config.js` 里面更改

本地调式完无误之后我们直接输入 `npm run build` 进行打包，就会生成个打包后的目录，`music`

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228142107290.webp#align=left&display=inline&height=450&margin=%5Bobject%20Object%5D&originHeight=450&originWidth=1071&status=done&style=none&width=1071)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228142107290.webp)

然后我们把打包之后的内容 push 到 Github 上

注：如果你使用 Gitee 方式的话，这一步就不需要了，直接 push 到 Gitee 上，然后开启 Gitee Pages 即可

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228145035546.webp#align=left&display=inline&height=582&margin=%5Bobject%20Object%5D&originHeight=582&originWidth=1203&status=done&style=none&width=1203)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228145035546.webp)

### 部署

当你打包成功之后并 push 到 Github 上，就离成功不远了

部署方式有两种，Vercel 和 Gitee (码云)

我比较推荐到是 Vercel，但是这两种方法我都会讲，其实还有一种 [Netlify](https://www.netlify.com/)，但是 Vercel 就够了

- Vercel 方式
- Gitee 方式

[点击](https://vercel.com/) 打开 Vercel 官网

右上角点击 [Sign Up](https://vercel.com/signup)，使用 Github 注册

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144101989.webp#align=left&display=inline&height=635&margin=%5Bobject%20Object%5D&originHeight=635&originWidth=1487&status=done&style=none&width=1487)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144101989.webp)

输入 Github 账号密码之后，我们点击 Authorize Vercel（授权给 Vercel）

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144223350.webp#align=left&display=inline&height=834&margin=%5Bobject%20Object%5D&originHeight=834&originWidth=1419&status=done&style=none&width=1419)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144223350.webp)

接着输入手机号码进行验证

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144428702.webp#align=left&display=inline&height=470&margin=%5Bobject%20Object%5D&originHeight=470&originWidth=1071&status=done&style=none&width=1071)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144428702.webp)

验证成功之后我们点击 Continue

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144838966.webp#align=left&display=inline&height=542&margin=%5Bobject%20Object%5D&originHeight=542&originWidth=1614&status=done&style=none&width=1614)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144838966.webp)

输入你 Github 仓库地址（也就是你刚刚打包 push 到 Github 的仓库地址）

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144858933.webp#align=left&display=inline&height=446&margin=%5Bobject%20Object%5D&originHeight=446&originWidth=1117&status=done&style=none&width=1117)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228144858933.webp)

然后就继续 Continue，最后 Deploy

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228145822068.webp#align=left&display=inline&height=743&margin=%5Bobject%20Object%5D&originHeight=743&originWidth=1784&status=done&style=none&width=1784)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228145822068.webp)

部署成功

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228145943215.webp#align=left&display=inline&height=869&margin=%5Bobject%20Object%5D&originHeight=869&originWidth=1610&status=done&style=none&width=1610)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228145943215.webp)

部署成功后 Vercel 会默认自带个二级域名，当然你也可以自定义

我们点击 Settings –> Domains 使用 Vercel 提供的二级域名

格式为：xxx.now.sh、xxx.vercel.app，xxx 表示你自定义的，然后直接 `Add`，如果没有人使用的话，会自动验证成功。

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228150428825.webp#align=left&display=inline&height=713&margin=%5Bobject%20Object%5D&originHeight=713&originWidth=1340&status=done&style=none&width=1340)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228150428825.webp)

如果你想绑定自己域名的话，那你就直接输入自己的域名，点击右侧 Add 即可，下方会提示 `Invalid Config`

然后在域名购买方控制台进行域名解析。解析完成后即可通过自己的域名访问。

大功告成！！😁

[![](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228150617547.webp#align=left&display=inline&height=905&margin=%5Bobject%20Object%5D&originHeight=905&originWidth=1884&status=done&style=none&width=1884)](https://cdn.jsdelivr.net/gh/zjwo/img/houduan/vue/image-20201228150617547.webp)
