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

相关概念：ORM    对象关系映射

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
   
2. MyBatis中可以面向接口操作数据，需要保持两个一致

   * mapper接口全类名和映射文件的命名空间(namespace)保持一致
   * mapper接口中方法的方法名和映射文件中编写的SQL的标签的id属性保持一致

```java
public interface UserMapper {

    /**
     * MyBatis面向接口编程的两个一致
     * 1、映射文件的namespace要和mapper接口的全类名保持一致
     * 2、映射文件中SQL语句的id要和mapper接口中方法名一致
     *
     * 表 - 实体类 - mapper接口 - 映射文件
     */

    /**
     * 添加用户信息
     */
    int insertUser();
}
```

```xml
<mapper namespace="com.hua.mybatis.mapper.UserMapper">
    <!--insertUser-->
    <insert id="insertUser">
        insert into t_user values(null,'admin','123456',23,'男','12345@qq.com')
    </insert>
</mapper>
```

## 5、测试

```java
public class test {
    @Test
    public void testMyBatis() throws IOException {
        //加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        //获取SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        //获取SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        //获取SqlSession,参数是设置是否自动提交事务，默认是false不提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        //获取mapper接口对象,不需要通过实现类就可以创建
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        //测试功能
        int result = mapper.insertUser();
        //提交事务
        //sqlSession.commit();
        System.out.println("result:" + result);
    }
}
```

* SqlSession：表示java程序和数据库之间的会话(HttpSession是java程序和浏览器之间的会话)
* SqlSessionFactory：是生产SqlSession的工厂
* 工厂模式：如果创建一个对象，使用的过程基本固定，我们可以把创建这个对象的相关代码封装到一个工厂类中，以后都使用这个工厂类来生产我们需要的对象

## 6、加入log4j日志功能

### a>加入依赖

```xml
<!-- log4j日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

### b>加入log4j配置文件

加入的位置在resources下面

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n" />
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug" />
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info" />
    </logger>
    <root>
        <level value="debug" />
        <appender-ref ref="STDOUT" />
    </root>
</log4j:configuration>
```

日志的级别

FATAL(致命) > ERROR(错误) > WARN(警告)  > INFO(信息) > DEBUG(调式)

级别越高，打印的内容越少

## 三、MyBatis实现增删改查

### 1、增

UserMapper接口

```java
public interface UserMapper {
    /**
     * MyBatis面向接口编程的两个一致
     * 1、映射文件的namespace要和mapper接口的全类名保持一致
     * 2、映射文件中SQL语句的id要和mapper接口中方法名一致
     *
     * 表 - 实体类 - mapper接口 - 映射文件
     */

    /**
     * 添加用户信息
     */
    int insertUser();

}

```

UserMapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 <!--namespace与接口的路径一致-->
<mapper namespace="com.hua.mybatis.mapper.UserMapper">
    <!--insertUser，id与接口中的方法名-->
    <insert id="insertUser">
        insert into t_user values(null,'admin','123456',23,'男','12345@qq.com')
    </insert>
</mapper>
```



测试类

```java
@Test
public void testAdd() throws IOException {
    //加载核心配置文件
    InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
    //获取SqlSessionFactoryBuilder
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    //获取SqlSessionFactory
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
    //获取SqlSession,默认是false不自动提交事务，true是设置自动提交
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    //获取mapper接口对象,不需要通过实现类就可以创建
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    //测试功能,调用接口中的方法
    int result = mapper.insertUser();
    System.out.println("result:" + result);
}
```

### 2、修改

UserMapper接口

```java
public interface UserMapper {
    /**
     * MyBatis面向接口编程的两个一致
     * 1、映射文件的namespace要和mapper接口的全类名保持一致
     * 2、映射文件中SQL语句的id要和mapper接口中方法名一致
     *
     * 表 - 实体类 - mapper接口 - 映射文件
     */
    /**
     * 修改用户信息
     */
    void updateUser();
}
```

UserMapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.UserMapper">
    <!--void updateUser()-->
    <update id="updateUser">
        update t_user set username = '张三' where id = 4
    </update>
</mapper>
```

测试文件

```java
@Test
public void testUpdate() throws IOException {
    //获取核心配置文件的流
    InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
    //准备建工厂
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    //建工厂
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
    //开启会话
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    //获取接口的实现类
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    //调用方法
    mapper.updateUser();

}
```

### 3、删除

UserMapper接口

```java
package com.hua.mybatis.mapper;

public interface UserMapper {

    /**
     * MyBatis面向接口编程的两个一致
     * 1、映射文件的namespace要和mapper接口的全类名保持一致
     * 2、映射文件中SQL语句的id要和mapper接口中方法名一致
     *
     * 表 - 实体类 - mapper接口 - 映射文件
     */

    /**
     * 删除用户信息
     */
    void deleteUser();
}
```

UerMapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.UserMapper">
    <!--void deleteUser()-->
    <delete id="deleteUser">
        delete from t_user where id = 5
    </delete>
</mapper>
```

测试文件

```java
@Test
public void testDelete() throws IOException {
    InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    mapper.deleteUser();
}
```

### 4、查询

**注意：在所有的查询的接口配置文件中需要添加属性resultType或resultMap**

#### 1、根据id查

UserMapper

```java
public interface UserMapper {

    /**
     * MyBatis面向接口编程的两个一致
     * 1、映射文件的namespace要和mapper接口的全类名保持一致
     * 2、映射文件中SQL语句的id要和mapper接口中方法名一致
     *
     * 表 - 实体类 - mapper接口 - 映射文件
     */
    /**
     * 根据id查询用户信息
     */
    User getUserById();

}
```

UserMapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.UserMapper">
    <!--User getUserById-->
    <!--
        查询功能的标签必须设置resultType或resultMap
        resultType：设置默认的映射关系，能够将读到的字段名匹配到对应的属性名
        resultMap：设置自定义的映射关系，字段名与属性名不一致
    -->
    <select id="getUserById" resultType="com.hua.mybatis.pojo.User">
        select * from t_user where id = 4
    </select>

</mapper>
```

测试类

```java
@Test
public void testgetUserById() throws IOException {
    InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.getUserById();
    System.out.println(user);
}
```

#### 2、查询所有的用户

UserMapper接口

```java
public interface UserMapper {

    /**
     * MyBatis面向接口编程的两个一致
     * 1、映射文件的namespace要和mapper接口的全类名保持一致
     * 2、映射文件中SQL语句的id要和mapper接口中方法名一致
     *
     * 表 - 实体类 - mapper接口 - 映射文件
     */

    /**
     * 查询所有的用户信息
     */
    List<User> getAllUser();
}
```

UerMapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.UserMapper">

    <!--List<User> getAllUser()-->
    <select id="getAllUser" resultType="com.hua.mybatis.pojo.User">
        select * from t_user
    </select>

