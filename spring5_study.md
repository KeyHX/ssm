# Spring5

![](C:\Users\AIERXUAN\Desktop\picture\DD\2.jpg)

![](C:\Users\AIERXUAN\Desktop\picture\DD\1.jpg)

## 一、Spring框架

### 1、学习内容

1. 框架概述
2. IOC容器
3. Aop
4. JdbcTemplate
5. 事务管理
6. Spring5新特性

### 2、框架概述

1. spring是轻量级的开源的javaEE框架，可以解决企业应用开发的复杂性

2. Spring有两个核心部分：

   IOC：控制反转，把创建对象的过程交给Spring进行管理

   Aop：面向切面，不修改源代码进行功能的增强

3. Spring特点：方便解耦，简化开发

### 3、简单操作案例

1. https://repo.spring.io/release/org/springframework/spring/ 下载框架

2. 在idea创建java工程

3. 导入Spring相关jar包

   ![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E7%9B%B8%E5%85%B3jar%E5%8C%85.png)

4. 创建普通类，在普通类里面创建普通方法

   ```java
   public void add(){
       System.out.println("add...");
   }
   ```

5. 创建Spring配置文件，在配置文件创建对象

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:p="http://www.springframework.org/schema/p"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <!--配置User类的创建-->
       <bean id="user" class="com.hua.spring5.User"></bean>
   </beans>
   ```

6. 编写代码进行测试

   ```java
   @Test
   public void testAdd(){
       //此处的bean1就是上面创建的配置文件
       ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
       //user就是配置文件里面的id
       UserService userService = context.getBean("user", User.class);
       //调用创建对象的方法
       userService.add();
   }
   ```

   注：主要就是四步

   第一步：导包

   第二步：创建类

   第三步：写配置文件

   第四步：测试

   

## 二、IOC的概念

### 1、什么是IOC以及底层原理

IOC：控制反转，将对象创建和对象之间的调用过程，交给Spring进行管理，目的是为了降低耦合度。

IOC底层原理：xml解析、工厂模式、反射

IOC底层原理图

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/ioc%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86%E5%9B%BE.png)

### 2、IOC容器

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂（容器就是创建对象和注入属性的空间）

2. Spring提供IOC容器的两种实现方式：(两个接口)

​	(1)BeanFactory：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用(加载配置文件时候不会创建对象，在获取对象的时候才去创建对象)

​	(2)ApplicationContext：BeanFactory接口的子接口，一般开发人员进行使用(在加载配置文件的时候就会创建对象，比较好)

### 3、IOC操作Bean管理

Ⅰ、什么是Bean管理？指的是两个操作为：Spring创建对象和Spring注入属性

Ⅱ、Bean管理操作有两种方式：基于xml配置文件方式实现和基于注解的方式实现

## 三、基于xml的方式进行Bean管理

### 1、基于xml方式创建对象

```xml
<bean id="user" class="com.hua.spring5.User"></bean>
```

id属性：唯一标识

class属性：要创建的类的全路径

**创建对象的时候，默认执行的是类的空参构造器(类要有空参构造器，否则报错)**

```java
    //此处的bean1就是上面创建的配置文件
    ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
    //user就是配置文件里面的id
    UserService userService = context.getBean("user", User.class);
    //调用创建对象的方法
    userService.add();
```

### 2、基于xml方式注入属性

#### 1、方法一：使用set方法进行注入

​	(1)创建类，定义属性和对应的set方法(一定要有set方法)

```java
public class Book {
    //演示使用set方法进行属性注入
    private String bname;
    private String bauthor;
    private String address;
    //set方法注入
    public void setBname(String bname) {
        this.bname = bname;
    }

    public void setBauthor(String bauthor) {
        this.bauthor = bauthor;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public void testDemo(){
        System.out.println(bname + ":" + bauthor + ":" + address);
    }
}
```

​	(2)在spring配置文件配置对象创建，配置属性注入

```xml
<!--使用set方法注入属性-->
<bean id="book" class="com.hua.spring5.Book">
    <!--使用property完成属性的注入
        name：类里面属性的名称
        value:向属性注入的值
    -->
    <property name="bname" value="章鱼"></property>
    <property name="bauthor" value="皮卡丘"></property>

    <!--设置地址值为空值-->
    <!--<property name="address">-->
        <!--<null/>-->
    <!--</property>-->


    <!--属性值中包含特殊符号
        1、把括号进行转义
        2、把带特殊符号内容写到CDATA
    -->
    <property name="address">
        <value>
            <![CDATA[<<南京>>]]>
        </value>
    </property>
</bean>
```

#### 2、方法二： 使用有参构造进行注入

​	(1)创建类，定义属性，创建属性对应有参数构造方法

```java
public class Orders {
    private String name;
    private String address;

