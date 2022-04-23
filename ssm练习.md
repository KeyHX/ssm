# SSM_CRUD

利用SpringMVC + Spring + MyBatis实现CRUD

![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/31.png)

## 一、功能点

1. 分页
2. 数据校验
   * 前端校验 + 后端校验
3. ajax
4. Rest风格的URL

## 二、技术点

1. 基础框架ssm
2. 数据库MySQL
3. 前端框架bootstrap快速搭建简洁美观的界面
4. maven
5. 分页pagehelper
6. 逆向工程MyBatis Generator

## 三、基础环境搭建

### 1、创建maven工程

### 2、引入相关的jar包

1. spring
2. springmvc
3. mybatis
4. 数据库连接池，驱动包
5. 其他(jstl,servlet-api,junit)

### 3、引入bootstrap前端框架

下载bootstrap，在webapp下面创建static，将下载的文件放入static

### 4、编写SSM整合的关键配置文件

* web.xml,spring,springmvc,mybatis，使用mybatis的逆向工程生成对应的bean以及mapper

## 四、配置文件

### 1、配置pom.xml，导入依赖

* spring-webmvc
* spring-jdbc
* spring-aspject
* mybatis
* mybatis-spring
* c3p0
* mysql-connector-java
* servlet-api
* junit

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <packaging>war</packaging>

    <groupId>com.hua</groupId>
    <artifactId>ssm-crud</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <!--引入项目依赖的jar包-->
        <!-- 引入springmvc依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!--spring-jdbc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!--spring 面向切面编程-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.2</version>
        </dependency>

        <!--MyBatis整合spring的适配包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>

        <!--数据库连接池、驱动-->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.0.2</version>
        </dependency>

        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.22</version>
        </dependency>

        <!--jstl,servlet-api,junit-->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

    </dependencies>
</project>
```

### 2、配置web.xml

在webapp下

当服务启动时会首先加载web.xml这个文件，里面包括了对前端控制器、乱码等的配置

* 启动Spring容器
* 配置前端控制器
* 字符编码过滤器
* RestFul风格

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
        <!--1、启动Spring的容器-->
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </context-param>
        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>

        <!--2、配置SpringMVC前端控制器  拦截所有请求-->
        <servlet>
            <servlet-name>dispatherServlet</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>dispatherServlet</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>

        <!--3、字符编码过滤器,一定要放在所有过滤器之前-->
        <filter>
            <filter-name>CharacterEncodingFilter</filter-name>
            <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
            <init-param>
                <param-name>encoding</param-name>
                <param-value>utf-8</param-value>
            </init-param>
            <init-param>
                <param-name>forceRequestEncoding</param-name>
                <param-value>true</param-value>
            </init-param>
            <init-param>
                <param-name>forceResponseEncoding</param-name>
                <param-value>true</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>CharacterEncodingFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping>
    
        <!--RestFul风格，将页面普通的post请求转为指定的delete或put请求-->
        <filter>
            <filter-name>HiddenHttpMethodFilter</filter-name>
            <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
        </filter>
        <filter-mapping>
            <filter-name>HiddenHttpMethodFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping>
</web-app>
```

在webapp下的WEB-INF下创建dispatherServlet-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--SpringMVC的配置文件，包含网站跳转逻辑的控制，配置-->
    <context:component-scan base-package="com.hua.crud" use-default-filters="false">
        <!--只扫描控制器,所有标注了Controller注解的-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"></property>
        <property name="suffix" value=".jsp"></property>
     </bean>

    <!--两个标准配置-->
    <!--将springmvc不能处理的请求交给tomcat-->
    <mvc:default-servlet-handler/>
    <!--能支持springmvc更高级的一些功能，JSR303校验，快捷的ajax，映射动态请求-->
    <mvc:annotation-driven/>