</mapper>
```

测试类

```java
@Test
public void testgetAllUser() throws IOException {
    InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> user = mapper.getAllUser();
    for(User i : user){
        System.out.println(i);
    }
}
```

## 四、核心配置文件讲解（了解）

### 1、原始配置文件讲解

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置连接数据库的环境
        environments:配置多个连接数据库的环境
        属性：
            default：设置默认使用的环境的id
    -->
    <environments default="development">
        <!--
            environment：配置某个具体的环境
            属性：
                id：表示连接数据库的环境的唯一标识，不能重复，default就是调用的这边的值
        -->
        <environment id="development">
            <!--
                transactionManager：设置事务的管理方式
                属性：
                    type="JDBC/MANAGED"
                    JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事务的提交或回滚需要手动处理
                    MANAGED：被管理，例如Spring
            -->
            <transactionManager type="JDBC"/>
            <!--
                dataSource：配置数据源
                属性：
                    type：设置数据源的类型
                    type="POOLED/UNPOOLED/JNDI"
                    POOLED:表示使用数据库连接池缓存数据库连接
                    UNPOOLED：表示不使用数据库连接池缓存数据库连接
                    JNDI：表示使用上下文中的数据源
            -->
            <dataSource type="POOLED">
                <!--设置连接数据库的驱动-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <!--设置连接数据库的连接地址-->
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8"/>
                <!--设置连接数据库的用户名-->
                <property name="username" value="root"/>
                <!--设置连接数据库的密码-->
                <property name="password" value="abc123"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/Usermapper.xml"/>
    </mappers>
</configuration>
```

### 2、使用properrties文件进行数据库的连接

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--引入properties文件-->
    <properties resource="jdbc.properties"/>

    <!--配置连接数据库的环境
        environments:配置多个连接数据库的环境
        属性：
            default：设置默认使用的环境的id
    -->
    <environments default="development">
        <!--
            environment：配置某个具体的环境
            属性：
                id：表示连接数据库的环境的唯一标识，不能重复，default就是调用的这边的值
        -->
        <environment id="development">
            <!--
                transactionManager：设置事务的管理方式
                属性：
                    type="JDBC/MANAGED"
                    JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事务的提交或回滚需要手动处理
                    MANAGED：被管理，例如Spring
            -->
            <transactionManager type="JDBC"/>
            <!--
                dataSource：配置数据源
                属性：
                    type：设置数据源的类型
                    type="POOLED/UNPOOLED/JNDI"
                    POOLED:表示使用数据库连接池缓存数据库连接
                    UNPOOLED：表示不使用数据库连接池缓存数据库连接
                    JNDI：表示使用上下文中的数据源
            -->
            <dataSource type="POOLED">
                <!--设置连接数据库的驱动-->
                <property name="driver" value="${jdbc.driver}"/>
                <!--设置连接数据库的连接地址-->
                <property name="url" value="${jdbc.url}"/>
                <!--设置连接数据库的用户名-->
                <property name="username" value="${jdbc.username}"/>
                <!--设置连接数据库的密码-->
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/Usermapper.xml"/>
    </mappers>
</configuration>
```

### 3、类型别名

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--
    MyBatis核心配置文件中，标签的顺序
    properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,
    objectWrapperFactory?,reflectorFactory?,plugins?,environments?,
    databaseIdProvider?,mappers?

    注意：写出来的顺序必须按照上面的顺序，否则会报错
    -->

    <!--引入properties文件-->
    <properties resource="jdbc.properties"/>

    <!--typeAlias：设置类型别名
        属性：
            type：设置需要设置别名的类型
            alias：设置某个类型的别名，若不设置该属性，那么该属性拥有默认的别名，别名就是类名，且不区分大小写
    -->
    
    
    <!--在使用slect语句的resultType时可用-->
    <typeAliases>
        <!--<typeAlias type="com.hua.mybatis.pojo.User" alias="User"></typeAlias>-->
        <!--以包为单位将包下所有的类型设置默认的类型别名，别名就是类名，且不区分大小写-->
        <package name="com.hua.mybatis.pojo"/>
    </typeAliases>
    
    
    
    
    

    <!--配置连接数据库的环境
        environments:配置多个连接数据库的环境
        属性：
            default：设置默认使用的环境的id
    -->
    <environments default="development">
        <!--
            environment：配置某个具体的环境
            属性：
                id：表示连接数据库的环境的唯一标识，不能重复，default就是调用的这边的值
        -->
        <environment id="development">
            <!--
                transactionManager：设置事务的管理方式
                属性：
                    type="JDBC/MANAGED"
                    JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事务的提交或回滚需要手动处理
                    MANAGED：被管理，例如Spring
            -->
            <transactionManager type="JDBC"/>
            <!--
                dataSource：配置数据源
                属性：
                    type：设置数据源的类型
                    type="POOLED/UNPOOLED/JNDI"
                    POOLED:表示使用数据库连接池缓存数据库连接
                    UNPOOLED：表示不使用数据库连接池缓存数据库连接
                    JNDI：表示使用上下文中的数据源
            -->
            <dataSource type="POOLED">
                <!--设置连接数据库的驱动-->
                <property name="driver" value="${jdbc.driver}"/>
                <!--设置连接数据库的连接地址-->
                <property name="url" value="${jdbc.url}"/>
                <!--设置连接数据库的用户名-->
                <property name="username" value="${jdbc.username}"/>
                <!--设置连接数据库的密码-->
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/Usermapper.xml"/>
    </mappers>
</configuration>
```



```xml
<!--User getUserById-->
<!--
    查询功能的标签必须设置resultType或resultMap
    resultType：设置默认的映射关系，能够将读到的字段名匹配到对应的属性名
    resultMap：设置自定义的映射关系，字段名与属性名不一致
-->
<select id="getUserById" resultType="com.hua.mybatis.pojo.User">
    select * from t_user where id = 4
</select>

<!--List<User> getAllUser()
    因为在核心配置文件中设置了类型别名，所以可以通过User代替全路径名
    类型别名不区分大小写
-->
<select id="getAllUser" resultType="User">
    select * from t_user
</select>
```

### 4、mapper讲解

```xml
<!--引入映射文件-->
<mappers>
    <!--<mapper resource="mappers/Usermapper.mapper"/>-->
    <!--
        以包为单位引入映射文件
        要求：
        1、mapper接口所在包要和映射文件所在包一致
        2、mapper接口要和映射文件的名字一致
    -->
    <package name="com.hua.mybatis.mapper"/>
</mappers>
```

注意：在resources下创建包的时候不用 . 而是使用 /

### 5、一些技巧

* 在进行测试的时候，我们可以提取建工厂的部分到一个工具类

  ```java
  public class SqlSessionUtils {
      public static SqlSession getSqlSession(){
          SqlSession sqlSession = null;
          try {
              InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
              SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
              SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
              sqlSession = sqlSessionFactory.openSession(true);
  
          } catch (IOException e) {
              e.printStackTrace();
          }
          return sqlSession;
      }
  }
  ```

* 我们可以将核心配置文件以及接口的配置文件封装起来

  将写好的通用的代码赋值一下，在settings -> editor -> file and code templates中添加一个通用模板

## 五、MyBatis获取参数值的两种方式(重点)

目的：不写死sql语句，可以获取接口中传过来的参数

MyBatis获取参数值的两种方式：${} 和 #{}

${} 的本质就是**字符串的拼接**，#{} 的本质就是**占位符赋值**

${}使用字符串的方式拼接sql，若为字符串类型或者日期类型的字段进行赋值时，需要手动加单引号

