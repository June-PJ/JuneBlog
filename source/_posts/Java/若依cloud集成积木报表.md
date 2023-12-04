---
title: 若依cloud集成积木报表
date: '2023-04-05 19:16:22'
description: 若依cloud集成积木报表
cover: https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/ruoyi.png
categories: 
  - Java
tags:
  - Java
  - 若依
  - 微服务
  - 积木报表
---

## 前言

这段时间做微服务的项目，需要用到积木报表，但又感觉Jeecg框架太大了，所以选择了若依框架。

我项目里采用的是新建一个报表模块，参考的是：辰鬼丫 - [若依Cloud集成积木报表](https://blog.csdn.net/athwang/article/details/127218202)

**本文章默认已经启动了ruoyi-cloud项目**

若依开发文档 - [若依Cloud开发文档](http://doc.ruoyi.vip/ruoyi-cloud/)

积木报表 - [ruoyi单体版集成积木报表](https://help.jeecg.com/jimureport/ruoyi.html)

### 1. 数据库配置

先导入初始化脚本到数据库中

积木报表 - [初始化数据库脚本](https://github.com/jeecgboot/JimuReport/tree/master/db)

![积木报表数据库脚本仓库](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20-1024x257.png)

选择你要导入的数据库，我选择的是 `ry-cloud`。导入成功后，就可以看见下面的表

![积木报表初始化数据库表](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-21-1024x239.png)

### 2. 后端项目构建

#### 配置pom.xml

主要导入的是积木报表的依赖：

```xml
<!-- JimuReport -->
<dependency>
    <groupId>org.jeecgframework.jimureport</groupId>
    <artifactId>jimureport-spring-boot-starter</artifactId>
    <version>1.5.6</version>
</dependency>
```

这是我整个 `pom.xml` 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://maven.apache.org/POM/4.0.0"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <groupId>com.ruoyi</groupId>
        <artifactId>ruoyi-modules</artifactId>
        <version>3.6.2</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>ruoyi-modules-report</artifactId>

    <description>
        ruoyi-modules-report报表模块
    </description>

    <dependencies>
        <!-- SpringCloud Alibaba Nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <!-- SpringCloud Alibaba Nacos Config -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>

        <!-- SpringCloud Alibaba Sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>

        <!-- SpringBoot Actuator -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!-- Swagger UI -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>${swagger.fox.version}</version>
        </dependency>

        <!-- Mysql Connector -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <!-- RuoYi Common DataSource -->
        <dependency>
            <groupId>com.ruoyi</groupId>
            <artifactId>ruoyi-common-datasource</artifactId>
        </dependency>

        <!-- RuoYi Common Log -->
        <dependency>
            <groupId>com.ruoyi</groupId>
            <artifactId>ruoyi-common-log</artifactId>
        </dependency>

        <!--  Common core -->
        <dependency>
            <groupId>com.ruoyi</groupId>
            <artifactId>ruoyi-common-core</artifactId>
        </dependency>

        <!-- JimuReport -->
        <dependency>
            <groupId>org.jeecgframework.jimureport</groupId>
            <artifactId>jimureport-spring-boot-starter</artifactId>
            <version>1.5.6</version>
        </dependency>

        <!-- RuoYi Common Swagger -->
        <dependency>
            <groupId>com.ruoyi</groupId>
            <artifactId>ruoyi-common-swagger</artifactId>
        </dependency>
        
    </dependencies>
    <build>
        <finalName>${project.artifactId}</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

#### 配置父模块的pom.xml

![ruoyi-modules的pom.xml](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-14-1024x661.png)

#### 配置积木报表数据源

```java
package com.ruoyi.report.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

/**
 * @author June
 * @description: 数据源配置类
 */
@Configuration
public class DataSourceConfig{
   /**
    *  1、bean的名称必须为minidaoDataSource，否则不生效
    *  2、jeecg.minidao-datasource对应的是yml中的jeecg下的minidao-datasource，可自定义
    */
    @Bean(name="minidaoDataSource")
    @ConfigurationProperties(prefix = "jeecg.minidao-datasource")
    public DataSource dataSource(){
        return DataSourceBuilder.create().build();
    }
}
```

#### 在启动类里添加注解

```java
@SpringBootApplication(
        exclude = {DataSourceAutoConfiguration.class},
        scanBasePackages = {"org.jeecg.modules.jmreport","com.ruoyi"})
```

#### 配置bootstrap.yml

我是在本地测试的，所以用的是本机地址，报表模块的端口号用的是9301，也可以修改成你自己空闲的端口号。

```yaml
# Tomcat
server:
  port: 9301

# Spring
spring:
  application:
    # 应用名称
    name: ruoyi-report
  profiles:
    # 环境配置
    active: dev
  cloud:
    nacos:
      discovery:
        # 服务注册地址
        server-addr: 127.0.0.1:8848
      config:
        # 配置中心地址
        server-addr: 127.0.0.1:8848
        # 配置文件格式
        file-extension: yml
        # 共享配置
        shared-configs:
          - application-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}
```

#### 在nacos创建报表模块的配置

![ruoyi-report-dev.yml](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-24.png)

```yaml
# spring配置
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    password: password

# mybatis配置
mybatis:
  configuration: 
    # 超时单位为秒
    default-statement-timeout: 900
  # 搜索指定包别名
  typeAliasesPackage: com.ruoyi.report
  # 配置mapper的扫描，找到所有的mapper.xml映射文件
  mapperLocations: classpath:mapper/**/*.xml
  
#Minidao配置
minidao :
  base-package: org.jeecg.modules.jmreport.desreport.dao*

jeecg :
  minidao-datasource:
    jdbc-url: jdbc:mysql://127.0.0.1:3306/ry-cloud?allowMultiQueries=true&useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&useSSL=false&serverTimezone=GMT%2b8
    username: root
    password: password
    driver-class-name: com.mysql.cj.jdbc.Driver
    hikari:
      # 连接池最大连接数
      maximum-pool-size: 400
      # 空闲时保持最小连接数
      minimum-idle: 20
      # 空闲连接存活时间
      idle-timeout: 30000
      # 连接超时时间
      connection-timeout: 1800000
      #池中连接最长生命周期
      max-lifetime: 1800000
  jmreport:
    #数据字典是否进行saas数据隔离(限制只能看自己的字典)
    saas: false
    #是否 禁用导出PDF和图片的按钮 默认为false
    exportDisabled: false
    #是否自动保存
    autoSave: false
    #自动保存间隔时间毫秒
    interval: 20000
    # 列索引
    col: 300
    #自定义项目前缀
    customPrePath: /dev-api/ruoyi-report
    # 自定义API接口的前缀 #{api_base_path}的值
    # apiBasePath: http://10.10.0.138:83/
    #预览分页自定义
    pageSize:
      - 10
      - 20
      - 50
      - 100
    #打印纸张自定义
    printPaper:
      - title: 标签打印
        size:
          - 140
          - 100        
    #接口超时设置（毫秒）
    connect-timeout: 1800000
    #Excel导出模式(fast/快、primary/精致模式，默认fast)
    export-excel-pattern: fast
    #Excel导出数据每个sheet的行数,每个sheet最大1048576行
    page-size-number: 1048576
    #excel样式超过多少行显示默认样式（只在fast模式下有效）
    excel-style-row: 1048576
    #预览页面的工具条 是否显示 默认true
    viewToolbar: true
    #设计页面表格的线是否显示 默认true
    line: true
    #sql数据源不写字典下拉框显示条数 版本1.4.2之后被放弃
    select-show-total: 10

#输出sql日志
logging:
  level:
    org.jeecg.modules.jmreport : info
```

#### 配置gateway网关

![网关配置](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-25.png)

```yaml
- id: ruoyi-report
  uri: lb://ruoyi-report
  predicates:
    - Path=/report/**
  filters:
    - StripPrefix=1
```

再在安全配置中添加

![安全配置](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-27.png)

```yaml
- /ruoyi-report/jmreport/**
- /ruoyi-report/**
```

**至此，后端就配置好了，可以启动该模块了。**

#### 拓展：自定义报表鉴权

```java
package com.ruoyi.report.service.impl;

import com.ruoyi.common.core.utils.DateUtils;
import com.ruoyi.common.core.utils.StringUtils;
import com.ruoyi.common.security.service.TokenService;
import com.ruoyi.common.security.utils.SecurityUtils;
import com.ruoyi.system.api.model.LoginUser;
import org.jeecg.modules.jmreport.api.JmReportTokenServiceI;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpHeaders;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import java.util.HashMap;
import java.util.Map;

/**
 * @author June
 *
 * 自定义报表鉴权(如果不进行自定义，则所有请求不做权限控制)
 */
@Component
public class JimuReportTokenServiceImpl implements JmReportTokenServiceI {

    @Autowired
    private TokenService tokenService;

    /**
     * 通过请求获取Token
     */
    @Override
    public String getToken(HttpServletRequest request) {
        String token = request.getParameter("token");
        String jmToken = request.getHeader("token");
        if (token == null || token.length() == 0) {
            token = jmToken;
        }
        LoginUser loginUser = tokenService.getLoginUser(token);
        if (loginUser != null) {
            return token;
        }
        return "";
    }

    /**
     * 获取登录人用户名
     */
    @Override
    public String getUsername(String s) {
        LoginUser loginUser = tokenService.getLoginUser(s);
        return loginUser.getUsername();
    }

    /**
     * Token校验
     */
    @Override
    public Boolean verifyToken(String s) {
        if (s != null && s.length() > 0) {
            LoginUser loginUser = tokenService.getLoginUser(s);
            return loginUser !=null;
        }
        return false;
    }
    
    /**
     *  自定义请求头
     */
    @Override
    public HttpHeaders customApiHeader() {
        HttpHeaders header = new HttpHeaders();
        header.add("X-Access-Token", SecurityUtils.getToken());
        return header;
    }

    /**
     * 获取多租户id
     * @return tenantId
     */
    public String getTenantId() {
        String token = SecurityUtils.getCurrentRequestInfo().getParameter("token");
        String header = SecurityUtils.getCurrentRequestInfo().getHeader("X-Access-Token");
        LoginUser loginUser = null;
        if(StringUtils.isNotBlank(token)){
            loginUser =  tokenService.getLoginUser(token);
        }else if(StringUtils.isNotBlank(header)){
            loginUser =  tokenService.getLoginUser(header);
        }else {
            //都不具备则不能访问
            return "NO";
        }
        //具备admin或者管理员权限才可访问所有报表
        if(SecurityUtils.isAdmin(loginUser.getUserid())
                || loginUser.getRoles().contains("it")
                || loginUser.getRoles().contains("manger")){
            return "";
        }
        return loginUser.getUsername();
    }

    @Override
    public Map<String, Object> getUserInfo(String token) {
        // 将所有信息存放至map 解析sql会根据map的键值解析,可自定义其他值
        Map<String, Object> map = new HashMap<>(20);
        LoginUser loginUser = tokenService.getLoginUser(token);
        map.put("sysUserCode",loginUser.getUsername());
        //设置当前日期（年月日）
        map.put("sysData", DateUtils.getDate());
        //设置昨天日期（年月日）
        map.put("sysYesterDay",DateUtils.getyesterday());
        //设置当前登录用户昵称
        map.put("sysUserName",loginUser.getSysUser().getNickName());
        //设置当前登录用户部门ID
        map.put("deptId",loginUser.getSysUser().getDeptId());
//        //设置当前登录用户部门描述
//        map.put("describe",loginUser.getSysUser().getDept().getDescribes());
        return map;
    }
}
```



### 3. 前端配置

![积木报表前端结构](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-16.png)

#### jimu.vue

```vue
<template>
  <i-frame :src="openUrl" id="jimuReportFrame"></i-frame>
</template>
<script>
  import { getToken } from '@/utils/auth'
  import iFrame from '@/components/iFrame/index'
  export default {
    name: "Jimu",
    components: {iFrame},
    data() {
      return {
        // openUrl: process.env.VUE_APP_BASE_API + "/jmreport/list?token="+getToken(),//（部署到服务器上用这个地址）
        openUrl: "http://localhost:9301/jmreport/list?token="+getToken(),
      };
    },
    mounted: function() {

    }
  };
</script>
```

#### view.vue

```vue
<template>
  <i-frame :src="openUrl"/>
</template>

<script>
import {getToken} from '@/utils/auth'
import iFrame from "@/components/iFrame/index";

export default {
  name: 'jimuview',
  components: {iFrame},
  props: {
    reportID: {
      type: [String],
      required: false,
      default: ''
    },
  },
  data() {
    return {
      openUrl: '',
    }
  },
  created() {
    if (this.reportID.length != 0) {
      // this.openUrl = process.env.VUE_APP_BASE_API + '/jmreport/view/' + this.reportID + '?token=' +  getToken()
      this.openUrl = "http://localhost:9301/jmreport/list/" + this.reportID + '?token=' + getToken()
    } else {
      // this.openUrl = process.env.VUE_APP_BASE_API + '/jmreport/view/' + this.$route.path.substring(this.$route.path.lastIndexOf("/") + 1) + '?token=' + getToken()
      this.openUrl = "http://localhost:9301/jmreport/list/" + this.$route.path.substring(this.$route.path.lastIndexOf("/") + 1) + '?token=' + getToken()
    }
  }
}
</script>

<style scoped>

</style>
```

#### 菜单配置

##### 新增菜单

`系统管理` -> `菜单管理` -> `新增`

![积木报表菜单配置](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-17.png)

##### 修改其他角色权限

`系统管理` -> `角色管理` -> `选择要修改的角色`，将积木报表菜单权限开放

![其他角色权限修改](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-18.png)

最后刷新系统，积木报表就集成成功了

![系统展示](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-19-1024x418.png)

## 参考链接

- 辰鬼丫 - [若依Cloud集成积木报表](https://blog.csdn.net/athwang/article/details/127218202)
- 若依开发文档 - [若依Cloud开发文档](http://doc.ruoyi.vip/ruoyi-cloud/)
- 积木报表 - [ruoyi单体版集成积木报表](https://help.jeecg.com/jimureport/ruoyi.html)