</beans>
```

### 3、配置applicationContext.xml

在resources下

spring配置文件

* 组件扫描：非Controller
* 数据源
* 配置和mysql的整合
* 事务控制

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--组件扫描-->
    <context:component-scan base-package="com.hua">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    <!--Spring配置文件，配置业务逻辑相关的-->
    <!--数据源，事务控制-->
    <context:property-placeholder location="classpath:dbconfig.properties"/>
    <bean id="pooledDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
        <property name="driverClass" value="${jdbc.driverClass}"></property>
        <property name="user" value="${jdbc.user}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>

    <!--配置和mybatis的整合-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!--指定mybatis全局配置文件的位置-->
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
        <property name="dataSource" ref="pooledDataSource"></property>
        <property name="mapperLocations" value="classpath:mapper/*.xml"></property>
    </bean>

    <!--配置扫描器，将mybatis接口的实现加入到ioc容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.hua.crud.dao"></property>
    </bean>

    <!--事务控制-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="pooledDataSource"></property>
    </bean>

    <!--开启基于注解的事务，或者使用xml配置形式的事务(必要主要的都是使用配置)-->
    <aop:config>
    <!--切入点表达式-->
        <aop:pointcut id="txPoint" expression="execution(* com.hua.crud.service..*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPoint"/>
    </aop:config>

    <!--配置事务增强，事务如何切入-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!--所有方法都是事务方法-->
            <tx:method name="*"/>
            <!--以get开始的所有方法-->
            <tx:method name="get*" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!--Spring配置文件的核心点(数据源，与Mybatis的整合，事务控制）-->
</beans>
```

### 4、配置mybatis-config.xml

* 驼峰
* 路径包

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <typeAliases>
        <package name="com.hua.crud.bean"/>
    </typeAliases>
</configuration>
```

## 五、基础模块

### 1、逆向生成Bean文件

1. 引入依赖

   ```xml
               <!-- 逆向生成器，根据表生成bean-->
       <dependency>
           <groupId>org.mybatis.generator</groupId>
           <artifactId>mybatis-generator-core</artifactId>
           <version>1.3.5</version>
       </dependency>
   ```

2. 配置生成文件mbg.xml

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
           <jdbcConnection
                   driverClass="com.mysql.cj.jdbc.Driver"
                   connectionURL="jdbc:mysql://localhost:3306/ssm_crud?serverTimezone=Asia/Shanghai"
                   userId="root"
                   password="abc123">
           </jdbcConnection>
           <!-- javaBean的生成策略,就是实体类的生成策略-->
           <javaModelGenerator targetPackage="com.hua.crud.bean" targetProject=".\src\main\java">
               <!--是否生成子包-->
               <property name="enableSubPackages" value="true" />
               <!--去除字符串前后空格，将字段名转为属性，有空格就去掉-->
               <property name="trimStrings" value="true" />
           </javaModelGenerator>
           <!-- SQL映射文件的生成策略 -->
           <sqlMapGenerator targetPackage="mapper" targetProject=".\src\main\resources">
               <property name="enableSubPackages" value="true" />
           </sqlMapGenerator>
           <!-- Mapper接口的生成策略 -->
           <javaClientGenerator type="XMLMAPPER" targetPackage="com.hua.crud.mapper" targetProject=".\src\main\java">
               <property name="enableSubPackages" value="true" />
           </javaClientGenerator>
           <!-- 逆向分析的表 -->
           <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
           <!-- domainObjectName属性指定生成出来的实体类的类名 -->
           <table tableName="tbl_emp" domainObjectName="Employee"/>
           <table tableName="tbl_dept" domainObjectName="Department"/>
       </context>
   </generatorConfiguration>
   ```

3. 执行文件

   ```java
   package com.hua.crud.test;
   import org.mybatis.generator.api.MyBatisGenerator;
   import org.mybatis.generator.config.Configuration;
   import org.mybatis.generator.config.xml.ConfigurationParser;
   import org.mybatis.generator.exception.XMLParserException;
   import org.mybatis.generator.internal.DefaultShellCallback;
   import java.io.File;
   import java.io.IOException;
   import java.util.ArrayList;
   import java.util.List;
   
   public class MBGTest {
       public static void main(String[] args) throws Exception{
           List<String> warnings = new ArrayList<String>();
           boolean overwrite = true;
           File configFile = new File("mbg.xml");
           ConfigurationParser cp = new ConfigurationParser(warnings);
           Configuration config = cp.parseConfiguration(configFile);
           DefaultShellCallback callback = new DefaultShellCallback(overwrite);
           MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
           myBatisGenerator.generate(null);
       }
   
   }
   ```

   生成的文件都将有注释，如果不想要注释，可以在mbg.xml中加入

   ```xml
   <!--使生成的文件没有注释-->
           <commentGenerator>
               <property name="suppressAllComments" value="true"/>
           </commentGenerator>
   ```

