---
title: Java笔记（1）
date: 2024-01-08 19:30:20
categories: 
  - Java
tags:
  - Java
  - bug	
  - 错题
---
## 前言

​	最近接受了实验室的老项目，用的前后端不分离那一套，主要技术栈是SpringBoot+Thymeleaf+Layui。前后端都是自己搞，遇到了些奇奇怪怪的小问题（是我技术不到家），特此记录一下。

## 奇怪的问题

### 1. Mapper层写SQL时，前后的引号没用空格隔开导致SQL语句错误

这是最开始报错的语句，结果报错了，我从Controller层往下一个个查，到最后查SQL的时候才发现有问题。

```java
@Select("select ${tableName}.*, si_patientsbaseinfo.*" +
        "from ${tableName}" +
        "left join si_patientsbaseinfo on ${tableName}.patientsId = si_patientsbaseinfo.id" +
        "where ${where}" +
        "order by ${orderby}" +
        "LIMIT ${offset}, ${rows}")
List<Map> findByPageWithPatientId(@Param("tableName") String tableName, @Param("offset")int offset, @Param("rows")int rows, @Param("where")String where, @Param("orderby") String orderby);
```

这是报错的信息，`id`和`where`连起来了

![image-20240108195008744](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20240108195008744.png)

这是纠正后的，比较蠢的问题

```java
@Select(" select ${tableName}.*, si_patientsbaseinfo.* " +
        " from ${tableName} " +
        " left join si_patientsbaseinfo on ${tableName}.patientsId = si_patientsbaseinfo.id " +
        " where ${where} " +
        " order by ${orderby} " +
        " LIMIT ${offset}, ${rows} ")
List<Map> findByPageWithPatientId(@Param("tableName") String tableName, @Param("offset")int offset, @Param("rows")int rows, @Param("where")String where, @Param("orderby") String orderby);
```

### 2. 类上没加注解，导致返回的数据未序列化

最开始我是从系统页面发现的问题

![image-20240108195631514](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20240108195631514.png)

然后因为数据是从request里取的，所以调debugger看数据

![image-20240108195741813](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20240108195741813.png)

发现是从后端传来的数据就不对，对比发现少了个注解`@JsonFormat`，后面加上就没问题了

```java
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date receptionTime;
```

### 3. 前端页面无法从request里获取数据

因为是在源代码上面基础缝缝补补，所以写的代码也是照猫画虎

下面是后端和前端的相关代码，就是查出数据然后放在request里面，然后前端再根据`attribute`的值拿到数据

```java
@RequestMapping("/intoMedicalHistoryModify")
public String intoMedicalHistoryModify(HttpServletRequest request, Integer id) {
    MedicalHistoryVO mvo = new MedicalHistoryVO();
    mvo.setId(id);
    mvo = this.medicalHistoryService.get(mvo);

    request.setAttribute("mvo", mvo);
    this.logOperation("进入修改病人病历界面", OperationType.GOTO, true);
    return "web/patients/medicalHistory/medicalHistoryModify";
}
```

```javascript
let mvo = [[${mvo}]];
form.val("form", mvo);
```

结果就是死活拿不到数据

![image-20240108200801095](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20240108200801095.png)

![image-20240108200807757](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20240108200807757.png)

给我好一顿找，最后还是对比了老代码，才发现是`script`标签少写了一个属性，后面加上就能拿到数据了

```javascript
<script th:inline="javascript">
```

## 特别鸣谢

​	特别感谢chatGPT对我的大力帮助，没有他，代码写不了一点