    //创建有参构造
    public Orders(String name, String address) {
        this.name = name;
        this.address = address;
    }
    public void testDemo(){
        System.out.println(address + ":" + name);
    }
}
```

​	(2)在spring配置文件中进行配置

```xml
<!--有参构造注入属性-->
<bean id="orders" class="com.hua.spring5.Orders">
    <constructor-arg name="name" value="apple"></constructor-arg>
    <constructor-arg name="address" value="china"></constructor-arg>
</bean>
```

#### p名称空间注入

p名称空间注入：**P名称空间注入走的也是set方法**, 官方目的是简化set注入的property标签的写法，可以简化基于xml配置方式

第一步  添加p名称空间在配置文件中

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
```

第二步 进行属性注入

```xml
<bean id="book" class="com.hua.spring5.Book" p:bname="葫芦娃" p:bauthor="金刚金" ></bean>
```

### 3、xml注入其他类型的属性

#### 1、字面量

(1)null值

```xml
<property name="address">
    <null/>
</property>
```

(2)属性值包含特殊符号

```xml
  <property name="address">
        <value>
            <![CDATA[<<南京>>]]>
        </value>
 </property>
```

#### 2、注入属性---外部bean

（1）创建两个类 service 类和 dao 类 

（2）在 service 调用 dao 里面的方法

```java
public class UserDaoImpl1 implements UserDao1{
    @Override
    public void update() {
        System.out.println("I am update in UserDaoImpl");
    }
}
```

```java
public class UserService1 {

    //创建UserDao类型属性，生成set方法,用来在配置文件中进行属性的注入
    private UserDao1 userDao1;
    public void setUserDao1(UserDao1 userDao1) {
        this.userDao1 = userDao1;
    }
    public void add(){
        System.out.println("I am add method in service");
        userDao1.update();
    }
}
```

```xml
<!--service 和 dao对象的创建-->
<bean id="userService" class="com.hua.service.UserService1">
    <!--
        注入userDao对象
        name  是属性名称
        ref   创建Userdao对象bean标签的id值
    -->
    <property name="userDao1" ref="userDaoImpl"></property>
</bean>
<bean id="userDaoImpl" class="com.hua.dao.UserDaoImpl1"></bean>
```

#### 3、注入属性---内部bean

​	（1）一对多关系：部门和员工，一个部门有多个员工，一个员工属于一个部门，部门是一，员工是多

​    （2）在实体类之间表示一对多关系，员工表示所属部门，使用对象类型属性进行表示，在多的里面创建一的对象

```java
//部门类
public class Dept {
    private String dname;

    public void setDname(String dname) {
        this.dname = dname;
    }

    @Override
    public String toString() {
        return "Dept{" +
                "dname='" + dname + '\'' +
                '}';
    }
}

//员工类
public class Emp {
    private String ename;
    private String gender;
    //员工属于某一个部分，使用对象形式表示
    private Dept dept;

    //生成dept的get方法
    public Dept getDept() {
        return dept;
    }

    public void setDept(Dept dept) {
        this.dept = dept;
    }

    public void setEname(String ename) {
        this.ename = ename;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public void add(){
        System.out.println(ename + "::" + gender + "::" + dept);
    }
}

```

​	（3）在 spring 配置文件中进行配置

```xml
<!--内部bean-->
<bean id="emp" class="com.hua.bean.Emp">
    <!--设置两个普通属性-->
    <property name="ename" value="章鱼"></property>
    <property name="gender" value="女"></property>
    <!--设置对象类型属性-->
    <property name="dept">
        <bean id="dept" class="com.hua.bean.Dept">
            <property name="dname" value="保安部"></property>
        </bean>
    </property>
</bean>
```

#### 4、注入属性---级联赋值

1. 第一种写法

   ```xml
       <!--级联赋值-->
       <bean id="emp" class="com.hua.bean.Emp">
           <!--设置两个普通属性-->
           <property name="ename" value="章鱼"></property>
           <property name="gender" value="女"></property>
           <!--级联赋值-->
           <property name="dept" ref="dept"></property>
       </bean>
       <bean id="dept" class="com.hua.bean.Dept">
           <property name="dname" value="财务部"></property>
       </bean>
   </beans>
   ```