### 2、修改mapper文件

修改mapper文件，是的查询出来的员工带部门信息

* 首先在bean文件中添加

  ```java
  //希望查询员工的同时部门信息也是查询好的
  private Department department;
  
  public Department getDepartment() {
      return department;
  }
  
  public void setDepartment(Department department) {
      this.department = department;
  }
  ```

* 其次在mapper接口中添加

  ```java
  List<Employee> selectByExampleWithDept(EmployeeExample example);
  
  Employee selectByPrimaryKeyWithDept(Integer empId);
  ```

* 在接口配置文件中

  ```xml
  <resultMap id="WithDeptResultMap" type="com.hua.crud.bean.Employee">
      <id column="emp_id" jdbcType="INTEGER" property="empId" />
      <result column="emp_name" jdbcType="VARCHAR" property="empName" />
      <result column="gender" jdbcType="CHAR" property="gender" />
      <result column="email" jdbcType="VARCHAR" property="email" />
      <result column="d_id" jdbcType="INTEGER" property="dId" />
      <!--使用assicoation，指定部门字段的封装-->
      <association property="department" javaType="com.hua.crud.bean.Department">
        <id column="dept_id" property="deptId"/>
        <result column="dept_name" property="deptName"/>
      </association>
    </resultMap>
    <sql id="WithDept_Column_List">
      emp_id, emp_name, gender, email, d_id, dept_id, dept_name
    </sql>
    
  
  <!--
          List<Employee> selectByExampleWithDept(EmployeeExample example);
          Employee selectByPrimaryKeyWithDept(Integer empId);
          查询员工信息同时带上部门信息
          -->
  <select id="selectByExampleWithDept" resultMap="WithDeptResultMap">
      select
      <if test="distinct">
        distinct
      </if>
      <include refid="WithDept_Column_List" />
      from tbl_emp
      left join tbl_dept on tbl_emp.d_id = tbl_dept.dept_id
      <if test="_parameter != null">
        <include refid="Example_Where_Clause" />
      </if>
      <if test="orderByClause != null">
        order by ${orderByClause}
      </if>
  </select>
  <select id="selectByPrimaryKeyWithDept" resultMap="WithDeptResultMap">
    select
    <include refid="WithDept_Column_List" />
    from tbl_emp
    left join tbl_dept on tbl_emp.d_id = tbl_dept.dept_id
    where emp_id = #{empId,jdbcType=INTEGER}
  </select>
  ```

### 3、测试mapper

```java
package com.hua.crud.test;
import com.hua.crud.bean.Department;
import com.hua.crud.bean.Employee;
import com.hua.crud.mapper.DepartmentMapper;
import com.hua.crud.mapper.EmployeeMapper;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import java.util.UUID;


/**
 * Created with IntelliJ IDEA.
 *
 * @Author: KeyHX
 * @Date: 2022/04/22/13:52
 * 推荐Spring的项目就可以使用Spring的单元测试，可以自动注入我们需要的组件
 * 1、导入SpringTest模块
 * 2、@ContextConfiguration指定spring配置文件的位置
 * 3、直接autowired要使用的组件即可
 @RunWith(SpringJUnit4ClassRunner.class),让测试运行于Spring测试环境
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:applicationContext.xml"})
public class MapperTest {
    @Autowired
    DepartmentMapper departmentMapper;

    @Autowired
    EmployeeMapper employeeMapper;

    @Autowired
    SqlSessionTemplate sqlSessionTemplate;
    /**
     * 测试DepartmentMapper
     */
    @Test
    public void testCRUD(){
        System.out.println(departmentMapper);
        //1、插入几个部门
//        departmentMapper.insertSelective(new Department(null,"开发部"));
//        departmentMapper.insertSelective(new Department(null,"测试部"));

        //2、生成员工数据，测试员工插入
//        employeeMapper.insertSelective(new Employee(null,"Jerry","M","Jerry@qq.com",1));

        //3、批量插入多个员工：使用可以执行批量操作的sqlsession
        EmployeeMapper mapper = sqlSessionTemplate.getMapper(EmployeeMapper.class);
        for (int i = 0; i < 1000; i++) {
            String uid = UUID.randomUUID().toString().substring(0, 5) + i;
            mapper.insertSelective(new Employee(null,uid,"M",uid + "@qq.com",1));
        }
    }

}
```

