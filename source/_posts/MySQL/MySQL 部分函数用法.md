---
title: MySQL 部分函数用法
date: '2023-04-06 18:54:16'
description: "MySQL 部分函数用法，记录作者刷力扣MySQL时遇到的一些没见过的函数"
cover: https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/mysql-1.png
categories:
  - MySQL
tags:
  - MySQL
  - 函数
---

## 前言

本文记录作者刷力扣MySQL时遇到的一些没见过的函数。

**本文纯属搬运，在此只是记录一下，如有侵权，请联系作者**

## 1. rank排序函数

本部分转载自：小厂程序员DHJ - [MySQL rank() over、dense_rank() over、row_number() over 用法介绍](https://blog.csdn.net/qq_41057885/article/details/109176014)

[力扣（178. 分数排名)](https://leetcode.cn/problems/rank-scores/)

------

### 1.1 rank() over(业务逻辑)

**作用：查出指定条件后的进行排名，条件相同排名相同，排名间断不连续。**
说明：例如学生排名，使用这个函数，成绩相同的两名是并列，下一位同学空出所占的名次。即：1 1 3 4 5 5 7

```
select id, name, score, rank() over(order by score desc) 'rank' from student
```

![image-1](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-1.png)

### 1.2 dense_rank() over(业务逻辑)

**作用：查出指定条件后的进行排名，条件相同排名相同，排名间断不连续。
**说明：例如学生排名，使用这个函数，成绩相同的两名是并列，下一位同学空出所占的名次。即：1 1 3 4 5 5 7

```
select id, name, score, dense_rank() over(order by score desc) 'rank' from student
```

![image-2](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-2.png)

### 1.3 row_number() over(业务逻辑)

**作用：查出指定条件后的进行排名，条件相同排名相同，排名间断不连续。
**说明：例如学生排名，使用这个函数，成绩相同的两名是并列，下一位同学空出所占的名次。即：1 1 3 4 5 5 7

```
select id, name, score, row_number() over(order by score desc) 'rank' from student
```

![image-3](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-3.png)

## 2. 字符串拼接函数

本部分转载自：syslbjjly - [mysql 字符串拼接的几种方式](https://blog.csdn.net/syslbjjly/article/details/90640975)

[力扣（1484.按日期对销售的产品进行分组）](https://leetcode.cn/problems/group-sold-products-by-the-date/)

------

### 2.1 concat(string1, string2, ...)

**作用：将多个字符串无缝拼接
**说明：此方法在拼接的时候如果有一个值为NULL，则返回NULL

```
select concat("hello", "word") string
```

![image-6](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-6.png)

### 2.2 concat_ws(分隔符, string1, string2, ...)

**作用：将多个字符串带分隔符拼接
**说明：此方法在拼接的时候如果有分隔符为NULL，则返回NUL；其他参数为NULL时正常拼接

```
select concat_ws(', ', "hello", "word") string
```

![image-7](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-7.png)

### 2.3 group_concat([distinct] 字段 order by 顺序 separator 分隔符)

**作用：将字段按顺序带分隔符连接**
说明：distinct（去重）

```
select
    sell_date,
    count(distinct product) num_sold,
    group_concat(
        distinct product
        order by product
        separator ','
    ) products
from 
    Activities
group by sell_date
order by sell_date
```

## 参考链接

rank排序函数：小厂程序员DHJ - [MySQL rank() over、dense_rank() over、row_number() over 用法介绍](https://blog.csdn.net/qq_41057885/article/details/109176014)

字符串拼接函数：syslbjjly - [mysql 字符串拼接的几种方式](https://blog.csdn.net/syslbjjly/article/details/90640975)