#{}使用占位符的方式拼接sql，此时为字符串类型或者日期类型的字段进行赋值时，可以**自动添加单引号**

### 1、单个字面量类型的参数

若mapper接口中的方法参数为单个的字面量类型

此时可以使用${}和#{}以任意的名称获取参数的值，注意${}需要手动加单引号，括号里面可以随便写，但是最好与形参名一致

接口代码

```java
/**
 * 根据用户名查询用户信息
 */
User getUserByUername(String username);
```

配置文件代码

```xml
<!--User getUserByUername(String username)-->
<select id="getUserByUername" resultType="User">
    <!--select * from t_user where username = #{username}-->
    select * from t_user w9ere username = '${username}'
</select>
```

测试文件代码

```java
/**
 * MyBatis获取参数值的两种方式：${} 和 #{}
 * ${}本质字符串拼接，要注意sql注入
 * #{}本质占位符赋值
 * MyBatis来获取参数值的各种情况
 * 1、mapper接口方法的参数为单个的字面量类型
 *  可以通过${} 和 #{}以任意的名称获取参数值，但是要注意${}的单引号问题
 */
@Test
public void testGetUserByUsername(){
    //SqlSessionUtils是封装的工具类
    SqlSession sqlSession = SqlSessionUtils是封装的工具类.getSqlSession();
    ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
    User user = mapper.getUserByUername("张三");
    System.out.println(user);
}
```

### 2、多个字面量类型的参数

mapper接口的参数为多个值的时候
此时MyBatis会将这些参数放在一个map集合中，以两种方式进行存储
a->以arg0，arg1.....为键，以参数为值
b->以param1、param2....为键，以参数为值

因此我们只需要通过#{} 和 ${}以键的方式访问值即可，但是要注意${}的单引号问题。键值为系统设置的

接口

```java
/**
 * 验证登陆
 */
User checkLogin(String username,String password);
```

接口配置文件

```xml
<!--    User checkLogin(String username,String password)-->
<select id="checkLogin" resultType="User">
    <!--select * from t_user where username = #{arg0} and password = #{arg1}-->
    select * from t_user where username = '${arg0}' and password = '${arg1}'
</select>
```

测试文件

```java
@Test
public void testCheckLogin(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
    User user = mapper.checkLogin("张三","123456");
    System.out.println(user);
}
```

### 3、Map集合类型的参数

若mapper接口方法的参数有多个时，可以手动将这些参数放在一个map中进行存储

因此我们只需要通过#{} 和 ${}以键的方式访问值即可，但是要注意${}的单引号问题。键值为自己设置的

接口

```java
/**
 * 验证登录(参数为map)
 */
User checkLoginByMap(Map<String,Object> map);
```

接口配置文件

```java
<!--User checkLoginByMap(Map<String,Object> map)-->
<select id="checkLoginByMap" resultType="User">
    select * from t_user where username = #{username} and password = #{password}
</select>
```

测试类

```java
@Test
public void testCheckLoginByMap(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
    Map<String,Object> map = new HashMap<String,Object>();
    map.put("username","张三");
    map.put("password","123456");
    User user = mapper.checkLoginByMap(map);
    System.out.println(user);
}
```

### 4、实体类型的参数

若mapper接口中的方法参数为实体类对象时

此时可以使用${}和#{}，通过访问实体类对象中的属性名获取属性值，注意${}需要手动加单引号

接口代码

```java
/**
 * 添加用户信息
 */
int insertUser(User user);
```

接口代码配置文件

```xml
<!--int insertUser(User user)-->
<insert id="insertUser">
    insert into t_user values(null,#{username},#{password},#{age},#{sex},#{email})
</insert>
```

测试文件

```java
@Test
public void testInsertUser(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
    int result = mapper.insertUser(new User(null, "发子", "123456",23, "男", "123@qq.com"));
    System.out.println(result);
}
```

### 5、使用@Param标识参数

* 此时MyBatis会将这些参数放在一个map集合中，以两种方式进行存储
*  a->以@param注解的值为键，以参数为值
*  b->以param1、param2....为键，以参数为值
*  因此我们只需要通过#{} 和 ${}以键的方式访问值即可，但是要注意${}的单引号问题

接口代码

```java
/**
 * 验证登陆（使用@param）
 */
User checkLoginByParam(@Param("username") String username, @Param("password") String password);
```

接口配置文件代码

```xml
<!--User checkLoginByParam-->
<select id="checkLoginByParam" resultType="user">
    select * from t_user where username = #{username} and password = #{password}
</select>
```

测试文件

```java
@Test
public void testCheckLoginByParam(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
    User user = mapper.checkLoginByParam("张三","123456");
    System.out.println(user);
}
```

## 六、MyBatis各种查询功能

### 1、查询一个实体类对象

若查询出的数据只有一条
* a>可以通过实体类对象接收
* b>可以通过list集合接收
* c>可以通过map集合接收

接口代码

```java
/**
 * 根据id查询用户信息
 */
User getUserById(@Param("ID") Integer id);
```

接口配置文件代码

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.SelectMapper">
    <!--User getUserById()-->
    <select id="getUserById" resultType="User">
        select * from t_user where id = #{ID}
    </select>
</mapper>
```

测试类

```java
@Test
public void testgetUserById(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    User user = mapper.getUserById(7);
    System.out.println(user);
}
```

### 2、查询一个list集合

查询的数据有多条

a>可以通过list集合接收

b>可以通过map类型的list集合接收

接口代码

```java
/**
 * 查询所有数据
 */
List<User> getAllUser();
```

接口配置文件代码

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.SelectMapper">
    <!--List<User> getAllUser()-->
    <select id="getAllUser" resultType="User">
        select * from t_user
    </select>
</mapper>
```

测试文件

```java
@Test
public void testgetAllUser(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<User> user = mapper.getAllUser();
    for (User i : user){
        System.out.println(i);
    }
}
```

### 3、查询单个数据

接口代码

```java
/**
 * 查询用户信息的总记录数
 */
Integer getAllNumbers();
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.SelectMapper">
	<!--Integer getAllNumbers()，注意resultType的类型-->
    <select id="getAllNumbers" resultType="Integer">
        select count(*) from t_user
    </select>
</mapper>
```

测试文件

```java
@Test
public void testgetAllNumbers(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Integer numbers = mapper.getAllNumbers();
    System.out.println(numbers);
}
```

接口配置文件中的resultType中的值写的是别名，别名表如下：

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/7.png)

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/8.png)

### 4、查询一个数据为map集合

接口代码

```java
/**
 * 根据id查询用户信息为一个map集合
 */
Map<String,Object> getUserByIdToMap(@Param("ID") Integer id);
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.SelectMapper">
    <!--Map<String,Object> getUserByIdToMap(@Param("ID") Integer id)-->
    <select id="getUserByIdToMap" resultType="map">
        select * from t_user where id = #{ID}
    </select>
</mapper>
```

测试文件

```java
@Test
public void getUserByIdToMap(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Map<String, Object> map = mapper.getUserByIdToMap(4);
    System.out.println(map);
    //存储在map中，以字段为键，以属性值为键值
    System.out.println(map.get("username"));
}
```

### 5、查询多条数据为map集合