使用SqlSessionTemplate的时候需要配置以下，在SqlSessionTemplate进行配置

```xml
<!--    配置一个可以执行批量的sqlsession-->
    <bean class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"></constructor-arg>
        <constructor-arg name="executorType" value="BATCH"></constructor-arg>
    </bean>
```

## 六、查询操作

实现功能：

1. 访问index.jsp
2. index.jsp页面发出查询员工列表的请求
3. EmployeeController来接受请求，查出员工数据
4. 来到list.jsp页面进行展示

### 1、实现分页功能

1. 引入依赖

   ```xml
   <!--引入分页插件-->
   <dependency>
       <groupId>com.github.pagehelper</groupId>
       <artifactId>pagehelper</artifactId>
       <version>5.0.0</version>
   </dependency>
   ```

2. 在mybatis-config.xml中进行配置

   ```xml
   <!--分页插件-->
   <plugins>
       <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
   </plugins>
   ```

3. EmployeeController

   ```java
   /**
   * Created with IntelliJ IDEA.
    *
    * @Author: KeyHX
    * @Date: 2022/04/22/15:13
    * @Function:处理员工CRUD请求
    */
   @Controller
   public class EmployeeController {
       @Autowired
       EmployeeService employeeService;
       /**
        * 查询员工数据(分页查询)
        * @return
        */
       @RequestMapping("/emps")
       public String getEmps(@RequestParam(value = "pn",defaultValue = "1") Integer pn, Model model){
           //引入 PageHelper 分页插件
           //在查询之前只需要调用，传入页码，以及分页的大小，每页显示5个
           PageHelper.startPage(pn,5);
           //startPage后面紧跟的这个查询就是一个分页查询
           List<Employee> emps = employeeService.getAll();
           //使用pageInfo包装查询后的结果，只需要将pageInfo交给页面就行了
           //它封装了详细的分页信息，包括查询出来的数据,第二个参数为连续显示的页数
           PageInfo page = new PageInfo(emps,5);
           model.addAttribute("pageInfo",page);
           return "list";
       }
   }
   ```

### 2、测试分页功能

```java
/**
 * Created with IntelliJ IDEA.
 *
 * @Author: KeyHX
 * @Date: 2022/04/22/15:50
 * @Function:使用Spring测试CRUD请求的正确性
 */
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
@ContextConfiguration(locations = {"classpath:applicationContext.xml","file:src/main/webapp/WEB-INF/dispatherServlet-servlet.xml"}) //
public class MVCTest {
    //传入Springmvc的ioc
    @Autowired
    WebApplicationContext context;

    //虚拟mvc请求，获取到处理结果
    MockMvc mockMvc;

    @Before
    public void initMokcMvc(){
        mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
    }

    @Test
    public void testPage() throws Exception {
        //模拟请求拿到返回值
        MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/emps").param("pn", "201")).andReturn();

        //请求成功以后，请求域中会后pageInfo
        MockHttpServletRequest request = result.getRequest();
        PageInfo pi = (PageInfo) request.getAttribute("pageInfo");
        System.out.println("当前页码：" + pi.getPageNum());
        System.out.println("总页码：" + pi.getPages());
        System.out.println("总记录数：" + pi.getTotal());
        System.out.println("在页面需要连续显示的页码：");
        int[] nums = pi.getNavigatepageNums();
        for (int num : nums) {
            System.out.print(" " + num);
        }
        System.out.println();
        //获取员工数据
        List<Employee> list = pi.getList();
        for (Employee employee : list) {
            System.out.println("id:" + employee.getEmpId() + "==>" + "name:" + employee.getEmpName());
        }

    }
}
```

## 七、前端页面显示

### 1、前端框架的搭建

views下的list.jsp文件

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>员工列表</title>
    <%
        pageContext.setAttribute("APP_PATH",request.getContextPath());
    %>
    <%--web路径
