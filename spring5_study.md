# Spring5

## 一、事务

### 1.事务概念

​			是数据库操作最基本单元，逻辑上一组操作，要么都成功，如果有一个失败则全部失败，比如银行转账。

### 2.事务四个特性（ACID）

①原子性：过程不可分割

②一致性：操作前后总量不变

③隔离性：多个事务之间不会产生影响

④持久性：当事务提交之后表中数据发生变化

### 3.事务操作思路图

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E4%BA%8B%E5%8A%A1%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B.png)

### 4.准备工作

①创建数据库表

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%A1%A8.png)

②创建Service和Dao，完成对象的创建和注入

​		其中Service中注入Dao，在dao中注入JdbcTemplate，在JdbcTemplate中注入DataSource

​		Service

```
@Service
public class UserService {
    //注入Dao
    @Autowired
    private UserDao userDao;

}
```

​		UserDaoImpl

```
@Repository
public class UserDaoImpl {
    @Autowired
    private JdbcTemplate jdbcTemplate;

}
```

③在dao中创建两个方法：多钱和少钱，在service创建转账方法

​		dao中

```
//多钱
@Override
public void addMoney() {
    String sql = "update t_account set money=money+? where username=?";
    jdbcTemplate.update(sql,100,"marry");
}

//少钱
@Override
public void reduceMoney() {
    String sql = "update t_account set money=money-? where username=?";
    jdbcTemplate.update(sql,100,"lucy");
}
```

​		Service中代码

```
//转账方法
public void accountMoney(){
    //lucy少100
    userDao.reduceMoney();
    //marry多100
    userDao.addMoney();
}
```

​    **当有下面的异常时，一个人钱减少而另一个人钱没有增加**

```
//转账方法
public void accountMoney(){
    //lucy少100
    userDao.reduceMoney();
    //出现异常
    int x = 10 / 0;
    //marry多100
    userDao.addMoney();
}
```

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E5%BC%82%E5%B8%B8%E8%A1%A8.png)

​	上面问题使用事务进行解决



### 5.事务操作

①  事务一般添加JavaEE三层结构里面的Service层（业务逻辑层）

②  在Spring进行事务管理操作方式有两种：编程式事务管理和声明式事务管理（使用）

③  编程式:一步一步写代码

​			![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E7%BC%96%E7%A8%8B%E5%BC%8F%E4%BA%8B%E5%8A%A1%E7%AE%A1%E7%90%86.png)

<p style="color:red">④   声明式事务管理有两种方式：基于注解方式(使用)和基于xml配置文件方式，底层使用AOP原理</p>

⑤  Spring提供一个接口，PlatformTransactionManager代表事务管理器，这个接口针对不同的框架提供不同的实现类



### 6.声明式事务管理：使用注解方式

①  在Spring配置文件中配置事务管理器

```ABAP
<!--创建事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!--注入数据源-->
    <property name="dataSource" ref="dataSource"></property>//引用的是jdbc数据源，未copy过来
</bean>
```

②  在spring配置文件，开启事务注解

​		首先引入名称空间tx

```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                            http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
```

​		

​		再开启事务注解

```
<!--开启事务注解-->
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```

​		

​		在service类上面（或者service类里面方法上面）添加事务注解

​		@Transactional 此注解可以添加到类上面，也可以添加到方法上面

​		如果把这个注解添加到类上面，这个类里面所有的方法都添加了事务

​		如果把这个注解添加到方法上面，为这个方法添加事务

```
@Service
@Transactional
public class UserService {
    //注入Dao
    @Autowired
    private UserDao userDao;

    //转账方法
    public void accountMoney(){
            //lucy少100
            userDao.reduceMoney();
            //marry多100
            userDao.addMoney();
    }
}
```

### 7.**声明式事务管理参数配置**

①  在service类上面添加注解@Transactional，在这个注解里面可以配置事务相关参数

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E4%BA%8B%E5%8A%A1%E5%8F%82%E6%95%B0.png)

②  propagation：事务传播行为

​		多事务方法直接进行调用，这个过程中事务是如何进行管理的

​		![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E4%BA%8B%E5%8A%A1%E4%BC%A0%E6%92%AD%E8%A1%8C%E4%B8%BA.png)

```
@Service
@Transactional(propagation = Propagation.REQUIRED)
```

③  ioslation：事务隔离级别

​	事务有隔离性：多事务操作之间不会产生影响。如果不考虑事务的隔离性，将会产生三种读问题：脏读、不可重复读、虚（幻）读

​	

​	**脏读：一个未提交的事务读取到另一个未提交事务的数据**（问题有点大）

​	![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E8%84%8F%E8%AF%BB.png)

​		两个事务进行处理的时候，岳不群首先修改了表里面的5000，东方不败刚好读取到了岳不群修改后的60000，而这时候岳不群进行了回滚，表中数据变回了五千，则东方不败读取到的数据错误



​	**不可重复读：一个未提交的事务读取到另一个提交事务修改的数据**（问题不大）

​	![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E4%B8%8D%E5%8F%AF%E9%87%8D%E5%A4%8D%E8%AF%BB.png)

​			东方不败还未提交事务却能够读取到岳不群提交的事务的数据



​	**虚读：一个未提交的事务读取到另一个提交事务添加的数据**（和不可重复读差不多）



​	**通过事务的隔离级别，解决上述问题**

​	![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E4%BA%8B%E5%8A%A1%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB.png)