方式一：将查询到的每一个map存储在List集合中

接口代码

```java
/**
 * 查询所有用户信息为map集合
 */
List<Map<String,Object>> getAllUserToMap();
```

接口代码配置文件

```xml
<!--Map<String,Object> getAllUserToMap()-->
<select id="getAllUserToMap" resultType="map">
    select * from t_user
</select>
```

测试文件

```java
@Test
public void getAllUserToMap(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<Map<String, Object>> allUserToMap = mapper.getAllUserToMap();
    System.out.println(allUserToMap);
    }
```

方式二：完全通过map存储

接口代码：

```java
    /**
     * 查询所有用户信息为map集合
     */
    @MapKey("id")//作用，将id作为map的键，将map结果作为对应的值。挑选的键要唯一
    Map<String,Object> getAllUserToMap();
```

接口配置文件代码：

```xml
<!--Map<String,Object> getAllUserToMap()-->
<select id="getAllUserToMap" resultType="map">
    select * from t_user
</select>
```

测试文件

```java
    @Test
    public void getAllUserToMap(){
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        Map<String, Object> allUserToMap = mapper.getAllUserToMap();
        System.out.println(allUserToMap);
    }
```

## 七、执行特殊的SQL

主要考虑的是使用${}还是#{}，注意两者的区别

### 1、模糊查询

注意接口代码配置文件的三种方式，推荐最后一种。不能使用select * from t_user where username like '%#{Username}%'，因为#{Username}会自动添加上单引号

接口代码

```java
/**
 * 根据用户名模糊查询用户信息
 */
List<User> getUserByLike(@Param("Username") String username);
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.SQLMapper">
    <!--List<User> getUserByLike(@Param("Username") String username)-->
    <select id="getUserByLike" resultType="User">
        <!--select * from t_user where username like '%${Username}%'-->
        <!--select * from t_user where username like concat('%',#{Username},'%')-->
        select * from t_user where username like "%"#{Username}"%"
    </select>
</mapper>
```

测试文件

```java
@Test
public void testGetUserByLike(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
    List<User> list = mapper.getUserByLike("a");
    System.out.println(list);
}
```

### 2、批量删除

只能使用${}，因为#{}会自动添加上单引号

接口代码：

```java
/**
 * 批量删除
 */
int deleteMore(@Param("ids") String ids);
```

接口配置文件：

```xml
<!--int deleteMore(@Param("ids") String ids)-->
<delete id="deleteMore">
    delete from t_user where id in (${ids})
</delete>
```

测试文件

```java
@Test
public void testDeleteMore(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
    int result = mapper.deleteMore("1,2,4");
    System.out.println(result);
}
```

### 3、动态设置表名

只能使用${}，因为表名不能加单引号

接口代码：

```java
/**
 * 查询指定表中数据
 */
List<User> getUserByTableName(@Param("tableName") String tableName);
```

接口代码配置文件

```xml
<!--   List<User> getUserByTableName(@Param("tableName") String tableName)-->
<select id="getUserByTableName" resultType="User">
    select * from ${tableName}
</select>
```

测试文件

```java
@Test
public void testGetUserByTableName(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
    List<User> t_user = mapper.getUserByTableName("t_user");
    System.out.println(t_user);
}
```

### 4、添加功能获取自增的主键

接口代码：

```java
/**
 * 添加用户信息
 */
void insertUser(User user);
```

接口配置文件代码：

```xml
<!--void insertUser(User user)
    useGeneratedKeys：设置当前标签中的sql使用了自增的主键
    Property：将自增的主键赋值给传输到映射文件中参数的某个属性
-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into t_user values(null,#{username},#{password},#{age},#{sex},#{email})
</insert>
```

测试文件

```java
@Test
public void testInsertUser(){
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
    User user = new User(null,"afei","123",23,"男","123@qq.com");
    mapper.insertUser(user);
    System.out.println(user);
}
```

## 八、自定义映射(ResultMap)

本节主要讲字段名与属性名不匹配时解决的手法

### 1、环境搭建(最新)

1. 创建maven工程

2. 导入依赖

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>com.hua.mybatis</groupId>
       <artifactId>MyBatis_demo3</artifactId>
       <version>1.0-SNAPSHOT</version>
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
   
           <!-- log4j日志 -->
           <dependency>
               <groupId>log4j</groupId>
               <artifactId>log4j</artifactId>
               <version>1.2.17</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

3. 在resources下写jdbc配置文件和日志文件

   jdbc

   ```properties
   jdbc.driver=com.mysql.jdbc.Driver
   jdbc.url=jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8
   jdbc.username=root
   jdbc.password=abc123
   ```

   日志文件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
   <log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
       <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
           <param name="Encoding" value="UTF-8" />
           <layout class="org.apache.log4j.PatternLayout">
               <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n" />
           </layout>
       </appender>
       <logger name="java.sql">
           <level value="debug" />
       </logger>
       <logger name="org.apache.ibatis">
           <level value="info" />
       </logger>
       <root>
           <level value="debug" />
           <appender-ref ref="STDOUT" />
       </root>
   </log4j:configuration>
   ```

4. 写核心配置文件，在resources下

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <properties resource="jdbc.properties"/>
       <typeAliases>
           <package name="com.hua.mybatis.pojo"/>
       </typeAliases>
   
       <!--配置连接数据库的环境-->
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <property name="driver" value="${jdbc.driver}"/>
                   <property name="url" value="${jdbc.url}"/>
                   <property name="username" value="${jdbc.username}"/>
                   <property name="password" value="${jdbc.password}"/>
               </dataSource>
           </environment>
       </environments>
   
       <!--引入映射文件-->
       <mappers>
           <package name="com.hua.mybatis.mapper"/>
       </mappers>
   </configuration>
   ```

5. 在java(mapper和pojo)和resources(mapper)下创建对应的包，注意resources下创建包的方式

6. 编写实体类以及对应的配置文件

### 2、通过起别名来阶段字段名与属性名不同

为字段起别名，保持和属性名的一致，如果不起别名也不会报错，只是不一致名字的就没有显示了

接口：

```java
/**
 * 查询所有员工信息
 */
List<Emp> getAllEmp();
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.EmpMapper">
    <!--List<Emp> getAllEmp()-->
    <select id="getAllEmp" resultType="Emp">
       select eid,emp_name empName,age,sex,email from t_emp  
    </select>
</mapper>
```

测试文件

```java
@Test
public void testGetAllEmp(){
    SqlSession sqlSession = SQLSessionUtils.getSQLSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    List<Emp> allEmp = mapper.getAllEmp();
    System.out.println(allEmp);
}
```

### 3、设置全局配置

通过设置全局配置，将 _ 自动映射为驼峰

在核心配置文件中添加

```xml
<!--设置MyBatis的全局配置-->
<settings>
    <!--将 _ 自动映射为驼峰，比如：emp_name : empName-->
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

接口

```java
/**
 * 查询所有员工信息
 */
List<Emp> getAllEmp();
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.EmpMapper">
    <!--List<Emp> getAllEmp()-->
    <select id="getAllEmp" resultType="Emp">
        <!--select eid,emp_name empName,age,sex,email from t_emp-->
        select * from t_emp
    </select>
