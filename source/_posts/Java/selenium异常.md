---
title: selenium异常
date: '2023-04-09 18:22:26'
description: "selenium异常：Exception in thread “main“ org.openqa.selenium.remote.http.ConnectionFailedException: Una"
categories: 
  - Java
tags:
  - Java
  - Selenium
  - 爬虫
---

## 前言

今天用webdriver打开edge浏览器的时候，程序在创建EdgeDriver实例的时候报错，搞了一两个小时才搞好。

![selenium异常报错](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-8-1024x322.png)

## 解决方法

### 1. 添加启动参数

此方法参考：[selenium启动ChromiumDriver出现403错误的解决办法](https://zhuanlan.zhihu.com/p/613115179)

我原先采用的是无参构造，现在它报了403的错，所以干脆禁掉它。

```java
String key = "webdriver.edge.driver";
String value = "E:\\MyCode\\edgedriver\\msedgedriver_112.exe";
System.setProperty(key, value);

EdgeOptions edgeOptions = new EdgeOptions();
edgeOptions.addArguments("--remote-allow-origins=*");//解决 403 出错问题

WebDriver driver = new EdgeDriver(edgeOptions);
```

但是通过这种方法会有警告：

![警告](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-10-1024x84.png)

意思是：找不到用于 的 CDP 版本。您可能需要使用类似于“org.seleniumhq.selenium：selenium-devtools-v86：4.6.0”的内容包含对特定版本的CDP的依赖，其中版本（“v86”）与您正在使用的基于铬的浏览器的版本匹配，并且工件的版本号与Selenium的版本号相同。

### 2. 修改selenium依赖

看到上面说依赖的问题，我就想是不是我selenium依赖的版本不适配了，就修改了pom。

selenium的maven依赖：[selenium-maven依赖库](https://mvnrepository.com/artifact/org.seleniumhq.selenium/selenium-java)

将我原先`4.6.0`的版本切换为`4.8.0`：

```xml
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.8.0</version>
</dependency>
```

重新运行后就不会出现上种方法的警告了。

## 参考链接

此方法参考：[selenium启动ChromiumDriver出现403错误的解决办法](https://zhuanlan.zhihu.com/p/613115179)

selenium的maven依赖：[selenium-maven依赖库](https://mvnrepository.com/artifact/org.seleniumhq.selenium/selenium-java)