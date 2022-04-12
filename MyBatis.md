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

注意：在所有的查询的接口配置文件中需要添加属性resultType或resultMap

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

${} 的本质就是字符串的拼接，#{} 的本质就是占位符赋值

${}使用字符串的方式拼接sql，若为字符串类型或者日期类型的字段进行赋值时，需要手动加单引号

#{}使用占位符的方式拼接sql，此时为字符串类型或者日期类型的字段进行赋值时，可以自动添加单引号

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
    select * from t_user where username = '${username}'
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
