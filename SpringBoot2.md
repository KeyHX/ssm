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
/**
 * SpringBootApplication:标注这是一个SpringBoot应用
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

### 1、依赖管理

```xml
    <!--父依赖,依赖管理，父项目里面写好了版本号，几乎声明了所有开发中常用的依赖版本号-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.6.6</version>
    </parent>

<!--下面的代码是上面代码的父依赖-->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.6.6</version>
  </parent>
```

使用父依赖，无需关注版本号，自动版本仲裁，可以修改版本号

* 1、查看spring-boot-dependencies里面规定当前依赖的版本

* 2、引入非版本仲裁的jar，在当前项目里面重写配置

  ```xml
  <properties>
      <mysql.version>5.1.43</mysql.version>
  </properties>
  ```

开发导入starter场景启动器

* 格式：spring-boot-starter-*

* 只要引入starter，这个场景的所有常规需要的依赖都会自动引入

* SpringBoot所有支持的场景：文档25页

* *.spring-boot-starter是第三方为我们提高的简化开发的场景启动器

* 所有场景启动器依赖最底层依赖

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>2.6.6</version>
    <scope>compile</scope>
  </dependency>
  ```

### 2、自动配置

* 自动配好Tomcat

  * 引入Tomcat依赖

  * 自动配置Tomcat

    ```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <version>2.6.6</version>
      <scope>compile</scope>
    </dependency>
    ```

* 自动配好SpringMVC

  * 引入SpringMVC全套组件

  * 自动配好SpringMVC常用组件(功能)

    

* 自动配好Web常见功能：如字符串编码问题

  * SpringBoot帮我们配置好了所有web开发从场景

* 默认的包结构

  * **主程序所在包及其下面所有子包里面的组件都会被默认扫描出来(组件扫描)**

  * 无需以前的包扫描配置

  * 想要改变扫描路径：@SpringBootApplication(scanBasePackages = "com.hua")  或者 通过@ComponentScan

    ```java
    @SpringBootApplication
    等同于
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan(com.hua.boot)
    ```

* **各种配置都有默认值**

  * 默认配置最终都是映射到MultipartProperties
  * 配置文件的值最终会绑定每一个类，这个类会在容器中创建对象

* 按需加载所有自动配置项

  * 引入非常多的starter场景
  * 引入了哪些场景这个场景的自动配置才会开启
  * SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure包里面
  

## 四、容器添加

### 4.1 组件添加

#### 1、@Configuration

Full模式与Lite模式

* Full(proxyBeanMethods = true)：当为true时，先判断容器中是否已经存在，如果已经存在，就直接调用，否则创建。即容器中是固定的，创建的是单实例的组件

* lite(proxyBeanMethods = false):当为false时，调用方法时候直接创建。一般为false，这样启动创建快。如果创建的组件还需要用，就创建为true

  ```java
  /**
   * 1、配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单实例的
   * 2、配置类本身也是组件
   * 3、proxyBeanMethods：代理bean的方法
   *      Full(proxyBeanMethods = true)：当为true时，先判断容器中是否已经存在，如果已经存在，就直接调用，否则创建。即容器中是固定的
   *      lite(proxyBeanMethods = false):当为false时，调用方法时候直接创建。一般为false，这样启动创建快。如果创建的组件还需要用，就创建为true
   *      组件依赖
   */
  @Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件（创建对象，属性注入等）
  public class MyConfig {
  
      //外部无论对配置类中的这个组件的注册方法调用多少次获取的都是之前注册的单实例
  
      @Bean //给容器中添加组件，以方法名作为组件的id(默认)，返回类型就是组件的类型，返回的值，就是组件在容器中实例
      public User user01(){
          User fage = new User("fage", 18);
          //user依赖pet
          fage.setPet(tomcatPet());
          return fage;
      }
  
      @Bean("tom")//注解的内容就是组件的名字，默认是方法名
      public Pet tomcatPet(){
          return new Pet("tomcat");
      }
  }
  ```

  ```java
  /**
   *
   * SpringBootApplication:这是一个SpringBoot应用
   */
  @SpringBootApplication
  public class MainApplication {
      //主程序类，启动的入口
      public static void main(String[] args) {
          //1、返回IOC容器
          ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
  
          //2、查看容器里面的组件
          String[] beanDefinitionNames = run.getBeanDefinitionNames();
          for (String name : beanDefinitionNames) {
              System.out.println(name);
          }
  
          //3、从容器中获取组件,注册的组件默认是单实例的
          Pet tom1 = run.getBean("tom", Pet.class);//第一个参数组件名，第二个组件类型
          Pet tom2 = run.getBean("tom", Pet.class);
          System.out.println("组件：" + (tom1 == tom2));
  
          
          MyConfig bean = run.getBean(MyConfig.class);
          System.out.println(bean);
  
          //@Configuration(proxyBeanMethods = true)里面的值是true，则是代理对象调用方法。SpringBoot总会检查这个组件是否在容器中有，保持组件单实例
          User user = bean.user01();
          User user1 = bean.user01();
          System.out.println(user == user1);//true，若proxyBeanMethods设置的是false，则是false
  
          User user01 = run.getBean("user01", User.class);
          Pet tom = run.getBean("tom", Pet.class);
  
          System.out.println(user01.getPet() == tom);//true，若proxyBeanMethods设置的是false，则是false
  
      }
  }
  ```

#### 2、@Import

@Import():在容器中自动创建出给定的类型的组件：给容器中自动创建出给定类型的组件、默认组件的名字就是全类名

```java
@Import({User.class})
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件（创建对象，属性注入等）
public class MyConfig {
}
```

#### 3、Conditional

条件装配：满足Conditional指定的条件，则进行组件注入

![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/23.png)

```java
/**
 * 	@Import():给容器中自动创建出给定的类型的组件
 *    给容器中自动创建出这两个类型的组件、默认组件的名字就是全类名
 */