</mapper>
```

测试文件

```java
@Test
public void testGetAllEmp(){
    SqlSession sqlSession = SQLSessionUtils.getSQLSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    List<Emp> allEmp = mapper.getAllEmp();
    System.out.println(allEmp);
}
```

### 4、通过resultMap

接口代码

```java
/**
 * 查询所有员工信息
 */
List<Emp> getAllEmp();
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.EmpMapper">
    <!--
        resultMap：设置自定义映射关系
        id:设置唯一标识，不可重复
        type：设置映射关系的实体类类型
        子标签：
            id:设置主键的映射关系
            result:设置普通字段的映射关系
        属性：
        property:设置映射关系中的属性名，必须是type属性所设置的实体类类型中的属性名
        column：设置映射关系中的字段名，必须是sql语句查询出的字段名
    -->
    <resultMap id="empResultMap" type="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="email" column="email"></result>
    </resultMap>

    <!--List<Emp> getAllEmp()-->
    <select id="getAllEmp" resultMap="empResultMap">
        select * from t_emp
    </select>
</mapper>
```

测试文件

```java
@Test public void testGetAllEmp(){
    SqlSession sqlSession = SQLSessionUtils.getSQLSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    List<Emp> allEmp = mapper.getAllEmp();
    System.out.println(allEmp);
}
```

### 5、多对一映射处理

查询员工信息以及员工所对应的部门信息

1. 方式一：级联方式处理映射关系

   接口代码

   ```java
   /**
    * 查询所有员工信息
    */
   List<Emp> getAllEmp();
   ```

   

   **要在emp类中创建Dept类以及对应的get和set方法**

   

   接口配置文件

   ```xml
   <!--处理多对一映射关系方式一：级联属性赋值-->
   <resultMap id="empAndDeptResultMapOne" type="Emp">
       <id property="eid" column="eid"/>
       <result property="empName" column="emp_name"></result>
       <result property="age" column="age"></result>
       <result property="sex" column="sex"></result>
       <result property="email" column="email"></result>
       <result property="dept.did" column="did"></result>
       <result property="dept.deptName" column="dept_name"></result>
   </resultMap>
   <!--Emp getEmpAndDept(@Param("eid") Integer eid)-->
   <select id="getEmpAndDept" resultMap="empAndDeptResultMapOne">
       select * from t_emp left join t_dept on t_emp.did = t_dept.did where t_emp.eid = #{eid}
   </select>
   ```

   测试文件

   ```java
   @Test public void testEmpAndDept(){
       SqlSession sqlSession = SQLSessionUtils.getSQLSession();
       EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
       Emp empAndDept = mapper.getEmpAndDept(1);
       System.out.println(empAndDept);
   }
   ```

2. 通过association来处理多对一的关系

   接口代码

   ```java
   /**
    * 查询员工以及员工所对应部门的信息
    */
   Emp getEmpAndDept(@Param("eid") Integer eid);
   ```

   配置文件

   ```xml
   <!--处理多对一映射关系方式二：通过association-->
   <resultMap id="empAndDeptResultMapTwo" type="Emp">
       <id property="eid" column="eid"/>
       <result property="empName" column="emp_name"></result>
       <result property="age" column="age"></result>
       <result property="sex" column="sex"></result>
       <result property="email" column="email"></result>
       <!--
           association：处理多对一的映射关系
           property:需要处理多对一映射关系的属性名
           javaType:该属性的类型
       -->
       <association property="dept" javaType="Dept">
           <id property="did" column="did"></id>
           <result property="deptName" column="dept_name"></result>
       </association>
   </resultMap>
   <!--Emp getEmpAndDept(@Param("eid") Integer eid)-->
   <select id="getEmpAndDept" resultMap="empAndDeptResultMapTwo">
       select * from t_emp left join t_dept on t_emp.did = t_dept.did where t_emp.eid = #{eid}
   </select>
   ```

   测试文件

   ```java
   @Test public void testEmpAndDept(){
       SqlSession sqlSession = SQLSessionUtils.getSQLSession();
       EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
       Emp empAndDept = mapper.getEmpAndDept(1);
       System.out.println(empAndDept);
   }
   ```

3. 分步查询处理多对一的关系

   接口代码

   EmpMapper

   ```java
   /**
    * 通过分步查询员工以及员工所对应部门的信息
    * 分布查询第一步查询员工信息
    */
   Emp getEmpAndDeptByStepOne(@Param("eid") Integer eid);
   ```

   DeptMapper

   ```java
   /**
    * 通过分布查询员工以及员工所对应的部门信息
    * 分布查询的第二步：通过did查询员工所对应的部门
    */
   Dept getEmpAndDeptByStepTwo(@Param("did") Integer did);
   ```

   配置文件代码

   EmpMapper.xml

   ```xml
   <resultMap id="empAndDeptByStepResultMap" type="Emp">
       <id property="eid" column="eid"/>
       <result property="empName" column="emp_name"></result>
       <result property="age" column="age"></result>
       <result property="sex" column="sex"></result>
       <result property="email" column="email"></result>
       <!--
           select:设置分布查询的sql的唯一标识(namesqace.SQLId 或 mapper接口的全类名.方法名)
           column:设置分布查询的条件
   		fetchType:当开启全局的延迟加载之后，可通过此属性手动控制延迟加载的效果
               fetchType="lazy/eager"
                   eager:立即加载
                   lazy：延迟加载
       -->
       <association property="dept"
                    select="com.hua.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
                    column="did"></association>
   </resultMap>
   <!--Emp getEmpAndDeptByStepOne(@Param("eid") Integer eid)-->
   <select id="getEmpAndDeptByStepOne" resultMap="empAndDeptByStepResultMap">
       select * from t_emp where eid = #{eid}
   </select>
   ```

   DeptMapper.xml

   ```xml
   <mapper namespace="com.hua.mybatis.mapper.DeptMapper">
       <!--Dept getEmpAndDeptByStepTwo(@Param("did") Integer did)-->
       <select id="getEmpAndDeptByStepTwo" resultType="Dept">
           select * from t_dept where did = #{did}
       </select>
   </mapper>
   ```

   测试文件

   ```java
   @Test public void testGetEmpAndDeptByStep(){
       SqlSession sqlSession = SQLSessionUtils.getSQLSession();
       EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
       Emp empAndDept = mapper.getEmpAndDeptByStepOne(1);
       System.out.println(empAndDept);
   }
   ```

延迟加载

### 6、一对多映射处理

注意：一对多，在一实体类里面创建一的集合；多对一：在多里面创建一的对象

1. 方式一：使用collection

   接口代码

   ```java
   /**
    * 部门以及部门中所有员工信息
    *
    */
   Dept getDeptAndEmp(@Param("did") Integer did);
   ```

   

   注意：需要在dept里面创建emp集合

   

   接口配置

   ```xml
   <resultMap id="deptAndEmpResultMap" type="Dept">
       <id property="did" column="did"></id>
       <result property="deptName" column="dept_name"></result>
       <!--
           Collection：处理一对多的映射关系
           ofType：表示该属性所对应的集合中存储数据的类型
       -->
       <collection property="emps" ofType="Emp">
           <id property="eid" column="eid"></id>
           <result property="empName" column="emp_Name"></result>
           <result property="age" column="age"></result>
           <result property="sex" column="sex"></result>
           <result property="email" column="email"></result>
       </collection>
   </resultMap>
   
   <!--Dept getDeptAndEmp(@Param("did") Integer did)-->
   <select id="getDeptAndEmp" resultMap="deptAndEmpResultMap">
       select * from t_dept left join t_emp on t_dept.did = t_emp.did where t_dept.did = #{did}
   </select>
   ```

   测试文件

   ```java
   @Test
   public void testGetDeptAndEmp(){
       SqlSession sqlSession = SQLSessionUtils.getSQLSession();
       DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
       Dept deptAndEmp = mapper.getDeptAndEmp(1);
       System.out.println(deptAndEmp);
   }
   ```

2. 方式二：分步查询

   接口代码

   ```java
   /**
    * 通过分步查询查询部门以及部门中所有员工的信息
    * 分步查询第一步：查询部门信息
    */
   Dept getDeptAndEmpByStepOne(@Param("did") Integer did);
   ```

   在Dept中创建对应着emp的集合

   

   接口配置文件

   ```xml
   <resultMap id="deptAndEmpByStepResultMap" type="Dept">
       <id property="did" column="did"></id>
       <result property="deptName" column="dept_name"></result>
       <collection property="emps"
                   select="com.hua.mybatis.mapper.EmpMapper.getDeptAndEmpByStepTwo"
                   column="did"></collection>
   </resultMap>
   <!--Dept getDeptAndEmpByStepOne(@Param("did") Integer did)-->
   <select id="getDeptAndEmpByStepOne" resultMap="deptAndEmpByStepResultMap">
       select * from t_dept where did = #{did}
   </select>
   ```

   接口代码

   ```java
   /**
    * 通过分步查询查询部门以及部门中所有员工的信息
    * 分步查询第二步：根据did查询员工信息
    */
   List<Emp> getDeptAndEmpByStepTwo(@Param("did") Integer id);
   ```

   接口配置文件

   ```xml
   <!--List<Emp> getDeptAndEmpByStepTwo(@Param("did") Integer id)-->
   <select id="getDeptAndEmpByStepTwo" resultType="Emp">
       select * from t_emp where did = #{did}
   </select>
   ```

   测试文件

   ```java
   @Test
   public void testGetDeptAndEmpByStep(){
       SqlSession sqlSession = SQLSessionUtils.getSQLSession();
       DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
       Dept deptAndEmp = mapper.getDeptAndEmpByStepOne(1);
       System.out.println(deptAndEmp);
   }
   ```

## 九、动态SQL

动态SQL功能：帮助我们实现sql语句的拼接，主要是拼接关键字和条件

### 1、if标签

if标签可以通过属性test的表达式来进行判断，如果表达式的结果为true，则标签内容执行，否则不执行。

接口代码：

```java
/**
 * 多条件查询
 */