 2、第二种写法

```xml
<!--级联赋值-->
<bean id="emp" class="com.atguigu.spring5.bean.Emp">
    <!--设置两个普通属性-->
    <property name="ename" value="lucy"></property>
    <property name="gender" value="女"></property>
    <!--级联赋值-->
    <property name="dept" ref="dept"></property>
    <property name="dept.dname" value="技术部"></property>
</bean>
<bean id="dept" class="com.hua.spring5.bean.Dept">
    <property name="dname" value="财务部"></property>
</bean>
```

### 4、IOC操作Bean管理（xml注入集合属性）

#### 1.注入数组、集合、map、set类型属性

（1） 创建类，定义数组、list、map、set类型属性，生成对应set方法

```java
public class Stu {
    //1 数组类型属性
    private String[] courses;
    //2 list集合类型属性
    private List<String> list;
    //3 map集合类型属性
    private Map<String,String> maps;
    //4 set集合类型属性
    private Set<String> sets;

    //学生所学多门课程
    private List<Course> courseList;
    public void setCourseList(List<Course> courseList) {
        this.courseList = courseList;
    }

    public void setSets(Set<String> sets) {
        this.sets = sets;
    }
    public void setCourses(String[] courses) {
        this.courses = courses;
    }
    public void setList(List<String> list) {
        this.list = list;
    }
    public void setMaps(Map<String, String> maps) {
        this.maps = maps;
    }

    public void test() {
        System.out.println(Arrays.toString(courses));
        System.out.println(list);
        System.out.println(maps);
        System.out.println(sets);
        System.out.println(courseList);
    }
}
```

​	（2）在配置文件中进行配置

```xml
<!--1 集合类型属性注入-->
<bean id="stu" class="com.hua.spring5.collectiontype.Stu">
    <!--数组类型属性注入-->
    <property name="courses">
        <array>
            <value>java课程</value>
            <value>数据库课程</value>
        </array>
    </property>
    <!--list类型属性注入-->
    <property name="list">
        <list>
            <value>张三</value>
            <value>小三</value>
        </list>
    </property>
    <!--map类型属性注入-->
    <property name="maps">
        <map>
            <entry key="JAVA" value="java"></entry>
            <entry key="PHP" value="php"></entry>
        </map>
    </property>
    <!--set类型属性注入-->
    <property name="sets">
        <set>
            <value>MySQL</value>
            <value>Redis</value>
        </set>
    </property>
    <!--注入list集合类型，值是对象-->
    <property name="courseList">
        <list>
            <ref bean="course1"></ref>
            <ref bean="course2"></ref>
        </list>
    </property>
</bean>
<!--创建多个course对象-->
<bean id="course1" class="com.hua.spring5.collectiontype.Course">
    <property name="cname" value="Spring5框架"></property>
</bean>
<bean id="course2" class="com.hua.spring5.collectiontype.Course">
    <property name="cname" value="MyBatis框架"></property>
</bean>
```

#### 2、使用util标签完成list集合注入提取

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

    <!--1 提取list集合类型属性注入-->
    <util:list id="bookList">
        <value>易筋经</value>
        <value>九阴真经</value>
        <value>九阳神功</value>
    </util:list>

    <!--2 提取list集合类型属性注入使用-->
    <bean id="book" class="com.hua.spring5.collectiontype.Book" scope="prototype">
        <property name="list" ref="bookList"></property>
    </bean>
</beans>
```

### 5、IOC操作Bean管理（FactoryBean）

1. Spring有两种类型bean，一种普通bean，另一种工厂bean（FactoryBean）

2. 普通bean：在配置文件中定义bean类型就是返回类型

3. 工厂bean：在配置文件定义bean类型可以和返回类型不一样

   第一步  创建类，让这个类作为工厂bean，实现接口FactoryBean

   第二步 重写接口里面的方法，在实现的方法中定义返回的bean类型

```java
public class MyBean implements FactoryBean<Course> {

    //定义返回bean
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

    <bean id="myBean" class="com.hua.spring5.factorybean.MyBean">
    </bean>
</beans>
```

```java
@Test public void test3() { 
	ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml"); 
	Course course = context.getBean("myBean", Course.class); 
	System.out.println(course); 
}
```





### 6、IOC操作bean管理 （bean作用域）

1. 在Spring里面，设置创建bean实例是单实例还是多实例

2. 在Spring里面，默认情况下，bean是单实例对象

   ![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E5%8D%95%E5%AE%9E%E4%BE%8B.png)

3. 如何设置单实例还是多实例

   (1)在spring配置文件bean标签里面有属性(scope)用于设置单实例还是多实例

   (2)scope属性值：默认值singleton，表示单实例对象。prototype，表示多实例对象。**设置属性值为singleton时，加载spring配置文件时候就会创建单实例对象。设置为prototype时候，不是在加载spring配置文件时候创建对象，在调用getBean方法时创建多实例对象。**

   ![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E5%A4%9A%E5%AE%9E%E4%BE%8B.png)

### 7、IOC操作Bean管理（Bean的生命周期）

1. 生命周期：从对象的创建到对象的销毁

2. bean生命周期

   (1) 通过构造器创建bean实例（无参构造）

   (2) 为bean的属性设置值和对其他bean引用(调用set方法)

   (3) 调用bean的初始化方法（需要配置初始化方法）

   (4) bean可以使用（获取到了对象）

   (5) 当容器关闭时候，调用bean的销毁的方法（需要配置销毁的方法）

3. 演示bean生命周期

   ```java
   public class Orders {
       //无参数构造
       public Orders() {
           System.out.println("第一步 执行无参数构造创建bean实例");
       }
   