@Import({User.class})
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件（创建对象，属性注入等）
public class MyConfig {

    //外部无论对配置类中的这个组件的注册方法调用多少次获取的都是之前注册的单实例
    @ConditionalOnBean(name = "tom")
    //当容器中有tom组件的时候再去注册下面的组件，也可以加载类上面，则表示当雕件成立的时候，类里面的bean方法才会执行
   	@Bean 
    //给容器中添加组件，以方法名作为组件的id(默认)，返回类型就是组件的类型，返回的值，就是组件在容器中实例
    public User user01(){
        User fage = new User("fage", 18);
        //user依赖pet
        fage.setPet(tomcatPet());
        return fage;
    }
    
	//@Bean("tom")//注解的内容就是组件的名字，默认是方法名
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}
```

### 4、2 原生配置文件引入

#### 1、@ImportResource

作用：导入Spring的配置文件，现在使用的是@Bean

```java
@ImportResource("classpath:beans.xml")
public class MyConfig {
}
```

beans.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="haha" class="com.hua.boot.bean.User">
        <property name="age" value="12"></property>
        <property name="name" value="yuege"></property>
    </bean>

    <bean id="hehe" class="com.hua.boot.bean.Pet">
        <property name="name" value="fage"></property>
    </bean>
</beans>
```

```java
//与@ImportResource("classpath:beans.xml")有关，如果写了该导入，则下面为true，否则为false
boolean haha = run.containsBean("haha");
boolean hehe = run.containsBean("hehe");
System.out.println("haha:" + haha);
System.out.println("hehe:" + hehe);
```

### 4.3 配置绑定

如何使用Java读取到**核心配置**文件中的内容，并且将它绑定到JavaBean中，以供随时使用，必须是核心配置文件

#### 1、方式一

使用：@Component + @ConfigurationProperties

核心配置文件内容：

```propertise
server.port=8080

mycar.brand=BYD
mycar.price=1000
```

JavaBean实体类car

```java
/**
 * 只有在容器中的组件，才会有SpringBoot提供的强大功能
 */
@Component//将car组件放入到容器中，这样能够让别的地方能够调用
@ConfigurationProperties(prefix = "mycar")//prefix是前缀
public class Car {
    private String brand;
    private Integer price;

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", price=" + price +
                '}';
    }
}
```

```java
//@Controller
//@ResponseBody 传输到浏览器
@RestController //是Controller和ResponseBody的合体
public class HelloController {

    @Autowired
    Car car;

    @RequestMapping("/car")
    public Car car(){
        return car;
    }

    @RequestMapping("/hello")
    public String hello01(){
        return "Hello,SpringBoot2 !" + "你好！";
    }
}
```

#### 2、方式二

使用 @EnableConfigurationProperties + @ConfigurationProperties

```java
@Import({User.class})
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件（创建对象，属性注入等）
@ImportResource("classpath:beans.xml")


@EnableConfigurationProperties(Car.class)
//EnableConfigurationProperties功能
//1、开启Car配置绑定功能  2、将组件Car自动注册到容器中


public class MyConfig {

    //外部无论对配置类中的这个组件的注册方法调用多少次获取的都是之前注册的单实例

    @ConditionalOnBean(name = "tom")//当容器中有tom组件的时候再去注册下面的组件，也可以加载类上面，则表示当雕件成立的时候，类里面的bean方法才会执行
    @Bean //给容器中添加组件，以方法名作为组件的id(默认)，返回类型就是组件的类型，返回的值，就是组件在容器中实例
    public User user01(){
        User fage = new User("fage", 18);
        //user依赖pet
        fage.setPet(tomcatPet());
        return fage;
    }

   @Bean("tom")//注解的内容就是组件的名字，默认是方法名
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}
```

car实体类

```java
/**
 * 只有在容器中的组件，才会有SpringBoot提供的强大功能
 */
@ConfigurationProperties(prefix = "mycar")//prefix是前缀
public class Car {
    private String brand;
    private Integer price;

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", price=" + price +
                '}';
    }
}
```