不以/开始的相对路径，找资源，以当前资源的路径为基准，经常出问题
以/开始的相对路径，找资源，以服务器的路径为准(http://localhost:3306)
--%>
    <%--引入jqery--%>
    <script TYPE="text/javascript" src="${APP_PATH}/static/js/jquery-1.12.4.min.js"></script>
    <!-- 引入样式 -->
    <link rel="stylesheet" href="${APP_PATH}/static/bootstrap-3.4.1-dist/css/bootstrap.min.css">
    <script src="${APP_PATH}/static/bootstrap-3.4.1-dist/js/bootstrap.min.js"></script>
</head>
<body>
    <%--搭建显示页面--%>
    <div class="container">
        <%--标题--%>
        <div class="row">
            <div class="col-md-12">
                <h1>SSM_CRUD</h1>
            </div>
        </div>
        <%--按钮--%>
        <div class="row">
            <div class="row">
                <div class="col-md-4 col-md-offset-8">
                    <button class="btn btn-primary">新增</button>
                    <button class="btn btn-danger">删除</button>
                </div>
            </div>
        </div>
        <%--显示表格数据--%>
        <div class="row">
            <div class="col-md-12">
                <table class="table table-hover">
                    <tr>
                        <th>#</th>
                        <th>empName</th>
                        <th>gender</th>
                        <th>email</th>
                        <th>deptName</th>
                        <th>操作</th>
                    </tr>
                    <tr>
                        <th>1</th>
                        <th>1</th>
                        <th>1</th>
                        <th>1</th>
                        <th>1</th>
                        <th>
                            <button class="btn btn-primary btn-sm">
                                <span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>
                                编辑
                            </button>
                            <button class="btn btn-danger btn-sm">
                                <span class="glyphicon glyphicon-trash" aria-hidden="true"></span>
                                删除
                            </button>
                        </th>
                    </tr>
                </table>
            </div>
        </div>
        <%--显示分页--%>
        <div class="row">
            <%--分页文字信息--%>
            <div class="col-md-6">
                当前记录数
            </div>
            <%--分页条信息--%>
            <div class="col-md-6">
                <nav aria-label="Page navigation">
                    <ul class="pagination">
                        <li><a href="#">首页</a></li>
                        <li>
                            <a href="#" aria-label="Previous">
                                <span aria-hidden="true">&laquo;</span>
                            </a>
                        </li>
                        <li><a href="#">1</a></li>
                        <li><a href="#">2</a></li>
                        <li><a href="#">3</a></li>
                        <li><a href="#">4</a></li>
                        <li><a href="#">5</a></li>
                        <li>
                            <a href="#" aria-label="Next">
                                <span aria-hidden="true">&raquo;</span>
                            </a>
                        </li>
                        <li><a href="#">末页</a></li>
                    </ul>
                </nav>
            </div>
        </div>
    </div>

</body>
</html>
```

使用的是6.1的controller

### 2、数据页面的填充

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%--引入标签库--%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>员工列表</title>
    <%
        pageContext.setAttribute("APP_PATH",request.getContextPath());
    %>
    <%--web路径
不以/开始的相对路径，找资源，以当前资源的路径为基准，经常出问题
以/开始的相对路径，找资源，以服务器的路径为准(http://localhost:3306)
--%>
    <%--引入jqery--%>
    <script TYPE="text/javascript" src="${APP_PATH}/static/js/jquery-1.12.4.min.js"></script>
    <!-- 引入样式 -->
    <link rel="stylesheet" href="${APP_PATH}/static/bootstrap-3.4.1-dist/css/bootstrap.min.css">
    <script src="${APP_PATH}/static/bootstrap-3.4.1-dist/js/bootstrap.min.js"></script>
</head>
<body>
    <%--搭建显示页面--%>
    <div class="container">
        <%--标题--%>
        <div class="row">
            <div class="col-md-12">
                <h1>SSM_CRUD</h1>
            </div>
        </div>
        <%--按钮--%>
        <div class="row">
            <div class="row">
                <div class="col-md-4 col-md-offset-8">
                    <button class="btn btn-primary">新增</button>
                    <button class="btn btn-danger">删除</button>
                </div>
            </div>
        </div>
        <%--显示表格数据--%>
        <div class="row">
            <div class="col-md-12">
                <table class="table table-hover">
                    <tr>
                        <th>#</th>
                        <th>empName</th>
                        <th>gender</th>
                        <th>email</th>
                        <th>deptName</th>
                        <th>操作</th>
                    </tr>
                    <%--要先配置，显示每一页的数据--%>
                    <c:forEach items="${pageInfo.list}" var="emp">
                        <tr>
                            <th>${emp.empId}</th>
                            <th>${emp.empName}</th>
                            <th>${emp.gender=="M"?"男":"女"}</th>
                            <th>${emp.email}</th>
                            <th>${emp.department.deptName}</th>
                            <th>
                                <button class="btn btn-primary btn-sm">
                                    <span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>
                                    编辑
                                </button>
                                <button class="btn btn-danger btn-sm">
                                    <span class="glyphicon glyphicon-trash" aria-hidden="true"></span>
                                    删除
                                </button>
                            </th>
                        </tr>
                    </c:forEach>

                </table>
            </div>
        </div>
        <%--显示分页--%>
        <div class="row">
            <%--分页文字信息--%>
            <div class="col-md-6">
                当前第${pageInfo.pageNum}页,总共${pageInfo.pages}页，总共有${pageInfo.total}条记录
            </div>
            <%--分页条信息--%>
            <div class="col-md-6">
                <nav aria-label="Page navigation">
                    <ul class="pagination">
                        <li><a href="${APP_PATH}/emps?pn=1">首页</a></li>
                        <%--上一页--%>
                        <c:if test="${pageInfo.hasPreviousPage}">
                            <li>
                                <a href="${APP_PATH}/emps?pn=${pageInfo.pageNum-1}" aria-label="Previous">
                                    <span aria-hidden="true">&laquo;</span>
                                </a>
                            </li>
                        </c:if>

                        <%--分页页码--%>
                        <c:forEach items="${pageInfo.navigatepageNums}" var="page_Num">
                            <%--判断当前取出的页面是不是我们当前正在显示的页码--%>
                            <c:if test="${page_Num == pageInfo.pageNum}">
                                <%--高亮显示当前正在显示页码数--%>
                                <li class="active"><a>${page_Num}</a></li>
                            </c:if>
                            <c:if test="${page_Num != pageInfo.pageNum}">
                                <li><a href="${APP_PATH}/emps?pn=${page_Num}">${page_Num}</a></li>
                            </c:if>

                        </c:forEach>

                        <c:if test="${pageInfo.hasNextPage}">
                            <li>
                                <a href="${APP_PATH}/emps?pn=${pageInfo.pageNum + 1}" aria-label="Next">
                                    <span aria-hidden="true">&raquo;</span>
                                </a>
                            </li>
                        </c:if>
                        <li><a href="${APP_PATH}/emps?pn=${pageInfo.pages}">末页</a></li>

                    </ul>
                </nav>
            </div>
        </div>
    </div>

</body>
</html>
```

### 3、使用ajax进行查询

步骤：

1. index.jsp页面直接发送ajax请求进行员工分页数据的查询
2. 服务器将查出的数据，以json字符串的形式返回给浏览器
3. 浏览器收到js字符串，可以使用js对json进行解析，使用js它通过dom增删改改变页面
4. 返回json实现客户端无关性

#### 3.1返回json数据

1. 引入依赖jackson

   ```xml
   <!--        返回json字符串-->
           <dependency>
               <groupId>com.fasterxml.jackson.core</groupId>
               <artifactId>jackson-databind</artifactId>
               <version>2.8.8</version>
           </dependency>
   ```

2. 创建一个类用来返回到浏览器的json

   ```java
   package com.hua.crud.bean;
   
   import org.mybatis.spring.mapper.MapperScannerConfigurer;
   
   import java.util.HashMap;
   import java.util.Map;
   
   /**
    * Created with IntelliJ IDEA.
    *
    * @Author: KeyHX
    * @Date: 2022/04/23/11:05
    * @Function:通用的返回的类
    */
   public class Msg {
       //状态码 100 - 成功  200 - 失败
       private int code;
       //提示信息
       private String msg;
   
       public static Msg success(){
           Msg result = new Msg();
           result.setCode(100);
           result.setMsg("处理成功！");
           return result;
       }
   
       public static Msg fail(){
           Msg result = new Msg();
           result.setCode(200);
           result.setMsg("处理失败！");
           return result;
       }
   
       public Msg add(String key,Object value){
           this.getExtend().put(key,value);
           return this;
       }
   
       public int getCode() {
           return code;
       }
   
       public void setCode(int code) {
           this.code = code;
       }
   
       public String getMsg() {
           return msg;
       }
   
       public void setMsg(String msg) {
           this.msg = msg;
       }
   
       public Map<String, Object> getExtend() {
           return extend;
       }
   
       public void setExtend(Map<String, Object> extend) {
           this.extend = extend;
       }
   
       //用户返回给浏览器的数据
       private Map<String,Object> extend = new HashMap<String,Object>();
   }
   
   ```

#### 3.2 将数据显示到浏览器

```java
/**
 * Created with IntelliJ IDEA.
 *
 * @Author: KeyHX
 * @Date: 2022/04/22/15:13
 * @Function:处理员工CRUD请求
 */
@Slf4j
@Controller
public class EmployeeController {
    @Autowired
    EmployeeService employeeService;

    /**
     * 导入jackson包，负责将对象转换为json字符串
     * @param pn
     * @param model
     * @return
     */
    @RequestMapping("/emps")
    @ResponseBody
    public Msg getEmpWithJson(@RequestParam(value = "pn",defaultValue = "1") Integer pn, Model model){
        //引入 PageHelper 分页插件
        //在查询之前只需要调用，传入页码，以及分页的大小
        PageHelper.startPage(pn,5);
        //startPage后面紧跟的这个查询就是一个分页查询
        List<Employee> emps = employeeService.getAll();
        //使用pageInfo包装查询后的结果，只需要将pageInfo交给页面就行了
        //它封装了详细的分页信息，包括查询出来的数据,第二个参数为连续显示的页数
        PageInfo page = new PageInfo(emps,5);
        return Msg.success().add("pageInfo",page);
    }
    /
}
```

#### 3.3 使用ajax处理json数据

要把前端框架搭好，然后往里面填值

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%--引入标签库--%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>员工列表</title>
    <%
        pageContext.setAttribute("APP_PATH",request.getContextPath());
    %>
    <%--web路径
不以/开始的相对路径，找资源，以当前资源的路径为基准，经常出问题
以/开始的相对路径，找资源，以服务器的路径为准(http://localhost:3306)
--%>
    <%--引入jqery--%>
    <script TYPE="text/javascript" src="${APP_PATH}/static/js/jquery-1.12.4.min.js"></script>
    <!-- 引入样式 -->
    <link rel="stylesheet" href="${APP_PATH}/static/bootstrap-3.4.1-dist/css/bootstrap.min.css">
    <script src="${APP_PATH}/static/bootstrap-3.4.1-dist/js/bootstrap.min.js"></script>
</head>
<body>
<%--搭建显示页面--%>
<div class="container">
    <%--标题--%>
    <div class="row">
        <div class="col-md-12">
            <h1>SSM_CRUD</h1>
        </div>
    </div>
    <%--按钮--%>
    <div class="row">
        <div class="row">
            <div class="col-md-4 col-md-offset-8">
                <button class="btn btn-primary">新增</button>
                <button class="btn btn-danger">删除</button>
            </div>
        </div>
    </div>
    <%--显示表格数据--%>
    <div class="row">
        <div class="col-md-12">
            <table class="table table-hover" id="emps_table">
                <thead>
                    <tr>
                        <th>#</th>
                        <th>empName</th>
                        <th>gender</th>
                        <th>email</th>
                        <th>deptName</th>
                        <th>操作</th>
                    </tr>
                </thead>
                <tbody>

                </tbody>
            </table>
        </div>
    </div>
    <%--显示分页--%>
    <div class="row">
        <%--分页文字信息--%>
        <div class="col-md-6" id="page_info_area">

        </div>
        <%--分页条信息--%>
        <div class="col-md-6" id="page_info_text">

        </div>
    </div>
</div>
<script type="text/javascript">
    //1、页面加载完成以后，直接发送一个ajax请求，要到分页数据
    $(function (){
        //初始化第一页
       to_page(1);
    });

    function to_page(pn) {
        $.ajax({
            url:"${APP_PATH}/emps",
            data:"pn=" + pn,
            type:"get",
            success:function (result){//result是查询到的json信息
                // console.log(result);
                //1、解析并显示员工数据
                build_emps_table(result);
                //2、解析并显示分页信息
                build_page_info(result);
                //3、解析显示分页条数据
                build_page_nav(result)
            }
        });
    }

    function build_emps_table(result){

        //清空表格
        $("#emps_table tbody").empty();

        var emps = result.extend.pageInfo.list;
        //遍历员工数据
        $.each(emps,function (index,item){
            var empIdTd = $("<td></td>").append(item.empId);
            var empNameTd = $("<td></td>").append(item.empName);
            var genderTd = $("<td></td>").append(item.gender == 'M'?"男":"女");
            var emailTd = $("<td></td>").append(item.email);
            var deptNameTd = $("<td></td>").append(item.department.deptName);
            // <button class="btn btn-primary btn-sm">
            // <span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>
            // 编辑
            // </button>
            //参照list.jsp里面的写
            var editBtn = $("<button></button>").addClass("btn btn-primary btn-sm")
                .append($("<span></span>").addClass("glyphicon glyphicon-pencil").append("编辑"));
            var delBtn =$("<button></button>").addClass("btn btn-danger btn-sm")
                .append($("<span></span>").addClass("glyphicon glyphicon-trash").append("删除"));
            var btnTd = $("<td></td>").append(editBtn).append(" ").append(delBtn);
            $("<tr></tr>").append(empIdTd)
                .append(empNameTd)
                .append(genderTd)
                .append(emailTd)
                .append(deptNameTd)
                .append(btnTd)
                .appendTo("#emps_table tbody");
        });
    }

    //解析显示分页信息
    function build_page_info(result){
        //清空分页信息
        $("#page_info_area").empty();

        $("#page_info_area").append("当前第" + result.extend.pageInfo.pageNum + "页" +
            "总共" + result.extend.pageInfo.pages + "页，总共有" + result.extend.pageInfo.total + "条记录")
    }

    //解析显示分页,具体参照bootstrap中发分页组件
    function  build_page_nav(result){
        //清空显示分页，不然会重复显示
        $("#page_info_text").empty();

        var ul = $("<ul></ul>").addClass("pagination");

        //构建首页和上一页
        var firstPageLi = $("<li></li>").append($("<a></a>").append("首页").attr("href","#"));
        var prePageLi = $("<li></li>").append($("<a></a>").append("&laquo;"));

        //当前是第一页的时候，则不能点击首页和前一页
        if(result.extend.pageInfo.hasPreviousPage == false){
            firstPageLi.addClass("disabled");
            prePageLi.addClass("disabled");
        }else{
            //当点击首页和前一页的时候
            firstPageLi.click(function () {
                to_page(1);
            });
            prePageLi.click(function () {
                to_page(result.extend.pageInfo.pageNum - 1);
            });
        }

        var nextPageLi = $("<li></li>").append($("<a></a>").append("&raquo;"));
        var lastPageLi = $("<li></li>").append($("<a></a>").append("末页").attr("href","#"));

        //当前是第一页的时候，则不能点击首页和前一页
        if(result.extend.pageInfo.hasNextPage == false){
            nextPageLi.addClass("disabled");
            lastPageLi.addClass("disabled");
        }else{
            //当点击尾页和下一页的时候
            lastPageLi.click(function (){
                to_page(result.extend.pageInfo.pages);
            });

            nextPageLi.click(function (){
                to_page(result.extend.pageInfo.pageNum + 1);
            });
        }



        //构造添加首页和前一页
        ul.append(firstPageLi).append(prePageLi);

        //遍历添加当前的页码
        $.each(result.extend.pageInfo.navigatepageNums,function (index,item) {//pageInfo中方法，显示当前的五个页码

            var numLi = $("<li></li>").append($("<a></a>").append(item));
            //当前正在显示的页面需要高亮,当前遍历的就是li
            if (result.extend.pageInfo.pageNum == item){
                numLi.addClass("active");
            }
            //点击页码的时候跳转
            numLi.click(function () {
                to_page(item);
            });
            ul.append(numLi);
        });

        //构造添加下一页和尾页
        ul.append(nextPageLi).append(lastPageLi);

        $("#page_info_text").append($("<nav></nav>").append(ul));

    }
</script>

</body>
</html>
```


## 八、新增功能实现

#### 1、新增的逻辑

1. 在index.jsp页面点击新增

2. 弹出新增对话框

3. 去数据库查询部门列表，显示在对话框

4. 用户输入数据完成保存

   