List<Emp> getEmpCondition(Emp emp);
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.DynamicSQLMapper">

    <!--List<Emp> getEmpCondition(Emp emp)-->
    <select id="getEmpCondition" resultType="Emp">
        <!--加1 = 1的原因，如果第一个if不成立，执行第二个if的时候会多一个and1-->
        select * from t_emp where 1 = 1
        <!--如果没有传参数则默认是null或者是空字符串-->
        <if test="empName != null and empName != '' ">
            emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="sex != null and sex != ''">
            and sex = #{sex}
        </if>
        <if test="email != null and email != ''">
            and email = #{email}
        </if>
    </select>
</mapper>
```

测试文件

```java
/**
 * 动态sql
 * 1、if：根据标签中test属性所对应的表达式决定标签中的内容是否需要拼接到SQL中
 */
@Test
public void testDynamic(){
    SqlSession sqlSession = SQLSessionUtils.getSQLSession();
    DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
    List<Emp> emp = mapper.getEmpCondition(new Emp(null, null, null, "男", "123@qq.com"));
    System.out.println(emp);
}
```

### 2、where标签

where：当where标签中有内容时，会自动生成where关键字，并且将内容前多余的and 或 or去掉
*           当where标签没有内容时，此时where标签没有任何效果
*           注意：where标签不能将其中内容后面的多余的and 或 or 去掉

与if相比，只有接口配置文件有变化

```xml
<select id="getEmpCondition" resultType="Emp">
    select * from t_emp
    <!--如果where里面有内容使用where标签能自动的生成where关键字，能够自动的去除and/or关键字-->
    <where>
        <!--如果没有传参数则默认是null或者是空字符串-->
        <if test="empName != null and empName != '' ">
            emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="sex != null and sex != ''">
            and sex = #{sex}
        </if>
        <if test="email != null and email != ''">
            and email = #{email}
        </if>
    </where>
</select>
```

### 3、trim标签

若标签中有内容时：

*          perfix/suffix:将trim标签中内容前面或后面添加指定内容
*          suffixOverrides/prefixOverrides:将trim标签中内容前面或后面去掉指定内容

若标签中没有内容时：trim标签也没有任何效果

```xml
<select id="getEmpCondition" resultType="Emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and|or">
        <!--如果没有传参数则默认是null或者是空字符串-->
        <if test="empName != null and empName != '' ">
            emp_name = #{empName} and
        </if>
        <if test="age != null and age != ''">
            age = #{age} or
        </if>
        <if test="sex != null and sex != ''">
            sex = #{sex} and
        </if>
        <if test="email != null and email != ''">
            email = #{email}
        </if>
    </trim>
</select>
```

### 4、choose,when,otherwise

choose,when,otherwise 相当于if...else if...else..,只会执行一个
*          when:至少有一个，otherwise至多有一个
*          choose是父标签

接口代码：

```java
/**
 * 测试choose when  otherwise
 */
List<Emp> getEmpByChoose(Emp emp);
```

接口配置文件

```xml
<select id="getEmpCondition" resultType="Emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and|or">
        <!--如果没有传参数则默认是null或者是空字符串-->
        <if test="empName != null and empName != '' ">
            emp_name = #{empName} and
        </if>
        <if test="age != null and age != ''">
            age = #{age} or
        </if>
        <if test="sex != null and sex != ''">
            sex = #{sex} and
        </if>
        <if test="email != null and email != ''">
            email = #{email}
        </if>
    </trim>