```
@Service
@Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.REPEATABLE_READ)
//不设置默认使用的是可重复读
```

④  timeout：超时时间

​		事务需要在一定时间内进行提交，不提交就进行回滚

​		默认值是-1，表示不超时，设置的时间以秒为单位进行计算



⑤  readOnly：是否只读

​		读：查询操作，写：添加修改删除操作

​		readOnly默认值false，表示都可以。为true时，只可以查



⑥  rollbackFor：回滚

​		设置出现哪些异常进行事务回滚



⑦  norollbackFor：不回滚

​		设置出现哪些异常不进行事务回滚



### 8.**声明事务管理：使用xml方式**

①  在spring配置文件中进行配置

​	第一步 配置事务管理器

​	第二步 配置通知

​	第三步 配置切入点和切面

```
 <!--第一步 创建事务管理器-->
 <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
     <!--注入数据源-->
     <property name="dataSource" ref="dataSource"></property>
 </bean>

<!--第二步 配置通知-->
 <tx:advice id="txadvice">
     <!--配置事务参数-->
     <tx:attributes>
         <!--指定哪种规则的方法上面添加事务-->
         <tx:method name="account*" propagation="REQUIRED" isolation="REPEATABLE_READ"/>
     </tx:attributes>
 </tx:advice>

 <!--第三步 配置切入点和切面-->
 <aop:config>
     <!--配置切入点-->
     <aop:pointcut id="pt" expression="execution(* com.hua.service.UserService.*(..))"/>
     <!--配置切面-->
     <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
 </aop:config>
```



### 9.**完全注解声明式事务管理**

创建配置类，使用配置类替代xml配置文件

```
@Configuration//配置类
@ComponentScan(basePackages = "com.hua")//组件扫描
@EnableTransactionManagement//开启事务注解
public class TxConfig {
    //创建数据库连接池
    @Bean//表示产生一个Bean对象
    public DruidDataSource getDruidDataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/user_db?characterEncoding=utf8");
        dataSource.setUsername("root");
        dataSource.setPassword("abc123");
        return dataSource;
    }

    //创建JdbcTemplate
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource){//到IOC容器中根据类型找到dataSource
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        //注入dataSource
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    //创建事务管理器
    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```





## 二、Spring5 框架新功能

### 1.Spring5框架代码基于java8，运行时兼容JDK9，许多不建议使用的类和方法在代码库中删除

### 2.Spring5框架自带了通用的日志封装

Spring5已经移除Log4jConfigListener,官方建议使用Log4j2,Spring5框架整合Log4j2，Log4j2会输出初始化、翻转和其他内部操作的详细信息

Log4j2的使用

第一步  引入jar包

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/Log4j2%E7%9A%84jar%E5%8C%85.png)

第二步  创建log4j2.xml配置文件（名字固定）

```
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->(越往右优先级别越高，显示内容越多，能够包含优先级别低的内容)
<!--Configuration后面的status用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4j2内部各种详细输出-->
<configuration status="INFO">
    <!--先定义所有的appender-->
    <appenders>
        <!--输出日志信息到控制台-->
        <console name="Console" target="SYSTEM_OUT">
            <!--控制日志输出的格式-->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </console>
    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <!--root：用于指定项目的根日志，如果没有单独指定Logger，则会使用root作为默认的日志输出-->
    <loggers>
        <root level="info">
            <appender-ref ref="Console"/>
        </root>
    </loggers>
</configuration>
```

### 3. Spring5框架核心容器部分支持@Nullable注解

@Nullable注解可以使用在方法，属性，参数里面，表示方法返回可以为空，属性值可以为空，参数值可以为空

①   注解在方法上面

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%B3%A8%E8%A7%A3%E5%9C%A8%E6%96%B9%E6%B3%95%E4%B8%8A%E9%9D%A2.png)

②   注解在属性上面

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%B3%A8%E8%A7%A3%E5%9C%A8%E5%B1%9E%E6%80%A7.png)

③  注解在参数前面

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%B3%A8%E8%A7%A3%E5%9C%A8%E6%96%B9%E6%B3%95%E5%8F%82%E6%95%B0%E9%87%8C.png)

### 4.Spring5支持整合JUnit5

①   整合JUnit4

第一步 引入依赖

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/jar1.png)

第二步  创建测试类，使用注解方式完成

```
@RunWith(SpringJUnit4ClassRunner.class)//单元测试框架
@ContextConfiguration("classpath:bean1.xml")//加载配置文件
public class JTest4 {
    @Autowired
    private UserService userService;//自动注入属性
    @Test
    public void test1(){
        userService.accountMoney();
    }
}
```

前两行相当于

```
ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
UserService userService = context.getBean("userService", UserService.class);
```

②  整合JUnit5

第一步 引入JUnit5依赖

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E5%BC%95%E5%85%A5JUnit5%E4%BE%9D%E8%B5%96.png)

第二步 创建测试类，使用注解完成

```
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:bean1.xml")
public class JTest5 {
    @Autowired
    private UserService userService;
    @Test
    public void test1(){
        userService.accountMoney();
    }
}
```

```
//@ExtendWith(SpringExtension.class)
//@ContextConfiguration("classpath:bean1.xml")
@SpringJUnitConfig(locations = "classpath:bean1.xml")//这一句整合了上面两句
public class JTest5 {
    @Autowired
    private UserService userService;
    @Test
    public void test1(){
        userService.accountMoney();
    }
}
```

### 5.Spring5框架新功能：先去学SpringMvc，P53

