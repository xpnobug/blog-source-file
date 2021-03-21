---
title: hexo×语雀 实现云端富文本写作
tags:  - 教程
top_img:
cover:
date: 2020-06-06 13:11:02
updated: 2020-06-06 13:11:02
---

本文章通过语雀编写

> 教程参考自 [https://www.zhwei.cn/hexo-github-actions-yuque/](https://www.zhwei.cn/hexo-github-actions-yuque/)
> 作者：hongwei

## 什么是语雀？

> 「语雀」是一个「专业的云端知识库」，孵化自 [蚂蚁金服](https://www.antfin.com/?deer_tracert_token=cc478126-c93a-459b-a448-dd41de67f2d4) ，是 [体验科技](https://www.yuque.com/yubo/explore/tcaywl?deer_tracert_token=cc478126-c93a-459b-a448-dd41de67f2d4) 理念下的一款创新产品，已是 10 万阿里员工进行文档编写、知识沉淀的标配。
> 语雀诞生伊始，只是希望能给工程师提供一个好用的工具用来写技术文档，达成「用 Markdown 写文档」这个小目标。但在产品研发的过程中，我们发现其实身边的每个人、每个团队、每个组织都有很多知识，但一直以来缺少一个好用的工具让这些知识不只是留在每个人的大脑或电脑里，还可以被记录、分享和交流。
> 所以，带着这颗初心，我们觉得语雀不应止步于服务工程师，应该致力于为每个想表达所思所想的人提供一款顺手的工具，让知识能得以记录和传播，让人们可以在「语雀」中平等快乐地创作和交流知识，让再小的个体也可以拥有自己的知识库。

## 使用 hexo× 语雀的初衷？

在写教程之前先来几句废话。
笔者在使用 hexo 撰写博客时很不习惯使用 md 格式书写文档，同时 md 格式文档的排版需要进行 hexo s 才能进行预览。对于写作而言，大量的时间花费在样式调试和终端命令上，无法获取最纯粹的写作体验。
于是笔者就在思考如何能够像 wordpress 一样，优雅得实现博客的撰写，同时又完全摆脱进行终端上的操作，完全实现云上写作。就在这时，[HEO 大佬](https://blog.zhheo.com/) 提醒我说，为什么不试试阿里爸爸的语雀呢。于是，抱着试一试的心态，我和[CC 康纳百川同学](https://blog.ccknbc.cc/) 进行了语雀云写作的尝试。
通过本教程，你可以将你的文章储存在云端，实现云端写作（不限于 MAC 系统、Windows 系统、手机微信小程序），摆脱本地机器的限制。除此之外，优秀简约的富文本编辑器能极大提升你的写作效率，使你能更专注于文本的写作。通过结合 github action（github 自动部署）、serverless 云函数（腾讯云 API，用于部署事件的触发）、语雀（文档的发布）、Hexo（博客系统）自动实现文章发布到博客展现的流程。
[![yuque_diagram.jpg](https://cdn.nlark.com/yuque/0/2020/jpeg/8391485/1608140651407-830f0a5d-c458-4a02-a12a-dace0324cf07.jpeg#align=left&display=inline&height=171&margin=%5Bobject%20Object%5D&name=yuque_diagram.jpg&originHeight=342&originWidth=1284&size=75855&status=done&style=none&width=642#align=left&display=inline&height=342&margin=%5Bobject%20Object%5D&originHeight=342&originWidth=1284&status=done&style=none&width=1284)](https://cdn.nlark.com/yuque/0/2020/jpeg/8391485/1608140651407-830f0a5d-c458-4a02-a12a-dace0324cf07.jpeg#align=left&display=inline&height=171&margin=%5Bobject%20Object%5D&name=yuque_diagram.jpg&originHeight=342&originWidth=1284&size=75855&status=done&style=none&width=642)

## 使用前的准备？

为了更好、更方便的完成 hexo× 语雀的部署。在开始流程搭建的操作前，你需要完成以下步骤。

### 账号的申请与授权

- 语雀账号[申请](https://www.yuque.com/login?platform=wechat&inviteToken=f6e959505e77f114312173f53ec62f7b2c4ff248e08ed045603ad16d2ecf62ec)
- serverless 方案一（不推荐）：腾讯云账号[登录授权](https://console.cloud.tencent.com/scf/)（[https://console.cloud.tencent.com/scf/](https://console.cloud.tencent.com/scf/)），云函数授权后再进行[API 授权](https://console.cloud.tencent.com/apigateway/index)（[https://console.cloud.tencent.com/apigateway/index](https://console.cloud.tencent.com/apigateway/index)）这样才能正常使用云函数功能
- serverless 方案二（较为推荐）：百度云[授权](https://console.bce.baidu.com/cfc/#/cfc/functions)（[https://console.bce.baidu.com/cfc/#/cfc/functions](https://console.bce.baidu.com/cfc/#/cfc/functions)），[开通方法指南](https://cloud.baidu.com/doc/CFC/s/pjwvz3zg8)
- 2021 年 1 月 21 日新增方案三（推荐）：使用 vercel 构建的 api。
- 获取 github 私钥，前往[github 的 token 设置](https://github.com/settings/tokens)（[https://github.com/settings/tokens](https://github.com/settings/tokens)），点击生成密钥按钮，填写密钥名称，勾选 repo 选项。将生成的密钥保存在桌面新建的 txt 文件里备用。

[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608129629797-d1ff130c-137e-4108-b346-b27107848635.png#align=left&display=inline&height=562&margin=%5Bobject%20Object%5D&name=image.png&originHeight=562&originWidth=1264&size=90217&status=done&style=none&width=1264#align=left&display=inline&height=562&margin=%5Bobject%20Object%5D&originHeight=562&originWidth=1264&status=done&style=none&width=1264)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608129629797-d1ff130c-137e-4108-b346-b27107848635.png#align=left&display=inline&height=562&margin=%5Bobject%20Object%5D&name=image.png&originHeight=562&originWidth=1264&size=90217&status=done&style=none&width=1264)
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608129894558-8142d05c-74fa-4a3e-ba73-bdd37533b797.png#align=left&display=inline&height=437&margin=%5Bobject%20Object%5D&name=image.png&originHeight=437&originWidth=1078&size=63317&status=done&style=none&width=1078#align=left&display=inline&height=437&margin=%5Bobject%20Object%5D&originHeight=437&originWidth=1078&status=done&style=none&width=1078)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608129894558-8142d05c-74fa-4a3e-ba73-bdd37533b797.png#align=left&display=inline&height=437&margin=%5Bobject%20Object%5D&name=image.png&originHeight=437&originWidth=1078&size=63317&status=done&style=none&width=1078)
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608129954376-28b59063-79f5-40a4-96a1-0ed4f10ce05b.png#align=left&display=inline&height=229&margin=%5Bobject%20Object%5D&name=image.png&originHeight=229&originWidth=1006&size=32842&status=done&style=none&width=1006#align=left&display=inline&height=229&margin=%5Bobject%20Object%5D&originHeight=229&originWidth=1006&status=done&style=none&width=1006)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608129954376-28b59063-79f5-40a4-96a1-0ed4f10ce05b.png#align=left&display=inline&height=229&margin=%5Bobject%20Object%5D&name=image.png&originHeight=229&originWidth=1006&size=32842&status=done&style=none&width=1006)

### 仓库的准备

如果你已经配置了 github action 你可以忽略这一步。
为了实现 hexo 的自动部署，需要将本地的源码文件交与 github 托管，你可以创建私有仓库（建议）也可以创建共有仓库。
首先在 github 上创建私有仓库。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608130623269-9a051111-3d85-4694-9c45-e90558fbff18.png#align=left&display=inline&height=401&margin=%5Bobject%20Object%5D&name=image.png&originHeight=401&originWidth=894&size=39478&status=done&style=none&width=894#align=left&display=inline&height=401&margin=%5Bobject%20Object%5D&originHeight=401&originWidth=894&status=done&style=none&width=894)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608130623269-9a051111-3d85-4694-9c45-e90558fbff18.png#align=left&display=inline&height=401&margin=%5Bobject%20Object%5D&name=image.png&originHeight=401&originWidth=894&size=39478&status=done&style=none&width=894)

---

## 步骤一：hexo 博客文章的迁移

### 仓库的创建

登陆[语雀](https://www.yuque.com/)，点击知识库 👉 新建知识库。将知识库的可见范围改为“互联网可见”。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608131882653-69d7b88a-caa9-45ff-a7ca-419647791d22.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=102336&status=done&style=none&width=1279#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&originHeight=789&originWidth=1279&status=done&style=none&width=1279)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608131882653-69d7b88a-caa9-45ff-a7ca-419647791d22.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=102336&status=done&style=none&width=1279)

### 文章的导入

点击知识库的管理按钮，选择新建下的导入，将博客中\_post 文章进行批量导入。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608132157680-32f96698-74a3-45b3-81e6-215ba0115fab.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=155092&status=done&style=none&width=1279#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&originHeight=789&originWidth=1279&status=done&style=none&width=1279)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608132157680-32f96698-74a3-45b3-81e6-215ba0115fab.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=155092&status=done&style=none&width=1279)

### 模板的创建

为了方便以后文档的撰写，可以新建模板。注意图片链接需要加上’’防止被渲染成链接。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608132265373-09c816b7-bbf8-4a6f-9ea0-012060269c8b.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=127438&status=done&style=none&width=1279#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&originHeight=789&originWidth=1279&status=done&style=none&width=1279)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608132265373-09c816b7-bbf8-4a6f-9ea0-012060269c8b.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=127438&status=done&style=none&width=1279)
如果你使用了 abbrlink，请手动填写 abbrlink。

MARKDOWN

| 12345678910111213141516 | ---title: 教程：hexo× 语雀 实现云端富文本写作 author: Zhu Fuetags:• 教程• Butterfly 主题• DIYcategories:• 教程 cover: 'zfe.space/images/letter/y.png'top_img: 'zfe.space/images/letter/y.png'abbrlink: 554edate: 2020-12-16 11:42:00--- |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

---

## 步骤二：安装语雀插件进行本地调试

为了确保在云端能够正常生成博客，需要首先在本地进行调试。

### 语雀插件的安装

首先在根目录打开终端安装 yuque-hexo。

POWERSHELL

| 1   | npm i -g yuque-hexo |
| --- | ------------------- |

### 修改 package.json

在第一个对象代码块后增加”yuqueConfig”代码块。

JSON

| 1234567891011121314151617181920212223 | { "name": "hexo-site", "version": "0.0.0", "private": true, "scripts": { "build": "hexo generate", "clean": "hexo clean", "deploy": "hexo deploy", "server": "hexo server"}, "yuqueConfig": { "postPath": "source/\_posts", "cachePath": "yuque.json", "mdNameFormat": "slug", "adapter": "markdown", "concurrency": 5, "baseUrl": "https://www.yuque.com/api/v2", "login": "bingkanuo", "repo": "sffipz", "token": "**********\*\*\***********", "onlyPublished": true, "onlyPublic": true }, |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

其中需要修改”login”、”repo”、”token”字段。

- 点击进入博客的知识库，在浏览器地址栏中将用户名和仓库名复制分别粘贴为”login”、”repo”的字段。

[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608133592273-04a6a15f-6c75-44d0-ab1c-7ca737c42d07.png#align=left&display=inline&height=251&margin=%5Bobject%20Object%5D&name=image.png&originHeight=251&originWidth=598&size=23393&status=done&style=none&width=598#align=left&display=inline&height=251&margin=%5Bobject%20Object%5D&originHeight=251&originWidth=598&status=done&style=none&width=598)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608133592273-04a6a15f-6c75-44d0-ab1c-7ca737c42d07.png#align=left&display=inline&height=251&margin=%5Bobject%20Object%5D&name=image.png&originHeight=251&originWidth=598&size=23393&status=done&style=none&width=598)

- token 是在右上角头像 -> 账户设置 -> Token 添加的，权限的话只给读取就可以。复制粘贴获取的”token”字段。

[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608133711645-569d4bb4-3de1-450b-80b6-5cf1ca7060b0.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=176577&status=done&style=none&width=1279#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&originHeight=789&originWidth=1279&status=done&style=none&width=1279)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608133711645-569d4bb4-3de1-450b-80b6-5cf1ca7060b0.png#align=left&display=inline&height=789&margin=%5Bobject%20Object%5D&name=image.png&originHeight=789&originWidth=1279&size=176577&status=done&style=none&width=1279)
修改  “scripts”增加”sync”: “yuque-hexo sync”和  “clean:yuque”: “yuque-hexo clean”。

JSON

| 1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071727374 | { "name": "hexo-site", "version": "0.0.0", "private": true, "scripts": { "build": "hexo generate", "clean": "hexo clean", "deploy": "hexo deploy", "server": "hexo server", "sync": "yuque-hexo sync", "clean:yuque": "yuque-hexo clean" }, "yuqueConfig": { "postPath": "source/\_posts", "cachePath": "yuque.json", "mdNameFormat": "slug", "adapter": "markdown", "concurrency": 5, "baseUrl": "https://www.yuque.com/api/v2", "login": "bingkanuo", "repo": "sffipz", "token": "**********\*\*\***********", "onlyPublished": true, "onlyPublic": true }, "hexo": { "version": "5.2.0" }, "dependencies": { "yuque-hexo": "^1.6.0", "gulp": "^4.0.2", "gulp-base64": "^0.1.3", "gulp-htmlmin": "^5.0.1", "gulp-tobase64": "^1.1.2", "hexo": "^5.0.0", "hexo-abbrlink": "^2.2.1", "hexo-deployer-git": "^2.1.0", "hexo-generator-archive": "^1.0.0", "hexo-generator-baidu-sitemap": "^0.1.9", "hexo-generator-category": "^1.0.0", "hexo-generator-feed": "^3.0.0", "hexo-generator-index-pin-top": "^0.2.2", "hexo-generator-sitemap": "^2.1.0", "hexo-generator-tag": "^1.0.0", "hexo-helper-live2d": "^3.1.1", "hexo-offline-popup": "^1.0.3", "hexo-renderer-ejs": "^1.0.0", "hexo-renderer-marked": "^3.0.0", "hexo-renderer-pug": "^1.0.0", "hexo-renderer-stylus": "^1.1.0", "hexo-server": "^1.0.0", "hexo-tag-aplayer": "^3.0.4", "hexo-wordcount": "^6.0.1", "live2d-widget-model-unitychan": "^1.0.5", "workbox-build": "^5.1.4" }, "devDependencies": { "@babel/core": "^7.12.3", "@babel/preset-env": "^7.12.1", "del": "^6.0.0", "gulp": "^4.0.2", "gulp-babel": "^8.0.0", "gulp-clean-css": "^4.3.0", "gulp-cli": "^2.3.0", "gulp-html-css-js-base64": "^0.0.4", "gulp-htmlclean": "^2.7.22", "gulp-htmlmin": "^5.0.1", "gulp-imagemin": "^7.1.0", "gulp-imgs-base64": "^1.0.2", "gulp-minify-inline-json": "^1.3.4", "gulp-uglify": "^3.0.2", "gulp-uglify-es": "^2.0.0" }} |
| ------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

### 进行本地调试

添加完成后保存，在执行命令前，请先备份自己的\_post 文件夹，因为语雀的下载操作会覆盖原有的\_post 文件夹。
在终端中输入‘yuque-hexo sync’就会把语雀的文章给下载下来。
然后通过‘hexo g&hexo s’进行调试。
ps：输入‘yuque-hexo clean’就会清除\_post 下的所有文章。
如果存在外挂标签，请确保外挂标签格式的书写规范，否则容易报错。

---

## 步骤三：配置 github action

### 删除主题版本的控制文件

因为在仓库里面再放一个仓库是没法把里面那个仓库 push 到 github 的，只会传一个空文件夹，会导致后期博客成了空白页面，需要把 git clone 的 hexo 主题里的.git 文件夹给删掉。

> 这里只列出了最简单的方法，另外你也可以尝 fork 创建自己主题的方法，这里暂时不予以介绍。

### \*修改 hexo 主题文件中的 meta

以 butterfly 主题为例，
打开主题文件的/themes/butterfly/layout/includes/head.pug。
在 meta(name=”theme-color” content=themeColor)后方添加 meta(name=”referrer” content=”no-referrer”)。
该步骤是确保语雀中的图片可以正常加载。

YAML

| 12  | meta(name="theme-color" content=themeColor)meta(name="referrer" content="no-referrer") |
| --- | -------------------------------------------------------------------------------------- |

### 修改 hexo 的\_config,yml

前往博客的根目录，修改 hexo 的\_config,yml 中关于 develop 的配置。

POWERSHELL

| 123456789 | # Deployment## Docs: [https://hexo.io/docs/deployment.html](https://hexo.io/docs/deployment.html)deploy: type: git repository: github: [https://用户名:保存在 txt 中的密钥@github.com/](https://%E7%94%A8%E6%88%B7%E5%90%8D:%E4%BF%9D%E5%AD%98%E5%9C%A8txt%E4%B8%AD%E7%9A%84%E5%AF%86%E9%92%A5@github.com/)用户名/仓库名.git branch: master #例子：[https://Zfour:**\*\*\***@github.com/Zfour/zfe-2.0.git](https://Zfour:*******@github.com/Zfour/zfe-2.0.git) |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

> 这里只列出一种简单的方案，其他的有时间再介绍。

### 创建 github action 脚本

在博客根目录下新建.github 文件夹（点不要漏掉了），在该文件夹下新建 workflows 文件夹。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608135890427-0dc37d9e-8eed-4581-8570-e806b5e4fa1a.png#align=left&display=inline&height=65&margin=%5Bobject%20Object%5D&name=image.png&originHeight=65&originWidth=349&size=4021&status=done&style=none&width=349#align=left&display=inline&height=65&margin=%5Bobject%20Object%5D&originHeight=65&originWidth=349&status=done&style=none&width=349)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608135890427-0dc37d9e-8eed-4581-8570-e806b5e4fa1a.png#align=left&display=inline&height=65&margin=%5Bobject%20Object%5D&name=image.png&originHeight=65&originWidth=349&size=4021&status=done&style=none&width=349)
在 workflows 文件夹下新建 autodeploy.yml。并填入以下代码。
将**【更新 语雀拉取缓存及文章】**与**【部署】** user.name 和 user.email 后面的“”信息修改为自己的信息，注意对齐。

YAML

| 123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869 | name: 自动部署# 当有改动推送和语雀发布时，启动 Actionon: [push, repository_dispatch]jobs: deploy: runs-on: ubuntu-latest steps: - name: 检查分支 uses: actions/checkout@v2 with: ref: master #2020 年 10 月后 github 新建仓库默认分支改为 main，注意更改 #但私有仓库貌似还是 master 并没有变 - name: 安装 Node uses: actions/setup-node@v1 with: node-version: "12.x" - name: 安装 Hexo run: | export TZ='Asia/Shanghai' npm install hexo-cli -g #npm install gulp-cli -g #如果你有使用 gulp 的话，打开上面这一行 npm install yuque-hexo -g yuque-hexo clean yuque-hexo sync - name: 更新 语雀拉取缓存及文章 #更新 yuque 拉取的文章到 GitHub 仓库 run: | echo `date +"%Y-%m-%d %H:%M:%S"` begin > time.log git config --global user.email "499984532@qq.com" git config --global user.name "Zfour" git add . git commit -m "Refresh yuque json" -a - name: 推送 语雀拉取缓存及文章 #推送修改后的 yuque json uses: ad-m/github-push-action@master with: github_token: ${{ secrets.GITHUB_TOKEN }}      - name: 缓存 Hexo        uses: actions/cache@v1        id: cache        with:          path: node_modules          key: ${{runner.OS}}-${{hashFiles('**/package-lock.json')}} - name: 安装依赖 if: steps.cache.outputs.cache-hit != 'true' run: | npm install --save - name: 生成静态文件 run: | hexo clean hexo g #gulp #如果你有使用 gulp 的话，打开上面这一行 - name: 部署 run: | git config --global user.name "Zfour" git config --global user.email "499984532@qq.com" hexo deploy |
| --------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |

### 上传博客源码

打开终端输入以下命令，上传你的博客源码到私有源码仓库。

POWERSHELL

| 12345 | git initgit add .git commit -m "first commit"git remote add origin [https://github.com/](https://github.com/)你的用户名/你的私有博客源码仓库名.gitgit push -u origin master |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

### 进行云端调试

上传后你会发现 github action 生效。等待几分钟后，如果打勾，就说明部署成功。如果未打勾请检查出错的步骤。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608135730791-4f825b33-e38e-489c-a1f5-beb48d6f12b8.png#align=left&display=inline&height=415&margin=%5Bobject%20Object%5D&name=image.png&originHeight=415&originWidth=1247&size=63143&status=done&style=none&width=1247#align=left&display=inline&height=415&margin=%5Bobject%20Object%5D&originHeight=415&originWidth=1247&status=done&style=none&width=1247)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608135730791-4f825b33-e38e-489c-a1f5-beb48d6f12b8.png#align=left&display=inline&height=415&margin=%5Bobject%20Object%5D&name=image.png&originHeight=415&originWidth=1247&size=63143&status=done&style=none&width=1247)

---

> time.log 文件最近似乎不会自动生成，所有如果 action 不成功，请手动在仓库里新建 time.log

## 步骤四：配置云函数

### vercel 方案（推荐）

为了方便大家调用，我用 python 写了一个 api。
比较蛋疼的是，在仓库里填写 github 的私钥 github 会自动删除私钥。所以我试了好久都没成功。
因此，我直接将 api 设置成了参数传递型的，供大家调用。
api 地址：[https://yuque-vercel-webhook-api.vercel.app/api?](https://yuque-vercel-webhook-api.vercel.app/api)。
当然你也可以[fork 项目](https://github.com/Zfour/yuque_vercel_webhook_api)在 vercel 中自行搭建，将‘[https://yuque-vercel-webhook-api.vercel.app](https://yuque-vercel-webhook-api.vercel.app/api)’更换为你的 app 应用链接。
你需要传递的参数有 token，user，source。

CODE

| 12345678 | [https://yuque-vercel-webhook-api.vercel.app/api?](https://yuque-vercel-webhook-api.vercel.app/api?)token='{填写你的 github 私钥}'&user='{填写你的 github 用户名}'&source='{填写你的 github 仓库地址}'示例：[https://yuque-vercel-webhook-api.vercel.app/api?token='8888888888'&user='Zfour'&source='my-blog-source-file'](https://yuque-vercel-webhook-api.vercel.app/api?token='8888888888'&user='Zfour'&source='my-blog-source-file')将这个 URL 路径作为触发链接，在语雀中进行配置。 |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

修改触发链接里的参数项，访问这个链接，如果出现‘This’s OK!’说明配置成功。
复制 URL 路径作为触发链接，在语雀中进行配置。

### 百度云方案（推荐）

#### 新建一个云函数

登陆后，点击创建函数。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608209744874-c30f39ed-d043-4f9e-8860-01b1c613b533.png#align=left&display=inline&height=695&margin=%5Bobject%20Object%5D&name=image.png&originHeight=695&originWidth=1267&size=63797&status=done&style=none&width=1267#align=left&display=inline&height=695&margin=%5Bobject%20Object%5D&originHeight=695&originWidth=1267&status=done&style=none&width=1267)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608209744874-c30f39ed-d043-4f9e-8860-01b1c613b533.png#align=left&display=inline&height=695&margin=%5Bobject%20Object%5D&name=image.png&originHeight=695&originWidth=1267&size=63797&status=done&style=none&width=1267)
选择空白函数后，超时设置 10s，运行时选择 python2.7。点击下一步。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608209894902-f73d7508-d3d2-47bb-9ccf-37343476e9f6.png#align=left&display=inline&height=565&margin=%5Bobject%20Object%5D&name=image.png&originHeight=565&originWidth=1008&size=39647&status=done&style=none&width=1008#align=left&display=inline&height=565&margin=%5Bobject%20Object%5D&originHeight=565&originWidth=1008&status=done&style=none&width=1008)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608209894902-f73d7508-d3d2-47bb-9ccf-37343476e9f6.png#align=left&display=inline&height=565&margin=%5Bobject%20Object%5D&name=image.png&originHeight=565&originWidth=1008&size=39647&status=done&style=none&width=1008)
选择 HTTP 触发器，URL 路径填’/‘，HTTP 方法填写 POST 和 GET，然后点击提交。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608209973453-ec970c0f-222a-44c8-95dd-f261df04d197.png#align=left&display=inline&height=542&margin=%5Bobject%20Object%5D&name=image.png&originHeight=542&originWidth=1159&size=59000&status=done&style=none&width=1159#align=left&display=inline&height=542&margin=%5Bobject%20Object%5D&originHeight=542&originWidth=1159&status=done&style=none&width=1159)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608209973453-ec970c0f-222a-44c8-95dd-f261df04d197.png#align=left&display=inline&height=542&margin=%5Bobject%20Object%5D&name=image.png&originHeight=542&originWidth=1159&size=59000&status=done&style=none&width=1159)
点击函数，选择函数列表，将以下代码粘贴并保存，将用户名，仓库地址改为自己的信息。将保存的私钥进行替换\*\*\*\*，token 字段需要保留。测试代码，当返回”This’s OK!”  且 github action 开始运行则说明成功。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608210088380-99147f96-6d56-4880-a40d-70aa541838df.png#align=left&display=inline&height=622&margin=%5Bobject%20Object%5D&name=image.png&originHeight=622&originWidth=1170&size=102039&status=done&style=none&width=1170#align=left&display=inline&height=622&margin=%5Bobject%20Object%5D&originHeight=622&originWidth=1170&status=done&style=none&width=1170)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608210088380-99147f96-6d56-4880-a40d-70aa541838df.png#align=left&display=inline&height=622&margin=%5Bobject%20Object%5D&name=image.png&originHeight=622&originWidth=1170&size=102039&status=done&style=none&width=1170)
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136819166-2fc0b067-6503-4502-9b4a-3b7dc6df28cb.png#align=left&display=inline&height=443&margin=%5Bobject%20Object%5D&name=image.png&originHeight=443&originWidth=891&size=73191&status=done&style=none&width=891#align=left&display=inline&height=443&margin=%5Bobject%20Object%5D&originHeight=443&originWidth=891&status=done&style=none&width=891)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136819166-2fc0b067-6503-4502-9b4a-3b7dc6df28cb.png#align=left&display=inline&height=443&margin=%5Bobject%20Object%5D&name=image.png&originHeight=443&originWidth=891&size=73191&status=done&style=none&width=891)

PYTHON

| 123456789101112131415 | # -_- coding: utf8 -_-import requestsdef handler(event, context): r = requests.post("https://api.github.com/repos/Zfour/my-blog-source-file/dispatches", json = {"event_type": "run-it"}, headers = {"User-Agent":'curl/7.52.1', 'Content-Type': 'application/json', 'Accept': 'application/vnd.github.everest-preview+json', 'Authorization': 'token ********\*\*\*********'}) if r.status_code == 204: return "This's OK!" else: return r.status_code |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

复制 URL 路径作为触发链接，在语雀中进行配置。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608210289194-2911f512-33e4-4e58-951e-71bfc98c10f2.png#align=left&display=inline&height=607&margin=%5Bobject%20Object%5D&name=image.png&originHeight=607&originWidth=1176&size=60789&status=done&style=none&width=1176#align=left&display=inline&height=607&margin=%5Bobject%20Object%5D&originHeight=607&originWidth=1176&status=done&style=none&width=1176)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608210289194-2911f512-33e4-4e58-951e-71bfc98c10f2.png#align=left&display=inline&height=607&margin=%5Bobject%20Object%5D&name=image.png&originHeight=607&originWidth=1176&size=60789&status=done&style=none&width=1176)

### 腾讯云方案（不推荐，有使用期限）

#### 新建一个云函数

> 腾讯云方案目前有 1 年的使用期限，目前在尝试阿里云方案和 vercel 方案。

登陆后，点击新建按钮。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136492669-e83dc779-c0bc-4473-9198-a908be446a95.png#align=left&display=inline&height=312&margin=%5Bobject%20Object%5D&name=image.png&originHeight=312&originWidth=756&size=28760&status=done&style=none&width=756#align=left&display=inline&height=312&margin=%5Bobject%20Object%5D&originHeight=312&originWidth=756&status=done&style=none&width=756)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136492669-e83dc779-c0bc-4473-9198-a908be446a95.png#align=left&display=inline&height=312&margin=%5Bobject%20Object%5D&name=image.png&originHeight=312&originWidth=756&size=28760&status=done&style=none&width=756)
填写基本信息。选择运行环境为 python2.7,以空白方式创建。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136539676-2c3dbc41-f63b-433e-a192-c9fecc95f120.png#align=left&display=inline&height=422&margin=%5Bobject%20Object%5D&name=image.png&originHeight=422&originWidth=661&size=29288&status=done&style=none&width=661#align=left&display=inline&height=422&margin=%5Bobject%20Object%5D&originHeight=422&originWidth=661&status=done&style=none&width=661)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136539676-2c3dbc41-f63b-433e-a192-c9fecc95f120.png#align=left&display=inline&height=422&margin=%5Bobject%20Object%5D&name=image.png&originHeight=422&originWidth=661&size=29288&status=done&style=none&width=661)
填写以下脚本。将用户名，仓库地址改为自己的信息。将保存的私钥进行替换\*\*\*\*，token 字段需要保留。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136819166-2fc0b067-6503-4502-9b4a-3b7dc6df28cb.png#align=left&display=inline&height=443&margin=%5Bobject%20Object%5D&name=image.png&originHeight=443&originWidth=891&size=73191&status=done&style=none&width=891#align=left&display=inline&height=443&margin=%5Bobject%20Object%5D&originHeight=443&originWidth=891&status=done&style=none&width=891)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608136819166-2fc0b067-6503-4502-9b4a-3b7dc6df28cb.png#align=left&display=inline&height=443&margin=%5Bobject%20Object%5D&name=image.png&originHeight=443&originWidth=891&size=73191&status=done&style=none&width=891)

PYTHON

| 123456789101112131415 | # -_- coding: utf8 -_-import requestsdef main_handler(event, context): r = requests.post("https://api.github.com/repos/Zfour/my-blog-source-file/dispatches", json = {"event_type": "run-it"}, headers = {"User-Agent":'curl/7.52.1', 'Content-Type': 'application/json', 'Accept': 'application/vnd.github.everest-preview+json', 'Authorization': 'token ********\*\*\*********'}) if r.status_code == 204: return "This's OK!" else: return r.status_code |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |

点击保存并测试，如果返回”This’s OK!”  且 github action 开始运行则说明成功。

#### 新建一个触发条件

点击触发管理，新建触发器。点击提交后复制访问路径。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137244171-aa616289-9f33-4fba-a13f-47bfa2379f61.png#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&name=image.png&originHeight=400&originWidth=730&size=32431&status=done&style=none&width=730#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&originHeight=400&originWidth=730&status=done&style=none&width=730)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137244171-aa616289-9f33-4fba-a13f-47bfa2379f61.png#align=left&display=inline&height=400&margin=%5Bobject%20Object%5D&name=image.png&originHeight=400&originWidth=730&size=32431&status=done&style=none&width=730)
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137120274-f9338945-a8ac-498c-912a-67d1f2efbfbf.png#align=left&display=inline&height=601&margin=%5Bobject%20Object%5D&name=image.png&originHeight=601&originWidth=927&size=72449&status=done&style=none&width=927#align=left&display=inline&height=601&margin=%5Bobject%20Object%5D&originHeight=601&originWidth=927&status=done&style=none&width=927)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137120274-f9338945-a8ac-498c-912a-67d1f2efbfbf.png#align=left&display=inline&height=601&margin=%5Bobject%20Object%5D&name=image.png&originHeight=601&originWidth=927&size=72449&status=done&style=none&width=927)
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137286087-77efa6ef-bcad-488b-9bb0-3fa28da8b8fd.png#align=left&display=inline&height=486&margin=%5Bobject%20Object%5D&name=image.png&originHeight=486&originWidth=687&size=31801&status=done&style=none&width=687#align=left&display=inline&height=486&margin=%5Bobject%20Object%5D&originHeight=486&originWidth=687&status=done&style=none&width=687)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137286087-77efa6ef-bcad-488b-9bb0-3fa28da8b8fd.png#align=left&display=inline&height=486&margin=%5Bobject%20Object%5D&name=image.png&originHeight=486&originWidth=687&size=31801&status=done&style=none&width=687)

---

## 步骤五：配置语雀的 webhook

### 设定触发规则

在知识库中选择设置。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137446034-c58307fe-4aff-49c4-94b6-e10c68e163e1.png#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&name=image.png&originHeight=221&originWidth=300&size=11948&status=done&style=none&width=300#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&originHeight=221&originWidth=300&status=done&style=none&width=300)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137446034-c58307fe-4aff-49c4-94b6-e10c68e163e1.png#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&name=image.png&originHeight=221&originWidth=300&size=11948&status=done&style=none&width=300)
设定触发规则。粘贴在云函数处获取的访问路径（腾讯云）或 URL 链接（百度云）。
[![image.png](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137516162-582733c2-8201-40c9-9125-3b972bf7b218.png#align=left&display=inline&height=559&margin=%5Bobject%20Object%5D&name=image.png&originHeight=559&originWidth=1134&size=54956&status=done&style=none&width=1134#align=left&display=inline&height=559&margin=%5Bobject%20Object%5D&originHeight=559&originWidth=1134&status=done&style=none&width=1134)](https://cdn.nlark.com/yuque/0/2020/png/8391485/1608137516162-582733c2-8201-40c9-9125-3b972bf7b218.png#align=left&display=inline&height=559&margin=%5Bobject%20Object%5D&name=image.png&originHeight=559&originWidth=1134&size=54956&status=done&style=none&width=1134)
设置完毕后，你可以尝试发布一篇文章进行测试。如果 github action 执行则说明配置成功。
具体的样式测试请参考[CC 的部落格](https://blog.ccknbc.cc/posts/special-test-article/)
