# SpringBoot2

## 一、简介

### 1、为什么用SpringBoot

能快速创建生产级别的spring应用

### 2、SpringBoot优点

* 创建独立的Spring应用，没有复杂的配置文件
* 内嵌web服务器
* 自动starter依赖，简化构建配置
* 自动配置Spring以及第三方的功能
* 无代码生成，无需编写XML

SpringBoot是整合Spring技术栈的一站式框架，是简化Spring技术栈的快速开发脚手架

### 3、微服务

* 微服务是一种架构风格
* 一个应用拆分为一组小型服务
* 每个服务运行在自己的进程内，可以独立部署和升级
* 服务之间使用轻量级的HTTP交互
* 服务围绕业务功能进行拆分
* 可以由全自动部署机制独立部署
* 去中心化，服务自治。服务可以使用不同的语言、不同的存储技术

同一个服务可以存放在不同的计算机上

### 4、分布式

​		分布式计算是计算机科学中一个研究方向，它研究如何把一个需要非常巨大的计算能力才能解决的问题分成许多小的部分，然后把这些部分分配给多个计算机进行处理，最后把这些计算结果综合起来得到最终的结果。

分布式的困难：远程调用、服务发现、负载均衡、服务容错、任务调度、配置管理

分布式的解决：SpringBoot + SpringCloud

SpringBoot 用来快速创建服务；SpringCloud用来连接服务

### 5、云原生

"云原生"可以简单的理解为：“一个应用系统借助云计算相关的周边技术进行设计研发，从而使该应用能完美的适配云上环境”。

原生应用如何上云。Cloud Native

上云的困难：

* 服务自愈：某一个服务C出错，能否在别的服务器再拉起一份C
* 弹性伸缩：流量高峰期，部署的要多一些
* 服务隔离：服务D,E出问题，但是不影响之前的服务A,B,C
* 自动化部署
* ......

上云解决：大厂学院

![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/19.png)

## 二、简单项目创建

### 1、创建maven

### 2、导入依赖（pom.xml）

```xml
<!--父依赖-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.6</version>
</parent>

<!--web场景依赖-->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

### 3、创建主程序类

在java下新建一个MainApplication类，主程序类

```java
package com.hua.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 *
 * SpringBootApplication:这是一个SpringBoot应用
 */
@SpringBootApplication
public class MainApplication {
    //主程序类，启动的入口
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
```

### 4、编写控制层（业务）

在java下新建包下创建

```java
package com.hua.boot.Controller;


import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

//@Controller
//@ResponseBody 传输到浏览器
@RestController //是Controller和ResponseBody的合体
public class HelloController {
    @RequestMapping("/hello")
    public String hello01(){
        return "Hello,SpringBoot2 !";
    }
}
```

### 5、测试

直接运行main方法即可



### 6、简化配置

SpringBoot2简化了配置，所有配置都可以放在resources下的application.properties下

## 三、自动配置原理





