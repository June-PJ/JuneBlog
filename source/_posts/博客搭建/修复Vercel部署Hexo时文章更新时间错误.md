---
title: 修复Vercel部署Hexo时文章更新时间错误
date: 2023-12-23 21:09:47
description: 我的博客是基于Hexo搭建的，用的是Vercel的自动部署。然后发现我每次push的时候，发现文章的更新时间都会重置，于是就在网上寻找方案
categories:
  - 博客搭建
tags:
  - 博客搭建
  - Hexo
  - Vercel
---

## 

## 前言

我的博客是基于Hexo搭建的，用的是Vercel的自动部署。然后发现我每次push的时候，发现文章的更新时间都会重置，于是就在网上寻找方案

### 参考链接

[修复 Vercel/Github Actions 部署 hexo 导致文章的更新时间错误 | Hello! I'm 0o酱 (im0o.top)](https://blog.im0o.top/posts/c6d9de72.html)

[How can I use GitHub Actions with Vercel? --- 如何在 Vercel 中使用 GitHub Actions？](https://vercel.com/guides/how-can-i-use-github-actions-with-vercel)

## 解决方案

我是用Github Action部署到Vercel解决的

### 1. 解除Vercel的自动部署

因为现在要采用Github Action部署，所以要把Vercel与GitHub解绑

首先进入到https://vercel.com/

![image-20231223215105786](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223215105786.png)

### 2. 获取 Vercel Access Token

跳转[Tokens - Account (vercel.com)](https://vercel.com/account/tokens)，创建TOKEN：`VERCEL_TOKEN`

![image-20231223222050505](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223222050505.png)

记住生成的TOKEN，等下要用

### 3. 获取 VERCEL_ORG_ID 和 VERCEL_PROJECT_ID

安装` Vercel CLI`

```
npm i -g vercel

yarn global add vercel
```

安装完成后，输入命令`vercel login`，进行登录

![image-20231223230232382](C:\Users\June\AppData\Roaming\Typora\typora-user-images\image-20231223230232382.png)

然后在博客根目录下执行命令 `vercel link`，创建一个 Vercel 项目，此操作会在博客根目录下生成一个 `.vercel` 文件夹，`.vercel/project.json` 里面包含了 `VERCEL_ORG_ID` 和 `VERCEL_PROJECT_ID`

### 4. 在 Github 仓库中添加 secrets

在 Github 仓库中的 `Settings` -> `Secrets and Variables` -> `Actions` 中添加以下秘钥和环境变量：

- VERCEL_TOKEN
- VERCEL_ORG_ID
- VERCEL_PROJECT_ID

![image-20231223230522914](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223230522914.png)

### 5. 创建 Github Action

创建`[blogRoot]/.github/workflows/deploy.yml` 的文件，添加以下内容：

```yml
name: Deploy Blog to Vercel Production Deployment
env:
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
on:
  push:
    branches:
      - 你的主分支
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          persist-credentials: false
          # 0 indicates all history for all branches and tags.
          fetch-depth: 0
      - name: Restore file modification time 🕒
        run: find source/_posts -name '*.md' | while read file; do touch -d "$(git log -1 --format="@%ct" "$file")" "$file"; done
        # run: "git ls-files -z | while read -d '' path; do touch -d \"$(git log -1 --format=\"@%ct\" \"$path\")\" \"$path\"; done"
      - name: Install Vercel-cli🔧
        run: npm install --global vercel@latest
      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}
      - name: Build Project Artifacts
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}
      - name: Deploy Project Artifacts to Vercel
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
```

**注意：**将分支改为你自己的主分支

最后提交就完成了