       private String oname;
       public void setOname(String oname) {
           this.oname = oname;
           System.out.println("第二步 调用set方法设置属性值");
       }
   
       //创建执行的初始化的方法
       public void initMethod() {
           System.out.println("第三步 执行初始化的方法");
       }
   
       //创建执行的销毁的方法
       public void destroyMethod() {
           System.out.println("第五步 执行销毁的方法");
       }
   }
   ```

   ```java
       @Test
       public void testBean3() {
   //        ApplicationContext context =
   //                new ClassPathXmlApplicationContext("bean4.xml");
           ClassPathXmlApplicationContext context =
                   new ClassPathXmlApplicationContext("bean4.xml");
           Orders orders = context.getBean("orders", Orders.class);
           System.out.println("第四步 获取创建bean实例对象");
           System.out.println(orders);
   
           //手动让bean实例销毁
           context.close();
       }
   ```

   ```xml
   <bean id="orders" class="com.atguigu.spring5.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">
       <property name="oname" value="手机"></property>
   </bean>
   ```

   ![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%89%A7%E8%A1%8C%E6%AD%A5%E9%AA%A41.png)

4. bean的后置处理器，bean生命周期有七步

   (1) 通过构造器创建bean实例（无参构造）

   (2) 为bean的属性设置值和对其他bean引用(调用set方法)

   (3) 把bean实例传递bean后置处理器的方法postProcessBeforeInitialization

   (4) 调用bean的初始化方法（需要配置初始化方法）

   (5) 把bean实例传递bean的后置处理器方法postProcessAfterInitialization

   (5) bean可以使用（获取到了对象）

   (6) 当容器关闭时候，调用bean的销毁的方法（需要配置销毁的方法）

5. 演示七步

   (1) 创建类，实现接口BeanPostProcessor，创建后置处理器

   ```java
   public class MyBeanPost implements BeanPostProcessor {
       @Override
       public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
           System.out.println("在初始化之前执行的方法");
           return bean;
       }
       @Override
       public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
           System.out.println("在初始化之后执行的方法");
           return bean;
       }
   }
   ```

   ```xml
   <!--配置后置处理器-->
   <bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>
   ```

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%89%A7%E8%A1%8C%E6%AD%A5%E9%AA%A42.png)

### 8、IOC操作Bean管理(xml自动装配)

1. 自动装配定义：根据指定装配规则(属性名称或者属性类型)，Spring自动将匹配的属性值进行注入

2. 演示自动装配：根据属性名称和属性类型自动注入

   * 根据属性名称自动注入
   
     ```xml
     <!--实现自动装配
         bean标签属性autowire，配置自动装配
         autowire属性常用两个值：
             byName根据属性名称注入 ，注入值bean的id值和类属性名称一样
             byType根据属性类型注入
     -->
     <bean id="emp" class="com.hua.spring5.autowire.Emp" autowire="byType">
         <!--<property name="dept" ref="dept"></property>-->
     </bean>
     <bean id="dept" class="com.hua.spring5.autowire.Dept"></bean>
     ```
   
   * 根据属性类型自动注入
   
     ```xml
     <bean id="emp" class="com.hua.spring5.autowire.Emp" autowire="byType"> 
         <!--<property name="dept" ref="dept"></property>--> 
     </bean> 
     <bean id="dept" class="com.hua.spring5.autowire.Dept"></bean>
     ```
   
     

### 9、IOC操作Bean管理(外部属性文件)

1. 直接配置数据库信息

   ```xml
    <!--直接配置连接池-->
   <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
   ```

2. 引入外部属性文件配置数据库连接池

   (1)创建外部属性文件，properties格式文件，写数据库信息

   ```properties
   prop.driverClass=com.mysql.jdbc.Driver
   prop.url=jdbc:mysql://localhost:3306/userDb
   prop.userName=root
   prop.password=root
   ```

   (2) 把外部properties属性文件引入到spring配置文件中

   * 引入context名称空间

   * 引入外部属性文件

     ```xml
     <!--引入外部属性文件-->
     <context:property-placeholder location="classpath:jdbc.properties"/>
     
     <!--配置连接池-->
     <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
         <property name="driverClassName" value="${prop.driverClass}"></property>
         <property name="url" value="${prop.url}"></property>
         <property name="username" value="${prop.userName}"></property>
         <property name="password" value="${prop.password}"></property>
     </bean>
     ```

## 四、基于注解进行Bean管理

### 1、注解介绍

1.  注解：是代码的特殊标记，作用在类、方法、属性上面，目的是为了简化xml配置

2. Spring针对Beaen管理中**创建对象**提供的注解

   @Component(web层)、@Service(业务逻辑层)、@Controller(控制层)、@Respository(持久层)

### 2、基于注解方式实现创建对象步骤

1. 引入依赖

   ![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%B3%A8%E8%A7%A3jar%E5%8C%85.png)

2. 开启组件扫描

   ```xml
   <!--
       开启组件扫描
       1 如果扫描多个包，多个包之间使用逗号隔开
       2 扫描包的上层目录
   -->
   <context:component-scan base-package="com.hua"/>
   ```

3. 创建类，在类的上面添加注解

   ```java
   @Component(value = "userService") //<bean id="userService" class=".."/>
   public class UserService {
    public void add() {
    System.out.println("service add.......");
    } 
   }
   
   //开启组件扫描的细节
    <!--示例一
           use-default-filters="false" 表示现在不使用默认的filter,自己配置filter
           context:include-filter,设置扫描哪些内容
       -->
   
       <!--<context:component-scan base-package="com.hua" use-default-filters="false">-->
           <!--<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>-->
       <!--</context:component-scan>-->
   
       <!--示例二
           下面配置扫描包的所有内容
           context:exclude-filter：设置哪些内容不扫描
       -->
       <!--<context:component-scan base-package="com.hua">-->
           <!--<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>-->
       <!--</context:component-scan>-->
   ```

### 3、基于注解方式实现属性注入

 1. @Autowired：根据属性类型进行自动注入

    第一步  创建Service和dao对象，在service和dao类中添加创建对象注解

    第二步 在service注入dao对象，在service添加dao类属性，在属性上面使用注解

    ```java
    @Service
    public class UserService {
     //定义 dao 类型属性
     //不需要添加 set 方法
     //添加注入属性注解
     @Autowired 
     private UserDao userDao;
     public void add() {
     System.out.println("service add.......");
     userDao.add();
     } }
    ```

 2. @Qualifier：根据名称进行注入，要与上面@Autowired一起使用

    ```java
    //定义 dao 类型属性
    //不需要添加 set 方法
    //添加注入属性注解
    @Autowired //根据类型进行注入
    @Qualifier(value = "userDaoImpl1") //根据名称进行注入,接口的某一个实现类
    private UserDao userDao;
    ```

 3. @Resource：可以根据类型注入，可以根据名称注入

    ```java
    //@Resource //根据类型进行注入
    @Resource(name = "userDaoImpl1") //根据名称进行注入
    private UserDao userDao;
    ```

 4. @Value：注入普通类型属性

    ```java
    @Value(value = "abc")
    private String name;
    ```

### 4、完全注解开发

1. 创建配置类，替代xml配置文件

   ```java
   @Configuration //作为配置类，替代 xml 配置文件
   //开启组件扫描
   @ComponentScan(basePackages = {"com.atguigu"})
   public class SpringConfig {
   }
   ```

2. 编写测试类

   ```java
   public void testService2() {
    //加载配置类
    ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
    UserService userService = context.getBean("userService", UserService.class);
    System.out.println(userService);
    userService.add();
   }
   ```

## 五、AOP

### 1、AOP的概念

aop是面向切面编程，利用aop可以对业务逻辑的各个部分进行隔离，从而使耦合度降低，提高开发效率（不通过修改源代码的方式，在主干功能里面添加新功能）

### 2、AOP底层使用动态代理

动态代理有两种情况：

#### 1、有接口情况，使用**JDK动态代理**

创建接口的实现类对象，增强类的方法

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%9C%89%E6%8E%A5%E5%8F%A3.png)

#### 2、没有接口情况，使用**CGLIB动态代理**

创建子类的代理对象，增强类的方法

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E6%B2%A1%E6%9C%89%E6%8E%A5%E5%8F%A3.png)

### 3、JDK动态代理

#### 1、使用jdk动态代理，使用Proxy类里面的方法创建代理对象

调用newProxyInstance方法

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/newProxyInstance.png)

方法有三个参数：

第一个参数，类加载器

第二个参数，增强方法所在类，这个类实现实现的接口

第三个参数，实现接口InvocationHandler，创建代理对象，写增强部分

#### 2、代码实现

1. 创建接口，定义方法

   ```java
   public interface UserDao {
    public int add(int a,int b);
    public String update(String id);
   }
   ```

2. 创建接口实现类，实现方法

   ```java
   public class UserDaoImpl implements UserDao {
    @Override
    public int add(int a, int b) {
    return a+b;
    }
    @Override
    public String update(String id) {
    return id;
    } }
   ```

3. 使用Proxy类创建接口代理对象

   ```java
   public class JDKProxy {
    public static void main(String[] args) {
    //创建接口实现类代理对象
    Class[] interfaces = {UserDao.class};
    UserDaoImpl userDao = new UserDaoImpl();
    UserDao dao = (UserDao)Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new UserDaoProxy(userDao));
    int result = dao.add(1, 2);
    System.out.println("result:" + result);
    } 
   }
   //创建代理对象代码
   class UserDaoProxy implements InvocationHandler {
    //1 把创建的是谁的代理对象，把谁传递过来
    //有参数构造传递
    private Object obj;
    public UserDaoProxy(Object obj) {
    	this.obj = obj;
    }
    //增强的逻辑
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    //方法之前
    System.out.println("方法之前执行...." + method.getName()+" :传递的参数..."+ Arrays.toString(args));
    //被增强的方法执行
    Object res = method.invoke(obj, args);
    //方法之后
    System.out.println("方法之后执行...." + obj);
    return res;
    } 
   }
   ```

### 4、AOP的一些术语

1. 连接点：类里面哪些方法可以被增强，这些方法称为连接点

2. 切入点：实际被增强的方法

3. 通知

   (1) 实际增强的逻辑部分称为通知

   (2)通知的类型

   * 前置通知
   * 后置通知
   * 环绕通知
   * 异常通知
   * 最终通知

4. 切面：把通知应用到切入点过程

### 5、AOP操作

1. Spring框架一般都是基于 **AspectJ**实现AOP 操作：AspectJ不是Spring组成部分，独立AOP框架，一般把AspectJ和Spring框架一起使用进行AOP操作

2. 基于AspectJ实现AOP操作

   (1) 基于配置文件

   (2) 基于注解

3. 引入aop依赖

4. 切入点表达式：能够知道对拿个类里面的哪个方法进行增强

   语法结构：execution([权限修饰符] [返回类型] [类全路径] [方法名称] ([参数列表]) )

   举例 1：对 com.atguigu.dao.BookDao 类里面的 add 进行增强

   execution(* com.atguigu.dao.BookDao.add(..))

   举例 2：对 com.atguigu.dao.BookDao 类里面的所有的方法进行增强

   execution(* com.atguigu.dao.BookDao.* (..))

5. AOP操作步骤

   第一步 创建类，在类里面定义方法

   ```java
   public class User {
    public void add() {
    System.out.println("add.......");
    } 
   }
   ```

   第二步 创建增强类(编写增强逻辑)

   ```java
   //增强的类
   public class UserProxy {
    public void before() {//前置通知
    System.out.println("before......");
    } 
   }
   ```

   第三步 进行通知的配置

   1. * 1、开启组件扫描

      ```xml
       <!-- 开启注解扫描 -->
       <context:component-scan base-package="com.hua.spring5.aopanno"></context:component-scan>
      ```

      * 2、使用注解创建User和UserProxy

      * 3、在增强类上面添加注解@Aspect

        ```java
        @Component
        @Aspect //生成代理对象
        public class UserProxy {
        ```

      * 4、在配置文件中开启生成代理对象

        ```xml
        <!-- 开启 Aspect 生成代理对象--> 
        <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
        ```

   第四步 配置不同类型的通知

   * 在增强类的里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置

     ```java
     //增强的类
     @Component
     @Aspect //生成代理对象
     public class UserProxy {
      //前置通知
      //@Before 注解表示作为前置通知
      @Before(value = "execution(* com.hua.spring5.aopanno.User.add(..))")
      public void before() {
     	 System.out.println("before.........");
      }
      //后置通知（返回通知）
      @AfterReturning(value = "execution(* com.hua.spring5.aopanno.User.add(..))")
      public void afterReturning() {
      	System.out.println("afterReturning.........");
      }
      //最终通知
      @After(value = "execution(* com.hua.spring5.aopanno.User.add(..))")
      public void after() {
      	System.out.println("after.........");
      }
      //异常通知
      @AfterThrowing(value = "execution(* com.hua.spring5.aopanno.User.add(..))")
      public void afterThrowing() {
      	System.out.println("afterThrowing.........");
      }
      //环绕通知
      @Around(value = "execution(* com.hua.spring5.aopanno.User.add(..))")
      public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
          System.out.println("环绕之前.........");
          //被增强的方法执行
          proceedingJoinPoint.proceed();
          System.out.println("环绕之后.........");
      } 
     }
     ```

   注：相同切入点可以提取

   ```java
   //相同切入点抽取
   @Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
   public void pointdemo() {
   }
   //前置通知
   //@Before 注解表示作为前置通知
   @Before(value = "pointdemo()")
   public void before() {
    	System.out.println("before.........");
   }
   ```

6. 有多个增强类对同一个方法进行增强，设置增强的优先级

   在增强类上面添加注解 @Order(数字类型值)，数字类型值越小优先级越高

   ```java
   @Component
   @Aspect
   @Order(1)
   public class PersonProxy
   ```

7. 完全注解开发

   创建配置类，不需要创建 xml 配置文件

   ```java
   @Configuration
   @ComponentScan(basePackages = {"com.atguigu"})
   //开启增强类注解
   @EnableAspectJAutoProxy(proxyTargetClass = true)
   public class ConfigAop {
   }
   ```

8. 配置文件的方式略





## 六、JdbcTemplate

### 1、JdbcTemplate简述

Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作

### 2、准备工作

1. 引入相关jar包

2. 在Spring配置文件中配置数据库连接池

   ```xml
   <!-- 数据库连接池 --> 
   <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
    <property name="url" value="jdbc:mysql:///user_db" />
    <property name="username" value="root" />
    <property name="password" value="root" />
    <property name="driverClassName" value="com.mysql.jdbc.Driver" />
   </bean>
   ```

3. 配置JdbcTemplate对象，注入DataSource

   ```xml
   <!-- JdbcTemplate 对象 --> 
   <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    	<!--注入 dataSource-->
    	<property name="dataSource" ref="dataSource"></property>
   </bean>
   ```

4. 创建service类，创建dao类，在dao类注入JdbcTemplate对象

   ```xml
   <!-- 组件扫描 --> 
   <context:component-scan base-package="com.hua"></context:component-scan>
   ```
   
   Service类
   
   ```java
   @Service
   public class BookService {
    //注入 dao
    @Autowired
    private BookDao bookDao; 
    }
   ```
   
   Dao类
   
   ```java
   @Repository
   public class BookDaoImpl implements BookDao {
    //注入 JdbcTemplate
    @Autowired
    private JdbcTemplate jdbcTemplate; 
   }
   ```

### 3、JdbcTemplate操作数据库（添加）

1. 对应数据库创建实体类

   ```java
   public class User{
   	private String userId;
   	private String username;
   	private String ustatus;
   	
   	public void setUserId(String userId){
   		this.userId = userId;
   	}
   }
   ```

2. 编写service和dao

   (1) 在dao进行数据库的添加操作

   (2)调用JdbcTemplate对象里面update方法实现添加操作

   update(String sql,Object ... args)

   第一个参数：sql语句

   第二个参数：可变参数，设置sql语句值

   ```java
   @Repository
   public class BookDaoImpl implements BookDao {
    //注入 JdbcTemplate
    @Autowired
    private JdbcTemplate jdbcTemplate;
    //添加的方法
    @Override
    public void add(Book book) {
        //1 创建 sql 语句
        String sql = "insert into t_book values(?,?,?)";
        //2 调用方法实现
        Object[] args = {book.getUserId(), book.getUsername(), book.getUstatus()};
        int update = jdbcTemplate.update(sql,args);
        //输出的是操作的记录的个数
        System.out.println(update);
    } 
   }
   ```

   ```java
   @Service
   public class BookService {
       //注入dao
       @Autowired
       private BookDao bookDao;
       
       //添加方法
       public void addBook(Book book){
           bookDao.add(book);
       }
   ```

   

3. 测试类

   ```java
   @Test
   public void testJdbcTemplate() {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        BookService bookService = context.getBean("bookService", BookService.class);
        Book book = new Book();
        book.setUserId("1");
        book.setUsername("java");
        book.setUstatus("a");
        bookService.addBook(book);
   }
   ```

### 4、JdbcTemplate操作数据库(修改和删除)

```java
1、修改
@Override
public void updateBook(Book book) {
 String sql = "update t_book set username=?,ustatus=? where user_id=?";
 Object[] args = {book.getUsername(), book.getUstatus(),book.getUserId()};
 int update = jdbcTemplate.update(sql, args);
 System.out.println(update);
}

2、删除
@Override
public void delete(String id) {
 String sql = "delete from t_book where user_id=?";
 int update = jdbcTemplate.update(sql, id);
 System.out.println(update);
}
```

### 5、JdbcTemplate 操作数据库(查询返回某个值)

1. 查询表里有多少条记录，返回的是某个值

2. 使用 JdbcTemplate 实现查询返回某个值代码

   queryForObject(String sql,Class<T> requiredType)

   第一个参数：sql语句

   第二个参数：返回类型class

   ```java
   //查询表记录数
   @Override
   public int selectCount() {
        String sql = "select count(*) from t_book";
        Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
        return count;
   }
   ```

### 6、JdbcTemplate 操作数据库(查询返回对象)

1. JdbcTemplate 实现查询返回对象

   queryForObject(String sql,RowMapper<T> rowMapper,Objext ... args)

   第一个参数：sql语句

   第二个参数：RowMapper 是接口，针对返回不同类型数据，使用这个接口里面实现类完成数据封装

   第三个参数：sql语句值

```java
//查询返回对象
@Override
public Book findBookInfo(String id) {
         String sql = "select * from t_book where user_id=?";
         //调用方法
         Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id);
         return book;
}
```

### 7、JdbcTemplate 操作数据库(查询返回集合)

1. 使用JdbcTemplates方法实现查询返回结合

   query(String sql,RowMapper<T> rowMapper,Objext ... args)

   第一个参数：sql语句

   第二个参数：RowMapper是接口

   第三个参数：sql语句值

   ```java
   //查询返回集合
   @Override
   public List<Book> findAllBook() {
    String sql = "select * from t_book";
    //调用方法
    List<Book> bookList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
    return bookList;
   }
   ```

### 8、JdbcTemplate 操作数据库(批量操作)

1. 批量操作：操作表里面多条记录

2. JdbcTemplate实现批量添加操作

   batchUpdate(String sql,List<Objext[]> batchArgs)

   第一个参数：sql语句

   第二个参数：List集合，添加多条记录数据

```java
//批量添加
@Override
public void batchAddBook(List<Object[]> batchArgs) {
     String sql = "insert into t_book values(?,?,?)";
     int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
     System.out.println(Arrays.toString(ints));
}
```

```java
//批量添加测试
List<Object[]> batchArgs = new ArrayList<>();
Object[] o1 = {"3","java","a"};
Object[] o2 = {"4","c++","b"};
Object[] o3 = {"5","MySQL","c"};
batchArgs.add(o1);
batchArgs.add(o2);
batchArgs.add(o3);
//调用批量添加
bookService.batchAdd(batchArgs);
```



```java
//批量修改
@Override
public void batchUpdateBook(List<Object[]> batchArgs) {
 String sql = "update t_book set username=?,ustatus=? where user_id=?";
 int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
 System.out.println(Arrays.toString(ints));
}
//批量修改
List<Object[]> batchArgs = new ArrayList<>();
Object[] o1 = {"java0909","a3","3"};
Object[] o2 = {"c++1010","b4","4"};
Object[] o3 = {"MySQL1111","c5","5"};
batchArgs.add(o1);
batchArgs.add(o2);
batchArgs.add(o3);
//调用方法实现批量修改
bookService.batchUpdate(batchArgs);
```



```java
//批量删除
@Override
public void batchDeleteBook(List<Object[]> batchArgs) {
     String sql = "delete from t_book where user_id=?";
     int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
     System.out.println(Arrays.toString(ints));
}
//批量删除
List<Object[]> batchArgs = new ArrayList<>();
Object[] o1 = {"3"};
Object[] o2 = {"4"};
batchArgs.add(o1);
batchArgs.add(o2);
//调用方法实现批量删除
bookService.batchDelete(batchArgs);
```





## 七、事务

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

```java
@Service
public class UserService {
    //注入Dao
    @Autowired
    private UserDao userDao;

}
```

​		UserDaoImpl

```java
@Repository
public class UserDaoImpl {
    @Autowired
    private JdbcTemplate jdbcTemplate;

}
```

③在dao中创建两个方法：多钱和少钱，在service创建转账方法

​		dao中

```java
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

