# MyBatis

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/MyBatis.png)\

## 一、MyBatis简介

### 1、MyBatis特性

1. MyBatis支持定制化SQL、存储过程以及高级映射（实体类的属性与数据库字段之间映射）的持久层框架
2. MyBatis几乎避免了所有JDBC代码和手动设置参数以及获取结果集
3. MyBatis可以使用简单的xml或注解用于配置和原始映射，将接口和java的pojo映射成数据库中的记录
4. MyBatis是一个半自动的ORM（对象关系映射）框架

### 2、和其他持久层技术对比

* JDBC

  SQL 夹杂在Java代码中耦合度高，导致硬编码内伤

  维护不易且实际开发需求中 SQL 有变化，频繁修改的情况多见

  代码冗长，开发效率低

* Hibernate 和 JPA

  操作简便，开发效率高

  程序中的长难复杂 SQL 需要绕过框架（框架内部封装了一些SQL，但是长难复杂的不一定有）

  内部自动生产的 SQL，不容易做特殊优化

  基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难。

  反射操作太多，导致数据库性能下降

* MyBatis

  轻量级，性能出色

  SQL 和 Java 编码分开，功能边界清晰。Java代码专注业务、SQL语句专注数据

  开发效率稍逊于HIbernate，但是完全能够接受

## 二、搭建MyBatis

## 1、创建Maven工程

#### a->打包方式：jar

#### b->引入依赖

```xml
 <!--设置打包方式-->
    <packaging>jar</packaging>

    <dependencies>
        <!-- Mybatis核心 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>

        <!-- junit测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.3</version>
        </dependency>
    </dependencies>
```

## 2、创建MyBatis的核心配置文件

核心配置文件习惯上命名为mybatis-config.xml，在整合Spring之后，此配置文件可以省略

核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息，存放的位置是src\main\resources目录下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="abc123"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```

## 3、创建mapper接口

MyBatis中的mapper接口相当于以前的dao，但是区别是mapper仅仅是接口，我们不需要提供实现类。mybatis有面向接口的编程

```java
public interface UserMapper {
}
```

## 4、创建MyBatis的映射文件

相关概念：ORM对象关系映射

* 对象：Java的实体类对象
* 关系：关系型数据库
* 映射：二者之间的对应关系

| java概念 | 数据库概念 |
| -------- | ---------- |
| 类       | 表         |
| 属性     | 字段/列    |
| 对象     | 记录/行    |

1. 映射文件的命名规则

   表所对应的实体类的类名 + Mapper.xml

   比如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml

   因此一个映射文件对应一个实体类，对应一张表的操作。有几张表就有几个Mapper接口以及映射文件

   MyBatis映射文件用于编写SQL，访问以及操作表中的数据

   MyBatis映射文件存放的位置：src\main\resource\mappers目录下