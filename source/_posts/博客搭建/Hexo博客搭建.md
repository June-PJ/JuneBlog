---
title: Hexo博客搭建
date: 2023-12-03 15:46:34
description: 基于Hexo的个人博客搭建
---

## 1. Hexo安装

全局安装Hexo

```javascript
npm install hexo -g
```

### 1. 初始化博客

```
hexo init [文件夹名字]
```

### 2. 安装依赖

进入到刚刚创建的文件夹下，安装依赖

```javascript
npm install
```

### 3. 预览

本地启动Hexo

```
hexo serve
```

### 4. 其他常用指令

清缓存

```
hexo clean
```

生成页面

```
hexo generate
```

部署网站

```
hexo deploy
```

其他指令见：[Hexo官方文档](https://hexo.io/zh-cn/docs/commands)

## 2. 安装主题（butterfly）

详细文档：[Butterfly 安裝文檔(一) 快速開始 | Butterfly](https://butterfly.js.org/posts/21cfbf15/)

### 1. 下载主题

下面任选其一就行，国内用第二个快一些

```
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly

git clone -b master https://gitee.com/immyw/hexo-theme-butterfly.git themes/butterfly
```

### 2. 安装依赖

```
npm install
```

### 3. 修改配置

将主题目录下的`_config.yml`文件复制到hexo根目录，并改名为`_config.butterfly.yml`

修改根目录下的`_config.yml`文件
```
theme: butterfly
```

![image-20231204222621498](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231204222621498.png)

## 3. 主题优化

详细见https://butterfly.js.org/