```java
//转账方法
public void accountMoney(){
    //lucy少100
    userDao.reduceMoney();
    //marry多100
    userDao.addMoney();
}
```

​    **当有下面的异常时，一个人钱减少而另一个人钱没有增加**

```java
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



### 6.声明式事务管理：使用注解方式(tx命名空间)

①  在Spring配置文件中配置事务管理器

```XML
<!--创建事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!--注入数据源-->
    <property name="dataSource" ref="dataSource"></property>//引用的是jdbc数据源，未copy过来
</bean>
```

②  在spring配置文件，开启事务注解

​		首先引入名称空间tx

```xml
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

```xml
<!--开启事务注解-->
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```

​		

​		在service类上面（或者service类里面方法上面）添加事务注解

​		@Transactional 此注解可以添加到类上面，也可以添加到方法上面

​		如果把这个注解添加到类上面，这个类里面所有的方法都添加了事务

​		如果把这个注解添加到方法上面，为这个方法添加事务

```java
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

```java
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

```xml
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

```java
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





## 八、Spring5 框架新功能

### 1.Spring5框架代码基于java8，运行时兼容JDK9，许多不建议使用的类和方法在代码库中删除

### 2.Spring5框架自带了通用的日志封装

Spring5已经移除Log4jConfigListener,官方建议使用Log4j2,Spring5框架整合Log4j2，Log4j2会输出初始化、翻转和其他内部操作的详细信息

Log4j2的使用

第一步  引入jar包

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/Log4j2%E7%9A%84jar%E5%8C%85.png)

第二步  创建log4j2.xml配置文件（名字固定）

```xml
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

```java
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

```xml
ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
UserService userService = context.getBean("userService", UserService.class);
```

②  整合JUnit5

第一步 引入JUnit5依赖

![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/%E5%BC%95%E5%85%A5JUnit5%E4%BE%9D%E8%B5%96.png)

第二步 创建测试类，使用注解完成

```java
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

```java
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

### 5.Spring5框架新功能：Webflux

1. SpringWebflux介绍
2. 响应式编程
3. Webflux执行流程和核心API
4. SpringWebflux(基于注解编程模型)
5. SpringWebflux(基于函数式编程模型)