</select>
```

测试文件

```java
@Test
public void testGetEmpByChoose(){
    SqlSession sqlSession = SQLSessionUtils.getSQLSession();
    DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
    List<Emp> emp = mapper.getEmpByChoose(new Emp(null, null, null, null, null));
    System.out.println(emp);

}
```

### 5、foreach

foreach就是用来循环遍历的，foreach标签的属性

collection:设置需要循环的数组或集合
item：表示数组或集合中每一个数据
separator：循环体之间的分隔符
open：foreach标签所循环的所有内容的开始符
close：foreach标签所循环的所有内容的结束符

1. foreach标签用在数组上面

   接口代码

   ```java
   /**
    * 通过数组实现批量删除
    */
   int deleteMoreByArray(@Param("eids") Integer[] eids);
   ```

   接口配置文件

   ```xml
   <!--int deleteMoreByArray(Integer[] eids)-->
   <delete id="deleteMoreByArray">
   
       <!--
           delete form t_emp where eid in
           <foreach collection="eids" item="eid" separator="," close=")" open="(">
              #{eid}
           </foreach>
       -->
   
       delete from t_emp where
       <!--
           collection:是对应的参数Param
           item：是遍历的每一个
           separator:是分隔符
           open：是开始符号
           close：是结束符号
       -->
       <foreach collection="eids" item="eid" separator="or">
           eid = #{eid}
       </foreach>
   </delete>
   ```

   测试文件

   ```java
   @Test
   public void testDeleteMoreByAray(){
       SqlSession sqlSession = SQLSessionUtils.getSQLSession();
       DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
       int i = mapper.deleteMoreByArray(new Integer[]{7,8});
       System.out.println(i);
   }
   ```

2. foreach用在集合上面

   接口代码

   ```java
   /**
    * 通过list集合实现批量添加
    */
   int insertMoreByList(@Param("emps") List<Emp> emps);
   ```

   接口配置文件

   ```xml
   <!--int insertMoreByList(List<Emp> emps)-->
   <insert id="insertMoreByList">
       insert into t_emp values
       <foreach collection="emps" item="emp" separator=",">
           (null,#{emp.empName},#{emp.age},#{emp.sex},#{emp.email},null)
       </foreach>
   </insert>
   ```

   测试文件

   ```java
   @Test
   public void testinsertMoreByList(){
       SqlSession sqlSession = SQLSessionUtils.getSQLSession();
       DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
       Emp emp1 = new Emp(null,"a1",23,"男","123@qq.com");
       Emp emp2 = new Emp(null,"a2",23,"男","123@qq.com");
       Emp emp3 = new Emp(null,"a3",23,"男","123@qq.com");
       List<Emp> emps = Arrays.asList(emp1,emp2,emp3);
       int i = mapper.insertMoreByList(emps);
       System.out.println(i);
   }
   ```

### 6、sql标签

```java
* 声明sql片段：
*     <!--sql片段-->
*     <sql id="empColumns">eid,emp_name,age,sex,email</sql>
* 使用sql片段：
*     <include refid="empColumns"></include>
```

## 十、MyBatis的缓存

缓存只针对查询功能有效

### 1、MyBatis的一级缓存

一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存下来，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问。一级缓存是自动开启的

一级缓存失效的情况

1. 不同的SqlSession对应不同的一级缓存

2. 同一个SqlSession但是查询的条件不同

3. 同一个SqlSession两次查询期间执行了任何一次增删改操作

4. 同一个SqlSession两次查询期间手动清空了缓存

   ```
   //清除一级缓存
   sqlSession1.clearCache();
   ```

举例：

接口代码：

```java
public interface CacheMapper {
    Emp getEmpByEid(@Param("eid") Integer eid);
}
```

接口配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hua.mybatis.mapper.CacheMapper">
    <!--Emp getEmpByEid(@Param("eid") Integer eid)-->
    <select id="getEmpByEid" resultType="Emp">
        select * from t_emp where eid = #{eid}
    </select>
</mapper>
```

测试文件

```java
public class CacheMapperTest {
    @Test
    public void testCache(){
        SqlSession sqlSession1 = SQLSessionUtils.getSQLSession();
        CacheMapper mapper1 = sqlSession1.getMapper(CacheMapper.class);
        Emp emp1 = mapper1.getEmpByEid(1);
        System.out.println(emp1);
        CacheMapper mapper2 = sqlSession1.getMapper(CacheMapper.class);
        Emp emp2 = mapper2.getEmpByEid(1);
        System.out.println(emp2);

        SqlSession sqlSession2 = SQLSessionUtils.getSQLSession();
        CacheMapper mapper3 = sqlSession2.getMapper(CacheMapper.class);
        Emp empByEid = mapper3.getEmpByEid(1);
        System.out.println(empByEid);
    }
}
```

执行结果

DEBUG 04-14 11:24:47,487 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 04-14 11:24:47,506 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 04-14 11:24:47,521 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张三', age=1, sex='男', email='123@qq.com', dept=null}
Emp{eid=1, empName='张三', age=1, sex='男', email='123@qq.com', dept=null}
DEBUG 04-14 11:24:47,593 ==>  Preparing: select * from t_emp where eid = ? (BaseJdbcLogger.java:137) 
DEBUG 04-14 11:24:47,594 ==> Parameters: 1(Integer) (BaseJdbcLogger.java:137) 
DEBUG 04-14 11:24:47,595 <==      Total: 1 (BaseJdbcLogger.java:137) 
Emp{eid=1, empName='张三', age=1, sex='男', email='123@qq.com', dept=null}

### 2、MyBatis二级缓存

二级缓存是SqlSessionFactory级别的，通过一个SqlSessionFactory创建的SqlSession查询的结果会被缓存，此后若再执行相同的查询语句，结果就会从缓存中取

二级缓存开启的条件

1. 在核心配置文件中，设置全局配置属性cacheEnabled=true，默认为true，不需要设置

2. 在映射文件中设置标签<cache/>

3. 二级缓存必须在SqlSession(一级缓存)关闭或提交之后有效

   ```
   sqlSession1.commit();
   sqlSession1.close();
   ```

4. 查询的数据所转换的实体类类型必须实现序列化的接口

使二级缓存失效的情况

两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

缓存仅仅调用增删改时刷新

### 3、二级缓存的相关配置

* eviction属性：缓存回收策略

  LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。
  FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。
  SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。
  WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。
  默认的是 LRU。

* flushInterval属性：刷新间隔，单位毫秒

  默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新

* size属性：引用数目，正整数

  代表缓存最多可以存储多少个对象，太大容易导致内存溢出

* readOnly属性：只读，true/false

  true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了
  很重要的性能优势。
  false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是
  false。

### 4、MyBatis缓存查询的顺序

* 先查询二级缓存，因为二级缓存可能有其他程序已经查询出来的结果，可以拿来直接使用。
* 如果二级缓存没有命中，再查询一级缓存
* 如果一级缓存也没有命中，则查询数据库
* SqlSession关闭之后，一级缓存中的数据会写入二级缓存

### 5、整合第三方缓存EHCache

1. 添加依赖

   ```xml
   <!-- Mybatis EHCache整合包 -->
   <dependency>
   <groupId>org.mybatis.caches</groupId>
   <artifactId>mybatis-ehcache</artifactId>
   <version>1.2.1</version>
   </dependency>
   <!-- slf4j日志门面的一个具体实现 -->
   <dependency>
   <groupId>ch.qos.logback</groupId>
   <artifactId>logback-classic</artifactId>
   <version>1.2.3</version>
   </dependency>
   ```

2. 各jar包的功能

   | jar包名称       | 作用                            |
   | --------------- | ------------------------------- |
   | mybatis-ehcache | Mybatis和EHCache的整合包        |
   | ehcache         | EHCache核心包                   |
   | slf4j-api       | SLF4J日志门面包                 |
   | logback-classic | 支持SLF4J门面接口的一个具体实现 |

3. 创建EHChache的配置文件ehcache.xml

   ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
   <!-- 磁盘保存路径,将缓存保存到磁盘上 -->
   <diskStore path="D:\atguigu\ehcache"/>
   <defaultCache
   maxElementsInMemory="1000"
   maxElementsOnDisk="10000000"
   eternal="false"
   overflowToDisk="true"
   timeToIdleSeconds="120"
   timeToLiveSeconds="120"
   diskExpiryThreadIntervalSeconds="120"
   memoryStoreEvictionPolicy="LRU">
   </defaultCache>
   </ehcache>
   ```

4. 设置二级缓存的类型

   ```xml
   <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
   ```

5. 加入logback日志

   存在SLF4J时，作为简易日志的log4j将失效，此时我们需要借助SLF4J的具体实现logback来打印日志。
   创建logback的配置文件logback.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration debug="true">
       <!-- 指定日志输出的位置 -->
       <appender name="STDOUT"
                 class="ch.qos.logback.core.ConsoleAppender">
           <encoder>
           <!-- 日志输出的格式 -->
           <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体内容、换行 -->
           <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger]
           [%msg]%n</pattern>
           </encoder>
       </appender>
       <!-- 设置全局日志级别。日志级别按顺序分别是：DEBUG、INFO、WARN、ERROR -->
       <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
       <root level="DEBUG">
           <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
           <appender-ref ref="STDOUT" />
       </root>
       <!-- 根据特殊需求指定局部日志级别 -->
       <logger name="com.atguigu.crowd.mapper" level="DEBUG"/>
   </configuration>
   ```

## 十一、MyBatis的逆向工程

* 正向工程：先创建java实体类，由框架负责根据实体类生成数据库表。Hibernate是支持正向工程的
* 反向工程：先创建数据库表，由框架负责根据数据库表，反向生成如下资源
  * java实体类
  * Mapper接口
  * Mapper映射文件

### 1、创建逆向工程的步骤

#### a、添加依赖和插件

```xml
<packaging>jar</packaging>

<!-- 依赖MyBatis核心包 -->
<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>

    <!-- log4j日志 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
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



<!-- 控制Maven在构建过程中相关配置 -->
<build>
    <!-- 构建过程中用到的插件 -->
    <plugins>
        <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.0</version>
            <!-- 插件的依赖 -->
            <dependencies>
                <!-- 逆向工程的核心依赖 -->
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core</artifactId>
                    <version>1.3.2</version>
                </dependency>
                <!-- 数据库连接池 -->
                <dependency>
                    <groupId>com.mchange</groupId>
                    <artifactId>c3p0</artifactId>
                    <version>0.9.2</version>
                </dependency>
                <!-- MySQL驱动 -->
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>5.1.8</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

#### b、创建MyBatis的核心配置文件

#### c、创建逆向工程的配置文件

文件名必须是：generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
        <!--
            targetRuntime: 执行生成的逆向工程的版本
            MyBatis3Simple: 生成基本的CRUD（清新简洁版）
            MyBatis3: 生成带条件的CRUD（奢华尊享版）
        -->
        <context id="DB2Tables" targetRuntime="MyBatis3Simple">
            <!-- 数据库的连接信息 -->
            <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                            connectionURL="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8"
                            userId="root"
                            password="abc123">
            </jdbcConnection>
            <!-- javaBean的生成策略,就是实体类的生成策略-->
            <javaModelGenerator targetPackage="com.hua.mybatis.pojo" targetProject=".\src\main\java">
                <!--是否生成子包-->
                <property name="enableSubPackages" value="true" />
                <!--去除字符串前后空格，将字段名转为属性，有空格就去掉-->
                <property name="trimStrings" value="true" />
            </javaModelGenerator>
            <!-- SQL映射文件的生成策略 -->
            <sqlMapGenerator targetPackage="com.hua.mybatis.mapper" targetProject=".\src\main\resources">
                <property name="enableSubPackages" value="true" />
            </sqlMapGenerator>
            <!-- Mapper接口的生成策略 -->
            <javaClientGenerator type="XMLMAPPER" targetPackage="com.hua.mybatis.mapper" targetProject=".\src\main\java">
                <property name="enableSubPackages" value="true" />
            </javaClientGenerator>
            <!-- 逆向分析的表 -->
            <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
            <!-- domainObjectName属性指定生成出来的实体类的类名 -->
            <table tableName="t_emp" domainObjectName="Emp"/>
            <table tableName="t_dept" domainObjectName="Dept"/>
        </context>
        </generatorConfiguration>
```

#### d、执行MBG插件的generate

![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/2.png)

### 2、测试奢华尊享版

在配置文件中修改为奢华尊享版

下面的为QBC查询

```java
public class MBGTest {
    @Test
    public void testMBG(){
        try {
            InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
            SqlSession sqlSession = sqlSessionFactory.openSession(true);
            EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
            //查询所有数据
//            List<Emp> list = mapper.selectByExample(null);
//            for(Emp emp : list){
//                System.out.println(emp);
//            }

            //根据条件查询
//            EmpExample empExample = new EmpExample();
//            empExample.createCriteria().andEmpNameEqualTo("张三").andAgeEqualTo(1);
//            empExample.or().andDidIsNotNull();
//            List<Emp> list = mapper.selectByExample(empExample);
//            for(Emp emp : list){
//                System.out.println(emp);
//            }


            //修改
            //普通修改:有null值也会修改
//            mapper.updateByPrimaryKey(new Emp(1,"admin",22,null,"@456.com",2));

            //选择修改：有null值，则该字段不修改
            mapper.updateByPrimaryKeySelective(new Emp(1,"admin",22,null,"@456.com",2));



        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 十二、分页插件

### 1、分页插件使用步骤

#### 1、添加依赖

```xml
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
<dependency>
<groupId>com.github.pagehelper</groupId>
<artifactId>pagehelper</artifactId>
<version>5.2.0</version>
</dependency>
```

#### 2、配置分页插件

在MyBatis的核心配置文件中配置插件

```xml
<plugins>
<!--设置分页插件-->
<plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

### 2、分页插件的使用

a>在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能
		pageNum：当前页的页码
		pageSize：每页显示的条数
b>在查询获取list集合之后，使用PageInfo<T> pageInfo = new PageInfo<>(List<T> list, int
		navigatePages)获取分页相关数据
		list：分页之后的数据
		navigatePages：导航分页的页码数
c>分页相关数据
		PageInfo{
		pageNum=8, pageSize=4, size=2, startRow=29, endRow=30, total=30, pages=8,
		list=Page{count=true, pageNum=8, pageSize=4, startRow=28, endRow=32, total=30,
		pages=8, reasonable=false, pageSizeZero=false},
		prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, hasPreviousPage=true,
		hasNextPage=false, navigatePages=5, navigateFirstPage4, navigateLastPage8,
		navigatepageNums=[4, 5, 6, 7, 8]
		}
常用数据：
		pageNum：当前页的页码
		pageSize：每页显示的条数
		size：当前页显示的真实条数
		total：总记录数
		pages：总页数
		prePage：上一页的页码
		nextPage：下一页的页码

​		isFirstPage/isLastPage：是否为第一页/最后一页
​		hasPreviousPage/hasNextPage：是否存在上一页/下一页
​		navigatePages：导航分页的页码数
​		navigatepageNums：导航分页的页码，[1,2,3,4,5]



# END







