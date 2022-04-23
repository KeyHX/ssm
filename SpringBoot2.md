SpringBoot2

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
  

### 3、自动配置原理

#### 3.1 引导加载自动配置类

```java
@SpringBootApplication
//相当于下面三个注解
//@SpringBootConfiguration
//@EnableAutoConfiguration
//@ComponentScan("com.hua")

@SpringBootConfiguration 就相当于@Configuration，代表当前是一个配置类
    
@ComponentScan("com.hua")组件扫描
    
@EnableAutoConfiguration包含下面两个注解
    1、@AutoConfigurationPackage：自动配置包，内部包含下面注解
    	@Import({AutoConfigurationPackages.Registrar.class})：利用Register给容器中导入一系列组件
    		将指定包下（MainApplication所在包）的所有组件导入进来
	2、@Import({AutoConfigurationImportSelector.class})
    		①、利用getAutoConfigurationEntry(annotationMetadata)给容器中批量导入组件
    		②、调用List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes)获取所有需要导入的包
    		③、利用Map<String, List<String>> loadSpringFactories(ClassLoader classLoader)加载所有的组件
    		④、从META-INF/spring.factories位置加载一个文件，默认扫描这个位置的文件，主要是spring-boot-autoconfigure-2.6.6.jar包下的META-INF/spring.factories

```

#### 3.2 按需开启自动配置项

虽然场景的所有自动配置文件在启动的时候默认全部加载，但最终会按照条件装配原则按需配置

默认会在底层配置好所有的组件，但是如果用户自己配置了，以用户的优先

```java
 @Bean
 @ConditionalOnMissingBean()//判断当前容器中是否已经存在了当前组件，就是用户自己配置的组件
 public MultipartResolver multipartResolver(MultipartResolver resolver) {
            return resolver;
        }
    }
		
```

总结：

* SpringBoot先加载所有自动配置类xxxxxAutoConfiguratioon
* 每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值 xxxxxProperties里面拿，xxxxProperties和配置文件进行了绑定
* 生效的配置类就会给容器中装配很多组件
* 只要容器中有这些组件，相当于这些功能就有了
* 只要用户有自己配置的就以用户的优先
* 定制化配置：
  * 方法一：用户自己使用@Bean替换底层组件
  * 方法二：用户去看这个组件时获取配置文件什么值去修改

xxxxxAutoConfiguration ---> 组件 ----> xxxxxProperties里面拿值 ---> application.properties

### 4、最佳实践

* 引入场景依赖（就是那个starter）

* 查看自动配置了哪些（选做）

  * 自己分析，引入场景的自动配置一般都生效了
  * 配置文件写debug=true开启自动配置报告：Negative matches不生效的配置，Positive matches:生效的自动配置

* 是否需要修改配置

  * 参照配置文档

  * 自己分析，xxxxProperties绑定了配置文件的哪些前缀

  * 自定义加入或替换组件

     @Bean

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

## 五、开发技巧

#### 5.1 LomBok

简化javaBean的开发，可以免去我们在javaBean中写get、set、ToString、有参、无参、HashCode、日志等

但是当我们需要定制构造器的时候，还是需要手动写的

步骤一：引入依赖

```xml
<!--引入lombok插件-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```

步骤二：在setting中的plugins中搜索安装插件lombok

步骤三：

简化JavaBean操作

Pet类

```java
@Data //生成get和set方法
@ToString//生成toString方法
@AllArgsConstructor//生成有参构造器
@NoArgsConstructor//生成无参构造器
@EqualsAndHashCode //生成HashCode
public class Pet {
    private String name;
}
```

生成日志文件

```java
@Slf4j
@RestController //是Controller和ResponseBody的合体
public class HelloController {
    @RequestMapping("/hello")
    public String hello01(@RequestParam("name") String name){
        log.info("请求进来了！！！！");//log日志的使用
        return "Hello,SpringBoot2 !" + "你好！" + name;
    }
}
```

#### 5.2 dev-tools

快捷重新编译项目

引入依赖

```xml
<!--引入dev-tools依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

ctrl + F9重新热部署

#### 5.3 Spring Initailzr

新建项目里面使用Spring Initailzr初始化项目

## 六、SpringBoot2核心技术---核心功能

### 1、配置文件

#### 1.1 properties

同以前的properties用法一致

#### 1.2 yaml

适合用来做以数据为中心的配置文件

1. 基本语法

   * key: value；kv之间有空格
   * 大小写敏感
   * 使用缩进表示
   * 缩进不允许使用tab，只允许空格（使用也没问题）
   * 缩进的空格数不重要，只要相同层级的元素左对齐即可
   * #表示注释
   * 字符串无需加引号，如果要加，’ ’  与 “ ”表示字符串内容会被转义

2. 数据类型

   * 字面量：单个的、不可再分的值。date、boolean、string、number、null

     k: v

   * 对象：键值对的集合。map、hash、set、object

     ```yaml
     行内写法：  k: {k1:v1,k2:v2,k3:v3}
     #或
     k: 
       k1: v1
       k2: v2
       k3: v3
     ```

   * 数组：一组按次序排列的值。array、list、queue

     ```yaml
     行内写法：  k: [v1,v2,v3]
     #或者
     k:
      - v1
      - v2
      - v3
     ```

3. 案例

   person和pet实体类

   ```java
   @ConfigurationProperties(prefix = "person")
   @Component
   @Data
   @ToString
   public class Person {
       private String userName;
       private Boolean boss;
       private Date birth;
       private Integer age;
       private Pet pet;
       private String[] interests;
       private List<String> animal;
       private Map<String, Object> score;
       private Set<Double> salarys;
       private Map<String, List<Pet>> allPets;
   }
   ```

   pet类

   ```java
   @Data
   @ToString
   public class Pet {
       private String name;
       private Double weight;
   }
   ```

   yaml配置文件

   ```yaml
   person:
   # 单引号会将 \n 作为字符串输出 双引号会将 \n 作为换行输出
   # 双引号不会转义，单引号会转义
     boss: true
     birth: 2019/12/9
     age: 18
   #  interests: [篮球,足球]
     interests:
       - 篮球
       - 足球
       - 18
       - 2019/12/9
     animal: [阿猫,阿狗]
   #  score:
   #    english: 80
   #    math: 90
     score: {english:80,math:90}
     salarys:
       - 9999
       - 9999.99
     pet:
       name: 阿狗
       weight: 99.99
     allPets:
       sick:
         - {name: 阿狗,weight: 99.99}
         - name: 阿猫
           weight: 70
   
         - name: 阿赵
           weight: 60
       health:
         - {name: 阿花,weight: 199.99}
         - {name: 阿和,weight: 199.99}
     user-name: 张三
   ```

### 2、Web开发

#### 2.1 静态资源访问

1. 静态资源目录

   类路径下：/static，/public，/resources，/META-INF/resources

   访问：当前项目根路径/ + 静态资源名

   原理：静态映射/**

   请求进来，先去找Controller看能不能处理，不能处理的所有请求又都交给静态资源处理器，静态资源也找不到404

2. 静态资源访问的前缀

   默认无前缀

   在yaml文件中添加

   ```yaml
   spring:
     mvc:
       static-path-pattern: /res/**
   ```

   访问路径为：当前项目 + static-path-pattern + 静态资源名

   ```java
   http://localhost:8080/res/7.png
   ```

3. 改变默认的静态资源路径

   ```java
   spring:
     web:
       resources:
         static-locations: classpath:/haha
   ```
   
   自定义静态资源访问路径

#### 2.2 欢迎页支持

就是访问根路径的时候，自动跳转到index.html界面

* 静态资源路径下编写index.html

  * 可以配置静态资源路径

  * 不可以配置静态资源的访问前缀，否则导致index.html不能默认访问

    ```yaml
    spring:
    #  mvc:
    #    static-path-pattern: /res/**  这个会导致index.heml失效
      web:
        resources:
          static-locations: classpath:/haha
    ```

* controller能处理/index请求

#### 2.3 自定义Favicon

在静态文件夹下创建一个favicon.ico，作用就是在访问的时候标签页面又图标显示

```yaml
spring:
#  mvc:
#    static-path-pattern: /res/**
#  web:
#    resources:
#      static-locations: classpath:/haha
# 这两个会让Favicon功能失效
```

#### 2.4 静态资源配置原理

* SpringBoot启动默认加载xxxxConfiguration类(自动配置类)

* SpringMVC功能的自动配置类WebMVCAutoConfiguration

* 给容器中配置了什么

  ```java
  @Configuration(
      proxyBeanMethods = false
  )
  @Import({EnableWebMvcConfiguration.class})
  @EnableConfigurationProperties({WebMvcProperties.class, WebProperties.class})
  @Order(0)
  public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer, ServletContextAware {
  }
  ```

* 配置文件的相关属性和xxx进行了绑定。WebMvcProperties == spring.mvc、WebProperties == spring.web

1、一个配置类只有一个有参构造器有参构造器，所有参数的值都会从容器中确定

```java
//webProperties：获取和spring.web绑定的所有的值的对象
//mvcProperties：获取和spring.mvc绑定的所有的值的对象
//ListableBeanFactory:找spring容器
//ObjectProvider：找到所有的messageConvertersProvider
//resourceHandlerRegistrationCustomizerProvider：找到资源处理器的自定义器 =====
//ServletRegistrationBean：给应用注册Servlet、Filter
public WebMvcAutoConfigurationAdapter(WebProperties webProperties, WebMvcProperties mvcProperties, ListableBeanFactory beanFactory, ObjectProvider<HttpMessageConverters> messageConvertersProvider, ObjectProvider<ResourceHandlerRegistrationCustomizer> resourceHandlerRegistrationCustomizerProvider, ObjectProvider<DispatcherServletPath> dispatcherServletPath, ObjectProvider<ServletRegistrationBean<?>> servletRegistrations) {
    this.resourceProperties = webProperties.getResources();
    this.mvcProperties = mvcProperties;
    this.beanFactory = beanFactory;
    this.messageConvertersProvider = messageConvertersProvider;
    this.resourceHandlerRegistrationCustomizer = (ResourceHandlerRegistrationCustomizer)resourceHandlerRegistrationCustomizerProvider.getIfAvailable();
    this.dispatcherServletPath = dispatcherServletPath;
    this.servletRegistrations = servletRegistrations;
    this.mvcProperties.checkConfiguration();
}
```

2、资源处理的默认规则

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {
        this.addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
        this.addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
            registration.addResourceLocations(this.resourceProperties.getStaticLocations());
            if (this.servletContext != null) {
                ServletContextResource resource = new ServletContextResource(this.servletContext, "/");
                registration.addResourceLocations(new Resource[]{resource});
            }

        });
    }
}
```

```yaml
spring:
  web:
    resources:
      add-mappings: false  禁用所有静态资源规则
```

3、欢迎页面的处理规则

```java
HandlerMapping：处理器映射。保存了每一个Handler能处理哪些请求。	

@Bean
	public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
			FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
		WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
				new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
				this.mvcProperties.getStaticPathPattern());
		welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
		welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
		return welcomePageHandlerMapping;
	}

WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders,
		ApplicationContext applicationContext, Optional<Resource> welcomePage, String staticPathPattern) {
	if (welcomePage.isPresent() && "/**".equals(staticPathPattern)) {
        //要用欢迎页功能，必须是/**
		logger.info("Adding welcome page: " + welcomePage.get());
		setRootViewName("forward:index.html");
	}
	else if (welcomeTemplateExists(templateAvailabilityProviders, applicationContext)) {
        // 调用Controller  /index
		logger.info("Adding welcome page template: index");
		setRootViewName("index");
	}
}
```

#### 2.5 请求参数处理

1. 请求映射

* @xxxMapping（RequestMapping）：

* Rest风格支持

  * 以前：/getUser 获取用户     /deleteUser  删除用户   /editUser   修改用户   /saveUser 保存用户

  * 现在：/user   GET-获取用户  DELETE  PUT  POST

  * 核心Filter：HiddenHttpMethodFilter

    * 用法：表单method=post，隐藏域_method=put(注意)

    * 在SpringBoot中手动开启

      ```java
      //因为这个，所以要在核心配置文件中手动配置开启
      	@Bean
      	@ConditionalOnMissingBean(HiddenHttpMethodFilter.class)
      	@ConditionalOnProperty(prefix = "spring.mvc.hiddenmethod.filter", name = "enabled", matchIfMissing = false)
      	public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
      		return new OrderedHiddenHttpMethodFilter();
      	}
      ```

      ```yaml
      # 核心配置文件中手动开启
      spring:
        mvc:
          hiddenmethod:
            filter:
              enabled: true
      ```

```java
 @RequestMapping(value = "/user",method = RequestMethod.GET)
    public String getUser(){
        return "GET-张三";
    }

    @RequestMapping(value = "/user",method = RequestMethod.POST)
    public String saveUser(){
        return "POST-张三";
    }


    @RequestMapping(value = "/user",method = RequestMethod.PUT)
    public String putUser(){
        return "PUT-张三";
    }

    @RequestMapping(value = "/user",method = RequestMethod.DELETE)
    public String deleteUser(){
        return "DELETE-张三";
    }

//自定义filter
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
        methodFilter.setMethodParam("_m");
        return methodFilter;
    }
```

index.heml

```xml
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>atguigu，欢迎您</h1>
测试REST风格；
<form action="/user" method="get">
    <input value="REST-GET 提交" type="submit"/>
</form>
<form action="/user" method="post">
    <input value="REST-POST 提交" type="submit"/>
</form>
<form action="/user" method="post">
    <input name="_method" type="hidden" value="delete"/>
    <input name="_m" type="hidden" value="delete"/>
    <input value="REST-DELETE 提交" type="submit"/>
</form>
<form action="/user" method="post">
    <input name="_method" type="hidden" value="PUT"/>
    <input value="REST-PUT 提交" type="submit"/>
</form>
    
</body>
</html>
```

Rest原理(表单提交要使用REST的时候)

* 表单提交会带上_method=PUT
* 请求过来被HiddenHttpMethodFilter拦截
  * 请求是否正常，并且是post方式
    * 获取到_method的值
    * 兼容以下请求：PUT,DELETE,PATCH
    * 原生request，包装模式requestWrapper重写了getMethod方法，返回的是传入的值。
    * 过滤器放行的时候用wrapper，以后调用的getMethod是调用requestWrapper的。

Rest使用客户端工具：如postMan直接发送put、delete请求，无需Filter

#### 2.6 请求映射原理

![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/25.png)

SpringMVC功能分析都从org.springframework.web.servlet.DispatcherServlet -> doDispatch

```java
 protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
        HttpServletRequest processedRequest = request;
        HandlerExecutionChain mappedHandler = null;
        boolean multipartRequestParsed = false;
        WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

        try {
            try {
                ModelAndView mv = null;
                Exception dispatchException = null;

                try {
                    processedRequest = this.checkMultipart(request);
                    multipartRequestParsed = processedRequest != request;
                    //找到当前请求使用的哪个handler处理，就是找到请求对应的方法
                    mappedHandler = this.getHandler(processedRequest);
                    
                    //mappedHandler
```

![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/26.png)

RequestMappingHandlerMapping:保存了所有@RequestMapping 和 handler的映射规则

![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/27.png)

所有请求映射都在HandlerMapping中

* SpringBooot自动配置欢迎页的WelcomePageHandlerMapping。访问/能访问到index.html
* SpringBoot自动配置了默认的requestMappingHandlerMapping
* 请求进来，挨个尝试所有HandlerMapping看是否有请求信息
  * 如果有就找到这个请求对应的Handler
  * 如果没有就是下一个HandlerMapping
* 我们需要一些自定义的映射处理，我们也可以自己给容器中放HandlerMapping

#### 2.7 普通参数与基本注解

都是用来修饰形参

@PathVariable：获取路径变量

@RequestHeader：获取请求头信息

@RequestParam：获取请求参数

@MatrixVariable：矩阵变量

@CookieValue：获取cookie

@RequestBody：获取请求体

```java
@RestController
public class ParameterTestController {
    //  car/2/owner/zhangsan
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String,Object> getCar(@PathVariable("id") Integer id,
                                     @PathVariable("username") String name,
                                     @PathVariable Map<String,String> pv,
                                     @RequestHeader("User-Agent") String userAgent,
                                     @RequestHeader Map<String,String> header,
                                     @RequestParam("age") Integer age,
                                     @RequestParam("inters") List<String> inters,
                                     @RequestParam Map<String,String> params,
                                     @CookieValue("_ga") String _ga,
                                     @CookieValue("_ga") Cookie cookie){


        Map<String,Object> map = new HashMap<>();

//        map.put("id",id);
//        map.put("name",name);
//        map.put("pv",pv);
//        map.put("userAgent",userAgent);
//        map.put("headers",header);
        map.put("age",age);
        map.put("inters",inters);
        map.put("params",params);
        map.put("_ga",_ga);
        System.out.println(cookie.getName()+"===>"+cookie.getValue());
        return map;
    }


    @PostMapping("/save")
    public Map postMethod(@RequestBody String content){
        Map<String,Object> map = new HashMap<>();
        map.put("content",content);
        return map;
    }


    //1、语法： 请求路径：/cars/sell;low=34;brand=byd,audi,yd
    //2、SpringBoot默认是禁用了矩阵变量的功能
    //      手动开启：原理。对于路径的处理。UrlPathHelper进行解析。
    //              removeSemicolonContent（移除分号内容）支持矩阵变量的
    //3、矩阵变量必须有url路径变量才能被解析
    @GetMapping("/cars/{path}")
    public Map carsSell(@MatrixVariable("low") Integer low,
                        @MatrixVariable("brand") List<String> brand,
                        @PathVariable("path") String path){
        Map<String,Object> map = new HashMap<>();

        map.put("low",low);
        map.put("brand",brand);
        map.put("path",path);
        return map;
    }

    // /boss/1;age=20/2;age=10

    @GetMapping("/boss/{bossId}/{empId}")
    public Map boss(@MatrixVariable(value = "age",pathVar = "bossId") Integer bossAge,
                    @MatrixVariable(value = "age",pathVar = "empId") Integer empAge){
        Map<String,Object> map = new HashMap<>();

        map.put("bossAge",bossAge);
        map.put("empAge",empAge);
        return map;

    }

}
```



index.html内容

```xml
/cars/{path}?xxx=xxx&aaa=ccc queryString 查询字符串。@RequestParam；<br/>
/cars/sell;low=34;brand=byd,audi,yd  ；矩阵变量 <br/>
页面开发，cookie禁用了，session里面的内容怎么使用；
session.set(a,b)---> jsessionid ---> cookie ----> 每次发请求携带。
url重写：/abc;jsesssionid=xxxx 把cookie的值使用矩阵变量的方式进行传递.

/boss/1/2

/boss/1;age=20/2;age=20

<a href="/cars/sell;low=34;brand=byd,audi,yd">@MatrixVariable（矩阵变量）</a>
<a href="/cars/sell;low=34;brand=byd;brand=audi;brand=yd">@MatrixVariable（矩阵变量）</a>
<a href="/boss/1;age=20/2;age=10">@MatrixVariable（矩阵变量）/boss/{bossId}/{empId}</a>
<br/>
<form action="/save" method="post">
    测试@RequestBody获取数据 <br/>
    用户名：<input name="userName"/> <br>
    邮箱：<input name="email"/>
    <input type="submit" value="提交"/>
</form>
<ol>
    <li>矩阵变量需要在SpringBoot中手动开启</li>
    <li>根据RFC3986的规范，矩阵变量应当绑定在路径变量中！</li>
    <li>若是有多个矩阵变量，应当使用英文符号;进行分隔。</li>
    <li>若是一个矩阵变量有多个值，应当使用英文符号,进行分隔，或之命名多个重复的key即可。</li>
    <li>如：/cars/sell;low=34;brand=byd,audi,yd</li>
</ol>

```

在使用矩阵变量的时候首先要进行配置

```java
@Configuration(proxyBeanMethods = false)
public class WebConfig /** implements WebMvcConfigurer */ {
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        hiddenHttpMethodFilter.setMethodParam("_m");
        return hiddenHttpMethodFilter;
    }
    //方法二：创建容器
    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void configurePathMatch(PathMatchConfigurer configurer) {
                UrlPathHelper urlPathHelper = new UrlPathHelper();
                urlPathHelper.setRemoveSemicolonContent(false);
                configurer.setUrlPathHelper(urlPathHelper);
            }
        };
    }

    //方法一：实现接口来实现功能
//    @Override
//    public void configurePathMatch(PathMatchConfigurer configurer) {
//        UrlPathHelper urlPathHelper = new UrlPathHelper();
//        //不溢出：后面的内容。矩阵变量功能就可以生效
//        urlPathHelper.setRemoveSemicolonContent(false);
//        configurer.setUrlPathHelper(urlPathHelper);
//    }
}
```

servlet API：

WebRequest、ServletRequest、MultipartRequest、 HttpSession、javax.servlet.http.PushBuilder、Principal、InputStream、Reader、HttpMethod、Locale、TimeZone、ZoneId

复杂参数：

**Map**、**Model（map、model里面的数据会被放在request的请求域  request.setAttribute）、**Errors/BindingResult、**RedirectAttributes（ 重定向携带数据）**、**ServletResponse（response）**、SessionStatus、UriComponentsBuilder、ServletUriComponentsBuilder

#### 2.8 自定义Convert

可以通过自定义的Convert来解析浏览器端传输过来的数据

```html
<form action="/saveuser" method="post">
    姓名： <input name="userName" value="zhangsan"/> <br/>
    年龄： <input name="age" value="18"/> <br/>
    生日： <input name="birth" value="2019/12/10"/> <br/>
<!--        宠物姓名：<input name="pet.name" value="阿猫"/><br/>-->
<!--        宠物年龄：<input name="pet.age" value="5"/>-->
    宠物： <input name="pet" value="啊猫,3"/>
    <input type="submit" value="保存"/>
</form>
```

```java
@Bean
public WebMvcConfigurer webMvcConfigurer(){
    return new WebMvcConfigurer() {
        //将页面提交的字符串转换为pet类型
        @Override
        public void addFormatters(FormatterRegistry registry) {
            registry.addConverter(new Converter<String, Pet>() {
                @Override
                //source就是页面提交过来的值
                public Pet convert(String source) {
                    //阿猫，3
                    if(StringUtils.isEmpty(source)){
                        Pet pet = new Pet();
                        String[] split = source.split(",");
                        pet.setName(split[0]);
                        pet.setAge(split[1]);
                        return pet;
                    }
                    return null;
                }
            });
        }
    };
}
```

### 3.数据响应与内容协商

#### 3.1 响应JSON

首先要引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
web场景自动引入依赖json
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-json</artifactId>
      <version>2.6.6</version>
      <scope>compile</scope>
 </dependency>

```

只要加上@ResponseBody，就会给前端自动返回json数据

#### 3.2HTTPMessageConverter原理

#### 3.3 内容协商

根据客户算的接收能力不同，返回不同类型的数据

1. 首先引入依赖

   ```xml
   <!--支持xml的依赖-->
   <dependency>
       <groupId>com.fasterxml.jackson.dataformat</groupId>
       <artifactId>jackson-dataformat-xml</artifactId>
   </dependency>
   ```

2. postman分别测试json和xml

   只需要改变请求头中的Accept字段，是Http协议中规定的，告诉服务器本客户端可以接收的数据类型

   当Accept是application/xml，就是接收xml格式的文件

   当Accept是application/json，接收的是json格式的数据

#### 3.4 自定义MessageConvert

自定义返回到浏览器端的数据格式。SpringMVC的默认功能的修改，一个入口给容器中添加一个WebMvcConfigurer

1. 先创建一个KeyHXMessageConvert，实现接口HttpMessageConverter

   ```java
   public class KeyHXMessageConvert implements HttpMessageConverter<Person> {
   //是否可读
   @Override
   public boolean canRead(Class<?> clazz, MediaType mediaType) {
       return false;
   }
   
   @Override
   public boolean canWrite(Class<?> clazz, MediaType mediaType) {
       return clazz.isAssignableFrom(Person.class);
   }
   
   /**
    * 服务器要统计所有MessageConvert都能写出哪些内容类型
    * application/KeyHX
    * @return
    */
   @Override
   public List<MediaType> getSupportedMediaTypes() {
       return MediaType.parseMediaTypes("application/KeyHX");
   }
   
   @Override
   public Person read(Class<? extends Person> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException {
       return null;
   }
   
   @Override
   public void write(Person person, MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException {
       //自定义协议数据的写出
       String data = person.getUserName() + ";" + person.getAge() + ";" + person.getBirth();
   
       //写出数据
       OutputStream body = outputMessage.getBody();
       body.write(data.getBytes());
   }
   ```

2. 在WebMvcConfigurer进行配置

   ```java
   public WebMvcConfigurer webMvcConfigurer(){
       return new WebMvcConfigurer() {
           @Override
           public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
               converters.add(new KeyHXMessageConvert());
           }
   ```
   
   访问内容
   
   ```java
   @ResponseBody
   @RequestMapping("/testPerson")
   public Person getPerson(){
       Person person = new Person();
       person.setAge(18);
       person.setBirth(new Date());
       person.setUserName("zhangsan");
       return person;
   }
   ```
   
3. 在postman中进行实现

   ![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/28.png)

4. 在浏览器中进行访问

   首先要自定义协商策略

   ```java
   @Bean
   public WebMvcConfigurer webMvcConfigurer(){
       return new WebMvcConfigurer() {
           /**
            * 自定义内容协商策略
            * @param configurer
            */
           @Override
           public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
               //Map<String, MediaType> mediaTypes
               Map<String, MediaType> mediaTypeMap = new HashMap<>();
               mediaTypeMap.put("json",MediaType.APPLICATION_JSON);
               mediaTypeMap.put("xml",MediaType.APPLICATION_XML);
               mediaTypeMap.put("KeyHX",MediaType.parseMediaType("application/KeyHX"));
               //指定支持解析哪些参数对应的哪些媒体类型
               ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(mediaTypeMap);
               //基于请求头的
               HeaderContentNegotiationStrategy headerStrategy = new HeaderContentNegotiationStrategy();
   
               configurer.strategies(Arrays.asList(parameterStrategy,headerStrategy));
           }
   ```

   再在核心配置文件中进行配置

   ```yaml
   spring:
     mvc:
       contentnegotiation:
         favor-parameter: true
   ```

   浏览器中访问地址

   http://localhost:8080/testPerson?format=xml   其中format的内容可以选择

5. 总结

   在postman中进行访问的是根据请求头信息，在浏览器中进行访问的是参数配置 

### 4、视图解析与模板引擎

视图解析：SpringBoot默认不支持jsp，需要引入第三方模板引擎技术实现页面渲染

视图解析分为：转发，重定向，自定义视图

#### 4.1 thymeleaf的基本语法

1. 表达式

   | 表达式名字 | 语法    | 用途                               |
   | ---------- | ------- | ---------------------------------- |
   | 变量取值   | ${...}  | 获取请求域、session域、对象等值    |
   | 选择变量   | *{...}  | 获取上下文对象值                   |
   | 消息       | \#{...} | 获取国际化等值                     |
   | 链接       | @{...}  | 生成链接                           |
   | 片段表达式 | ~{...}  | jsp:include 作用，引入公共页面片段 |

2. 使用

   * 引入依赖

     ```xml
     <!--引入thymeleaf依赖-->
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-thymeleaf</artifactId>
     </dependency>
     ```

   * 页面默认放在templates，且必须是html后缀的，在html引入名称空间

#### 4.2拦截器使用

1. 编写一个拦截器实现接口HandlerInterceptor

   ```java
   /**
    * 登陆检查
    * 1、配置拦截器要拦截哪些请求
    * 2、将配置放在拦截器中
    */
   public class LoginInterceptor implements HandlerInterceptor {
       /**
        * 目标方法执行之前
        *
        * @param request
        * @param response
        * @param handler
        * @return
        * @throws Exception
        */
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           //登陆检查逻辑
           HttpSession session = request.getSession();
           Object loginUser = session.getAttribute("loginUser");
           if (loginUser != null) {
               //放行
               return true;
           } else {
               //拦截并跳转到登陆界面
               request.setAttribute("msg","请先登陆！");
   //            response.sendRedirect("/");
               request.getRequestDispatcher("/").forward(request,response);
               return false;
           }
       }
   
       /**
        * 页面渲染方法执行之后
        *
        * @param request
        * @param response
        * @param handler
        * @param ex
        * @throws Exception
        */
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
       }
   
       /**
        *目标方法执行之后
        *
        * @param request
        * @param response
        * @param handler
        * @param modelAndView
        * @throws Exception
        */
   
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
       }
   }
   ```

2. 拦截器注册到容器中：实现WebMvcConfigurer的addInterceptors方法，指定拦截的规则：如果拦截所有，静态资源也会被拦截

   ```java
   @Configuration
   public class AdminWebConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new LoginInterceptor())
                   .addPathPatterns("/**") //所有请求都被拦截包括静态资源(样式也会失效)
                   .excludePathPatterns("/","/login","/css/**","/fonts/**","/images/**","/js/**");//放行的请求
       }
   }
   ```

#### 4.3 文件上传

1. 表单设置submit

   ```java
   public class FormTestController {
       @GetMapping("/submit")
       public String submit(){
           return "submit";
       }
   ```

   ```xml
   <form method="post" enctype="multipart/form-data" th:action="@{/upload}">
       <div>
           <label>生活照</label></br>
           <input type="file" multiple="multiple" name="images"></br>
       </div>
       <div>
           <label>头像</label></br>
           <input type="file" name="singleimage"></br>
       </div>
       <input type="submit" value="上传">
   </form>
   ```

2. 配置文件的大小

   ```yaml
   spring:
       servlet:
           multipart:
               max-file-size: 64000MB
               max-request-size: 6400MB
   ```

3. 处理数据

   ```java
   @PostMapping("/upload")
   public String upload(@RequestPart("images") MultipartFile[] headerImg,
                        @RequestPart("singleimage") MultipartFile singleimage) throws IOException {
       log.info("上传的信息：headerImg={},singleimage={}",
               headerImg.length,singleimage.getSize());
   
       if(!singleimage.isEmpty()){
           //保存到文件服务器
           //获取源文件名字
           String originalFilename = singleimage.getOriginalFilename();
           singleimage.transferTo(new File("C:\\Users\\AIERXUAN\\Desktop\\picture\\" + originalFilename));
       }
   
       if(headerImg.length > 0){
           for (MultipartFile multipartFile : headerImg) {
               if (!multipartFile.isEmpty()){
                   String originalFilename = multipartFile.getOriginalFilename();
                   multipartFile.transferTo(new File("C:\\Users\\AIERXUAN\\Desktop\\picture\\" + originalFilename));
               }
           }
       }
   
   
       return "index";
   }
   ```


### 5、异常处理

#### 5.1错误处理

1. 处理规则

   * 默认情况下，SpringBoot提供/error处理所有错误请求映射
   * 对于机器客户端，它将生成json响应，其中包含错误。HTTP状态和异常消息的详细信息。对于浏览器客户端，响应一个whitelabel错误视图，以HTML格式呈现相同的数据
   * static/error/下的4xx，5xx页面会被自动解析：4xx，5xx是状态码

2. 定制错误处理逻辑

   * 自定义错误页面

     static/error/下写404.html，或者5xx.html:有精确的错误状态码就匹配精确，没有就找4xx.html;如果都没有就触发白页

   * @ControllerAdvice + @ExceptionHandler处理全局异常

     ```java
     @ControllerAdvice
     public class GlobalExceptionHandler {
         @ExceptionHandler(value = {ArithmeticException.class,NullPointerException.class}) //处理异常类型
         public String handlerArithException(Exception e){
             return "login";//视图地址
         }
     }
     ```
     
   * @ResponseStatus + 自定义异常(继承运行时类)
   
     ```java
     @ResponseStatus(value = HttpStatus.FORBIDDEN,reason = "用户数量太多")
     public class UserTomManyException extends RuntimeException{
         public UserTomManyException() {
         }
         public UserTomManyException(String message){
             super(message);
         }
     }
     ```
   
     使用：抛出异常
   
     ```java
     if(headerImg.length > 0){
         throw new UserTomManyException();
     }
     ```
   
   * HandlerExceptionResolver:自定义实现HandlerExceptionResolver处理异常
   
     ```java
     @Component
     @Order(value = 1)//数字越小优先级越高
     public class CustomerHandlerExceptionResolver implements HandlerExceptionResolver {
         @Override
         public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
             try {
                 response.sendError(511,"自定义错误");
             } catch (IOException e) {
                 throw new RuntimeException(e);
             }
             return new ModelAndView();
         }
     }
     ```

### 6、Web原生组件注入(Servlet、Filter、Listener)

#### 6.1 方式一

1. 在main方法中设置@ServletComponentScan(basePackages = "com.hua")：指定原生Servlet组件都放在哪里

   @WebServlet(urlPatterns = "/my")效果：直接响应，没用经过拦截器

   ```java
   @SpringBootApplication
   @ServletComponentScan(basePackages = "com.hua")
   public class Boot05WebAdminApplication {
       public static void main(String[] args) {
           SpringApplication.run(Boot05WebAdminApplication.class, args);
       }
   }
   ```

   ```java
   @WebServlet(urlPatterns = "/my")
   public class Myservlet extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           resp.getWriter().write("原生servlet");
       }
   }
   ```

2. Filter

   ```java
   @Slf4j
   @WebFilter(urlPatterns = {"/css/*"})//拦截器，拦截的内容
   public class MyFilter implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
           log.info("初始化完成");
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           log.info("MyFilter工作");
           filterChain.doFilter(servletRequest,servletResponse);
       }
   
       @Override
       public void destroy() {
           log.info("销毁");
       }
   }
   ```

3. Listener

   ```java
   @Slf4j
   @WebListener
   public class MyServletContextListener implements ServletContextListener {
       @Override
       public void contextInitialized(ServletContextEvent sce) {
           log.info("监听到项目初始化完成");
       }
   
       @Override
       public void contextDestroyed(ServletContextEvent sce) {
           log.info("监听到项目销毁");
       }
   }
   ```

#### 6.2方式二

 使用RegistrationBean

MyServlet

```java
public class Myservlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("原生servlet");
    }
}
```

MyFilter

```java
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("初始化完成");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        log.info("MyFilter工作");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        log.info("销毁");
    }
}
```

MyServletContextListener

```java
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("初始化完成");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        log.info("MyFilter工作");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        log.info("销毁");
    }
}
```

配置类

```java
@Configuration(proxyBeanMethods = true)
public class MyRegistConfig {
    @Bean
    public ServletRegistrationBean myServlet(){
        Myservlet myservlet = new Myservlet();
        return new ServletRegistrationBean(myservlet,"/my01","/my02");
    }

    @Bean
    public FilterRegistrationBean myFilter(){
        MyFilter myFilter = new MyFilter();
//        return new FilterRegistrationBean(myFilter,myServlet());//方式一：拦截myServlet里面的路径
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(myFilter);
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/my01"));
        return  filterRegistrationBean;
    }

    @Bean
    public ServletListenerRegistrationBean myListener(){
        MyServletContextListener listener = new MyServletContextListener();
        return new ServletListenerRegistrationBean(listener);
    }
}
```

### 7、定制化原理

#### 7.1 定制化的常见方式

* 修改核心配置文件
* 编写自定义的配置类  xxxConfiguration + @Bean替换、增加容器中的默认组件
* web应用，实现WebMvcConfiguration即可定制化Web的功能：实现WebMvcConfiguration接口

## 七、数据访问

#### 7.1 数据源的自动配置

1. 导入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jdbc</artifactId>
   </dependency>
   ```

   导入MySQL驱动，驱动要和数据库版本对应

   ```xml
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
   </dependency>
   ```

   自动配置的类

   * DataSourceAutoConfiguration：数据源的自动配置
   * DataSourceTransactionManagerAutoConfiguration：事务管理的自动配置
   * JdbcTemplateAutoConfiguration：JdbcTemplate的自动配置，可以来对数据库进行CRUD

2. 修改配置项

   ```yml
   spring:
     datasource:
       url: jdbc:mysql://localhost:3306/test
       username: root
       password: abc123
       driver-class-name: com.mysql.jdbc.Driver
   ```

3. 测试代码

   ```java
   @Slf4j
   @SpringBootTest
   class Boot05WebAdminApplicationTests {
       @Autowired
       JdbcTemplate jdbcTemplate;
       @Test
       void contextLoads() {
           Long length = jdbcTemplate.queryForObject("select count(*) from customers", Long.class);
           log.info("记录总数：{}",length);
       }
   
   }
   ```

#### 7.2 使用Druid数据源

1. 方式一：自定义（过时）

   1. 引入依赖

      ```xml
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid</artifactId>
          <version>1.1.17</version>
      </dependency>
      ```

      ```java
      /**
       * 自动配置数据源
       */
      @Configuration
      public class MyDataSourceConfig {
          @ConfigurationProperties(prefix = "spring.datasource")//属性与配置文件中的配置一一绑定
          @Bean
          public DataSource dataSource(){
              return new DruidDataSource();
          }
      }
      ```

   2. druid的监控功能

      ```java
      /**
       * 自动配置数据源
       */
      @Configuration
      public class MyDataSourceConfig {
          @ConfigurationProperties(prefix = "spring.datasource")//属性与配置文件中的配置一一绑定
          @Bean
          public DataSource dataSource() throws SQLException {
              DruidDataSource druidDataSource = new DruidDataSource();
              //加入监控功能
              druidDataSource.setFilters("stat,wall");
              return druidDataSource;
          }
      
          /**
           * 配置Druid的监控页的功能
           * @return
           */
          @Bean
          public ServletRegistrationBean statViewServlet(){
              StatViewServlet statViewServlet = new StatViewServlet();
              ServletRegistrationBean<StatViewServlet> registrationBean = new ServletRegistrationBean<>(statViewServlet, "/druid/*");//访问的地址
              registrationBean.addInitParameter("loginUsername","admin");//登陆的账户名与密码
              registrationBean.addInitParameter("loginPassword","123456");
              return registrationBean;
          }
      
          /**
           * WebStatFilter 用于采集web-jdbc关联监控的数据
           * @return
           */
          @Bean
          public FilterRegistrationBean WebStatFilter(){
              WebStatFilter webStatFilter = new WebStatFilter();
              FilterRegistrationBean<WebStatFilter> filterRegistrationBean = new FilterRegistrationBean<>(webStatFilter);
              filterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));
              filterRegistrationBean.addInitParameter("exclusion","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
              return filterRegistrationBean;
          }
      }
      ```

2. 使用官方starter方式

   1. 引入依赖

      ```xml
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid-spring-boot-starter</artifactId>
          <version>1.1.17</version>
      </dependency>
      ```

   2. 配置文件

      ```yaml
      spring:
        datasource:
          url: jdbc:mysql://localhost:3306/test
          username: root
          password: abc123
          driver-class-name: com.mysql.jdbc.Driver
      
          druid:
            stat-view-servlet:  # 配置监控页
              enabled: true
              login-username: admin
              login-password: 123456
              reset-enable: false
      
            web-stat-filter:  # 监控web
              enabled: true
              url-pattern: /*
              exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'
            filters: stat,wall   # 底层开启功能：stat(sql监控) wall(防火墙)
            filter:
              stat:
                slow-sql-millis: 1000
                log-slow-sql: true
                enabled: true
              wall:
                enabled: true
      ```

   

#### 7.3 整合MyBatis框架

一、非注解方式

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.mybatis.spring.boot</groupId>
       <artifactId>mybatis-spring-boot-starter</artifactId>
       <version>2.1.4</version>
   </dependency>
   ```

2. 配置模式

   SqlsessionFactory:自动配置好了

   SqlSession：自动配置了SqlSessionTemplate组合了SqlSession

   Mapper：**只要我们写的操作MyBatis的接口标注了@Mapper 就会自动扫描进来**

   ```java
   @EnableConfigurationProperties({MybatisProperties.class})：MyBatis配置项绑定类
   @AutoConfigureAfter({DataSourceAutoConfiguration.class, MybatisLanguageDriverAutoConfiguration.class})
   @ConfigurationProperties(
       prefix = "mybatis"
   )
   public class MybatisProperties {
   }
   ```

   可以修改配置文件中mybatis开始的所有；

   ```yml
   #配置mybatis规则
   mybatis:
   #  config-location: classpath:mybatis-config.xml   # mybatis全局映射文件的位置
     mapper-locations: classpath:com.hua.mapper/*.xml   #mybatissql映射文件
     configuration:
       map-underscore-to-camel-case: true
       #可以不写全局配置文件，所有全局配置文件都放在configuration配置项中即可
   ```

   其余操作与mybatis差不多，注意在mapper接口上面添加@mapper

3. 操作步骤

   * 导入mybatis官方starter
   * 编写mapper接口,标注@Mapper注解
   * 编写sql映射文件并绑定mapper接口
   * 在application.yml中指定Mapper配置文件的位置，以及指定全局配置文件的信息(建议：配置在mybatis.config)

二、注解方式

1. mapper接口

   ```java
   @Mapper
   public interface CityMapper {
   
       @Select("select * from city where id=#{id}")
       public City getById(Long id);
   
       @Insert("insert into city(`name`,`state`,`country`) values (#{name},#{state},#{country})")
       @Options(useGeneratedKeys = true,keyProperty = "id")
       public void insert(City city);
   }
   ```

2. service

   ```java
   @Service
   public class CityService {
       @Autowired
       CityMapper cityMapper;
   
       public City getById(Long id){
           return cityMapper.getById(id);
       }
   
       public void saveCity(City city) {
           cityMapper.insert(city);
       }
   }
   ```

3. controller

   ```java
   @ResponseBody
   @PostMapping("/city")
   public City saveCity(City city){
       cityService.saveCity(city);
       return city;
   }
   
   @ResponseBody
   @GetMapping("/city")
   public City getCityById(@RequestParam("id") Long id){
       return cityService.getById(id);
   }
   ```

   最佳实战：

   * 引入mybatis-starter
   * 配置application.yaml，指定mapper-location位置即可
   * 编写mapper接口并标注@Mapper接口
   * 简单方法直接注解方式
   * 复杂方法编写mapper.xml进行绑定映射

#### 7.4 整合MyBatis-Plus

1. 引入依赖

   ```xml
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.26</version>
           </dependency>
   ```

2. 配置

   mybatis-plus：xxxx就是对mybatis-plus的定制

   SqlSessionFactory 自动配置好的

   mapperLoations：自动配置好的，有默认值。建议以后sql映射文件都放在mapper路径下

   SqlSessionTemplates：自动配置好

   @Mapper标注的也会自动扫描

3. 优点

   * 只需要mapper接口继承BaseMapper就可以拥有CRUD，不需要自己写了

4. 完整流程

   * 创建数据库表user

   * 创建UserBean

     ```java
     @Data
     @TableName("user_test")//指定表名
     public class User {
         /**
          * 存在的所有属性都应该在数据库中有对应的字段
          */
         @TableField(exist = false) //MyBatis-plus新增功能，批注该属性在数据库中没有字段
         private String userName;
         @TableField(exist = false)
         private String password;
     
         private Long id;
         private String name;
         private Integer age;
         private String email;
     }
     ```

   * 创建对应的mapper接口

     ```java
     /**
      * 继承BaseMapper
      */
     public interface UserMapper extends BaseMapper<User> {
     
     }
     ```

   * 测试

     ```java
     @Test
     void testUserMapper(){
         User user = userMapper.selectById(1L);
         log.info("用户信息：{}",user);
     }
     ```

## 八、单元测试

#### 8.1 JUnit5 的变化

**Spring Boot 2.2.0 版本开始引入 JUnit 5 作为单元测试默认库**

作为最新版本的JUnit框架，JUnit5与之前版本的Junit框架有很大的不同。由三个不同子项目的几个不同模块组成。

**JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage**

**JUnit Platform**: Junit Platform是在JVM上启动测试框架的基础，不仅支持Junit自制的测试引擎，其他测试引擎也都可以接入。

**JUnit Jupiter**: JUnit Jupiter提供了JUnit5的新的编程模型，是JUnit5新特性的核心。内部 包含了一个**测试引擎**，用于在Junit Platform上运行。

**JUnit Vintage**: 由于JUint已经发展多年，为了照顾老的项目，JUnit Vintage提供了兼容JUnit4.x,Junit3.x的测试引擎。





注意：

**SpringBoot 2.4 以上版本移除了默认对** **Vintage 的依赖。如果需要兼容junit4需要自行引入（不能使用junit4的功能 @Test****）**

**JUnit 5’s Vintage Engine Removed from** `**spring-boot-starter-test,如果需要继续兼容junit4需要自行引入vintage**`

```xml
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



引入测试依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

SpringBoot整合Junit以后

* 编写测试方法：@Test标注(注意使用junit5版本)
* Junit类具有Spring功能：比如@Transactional标注测试方法，测试完成之后自动回滚

#### 8.2  Junit5常用注解

https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations

- **@Test :**表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
- **@ParameterizedTest :**表示方法是参数化测试，下方会有详细介绍
- **@RepeatedTest :**表示方法可重复执行，下方会有详细介绍
- **@DisplayName :**为测试类或者测试方法设置展示名称
- **@BeforeEach :**表示在每个单元测试之前执行
- **@AfterEach :**表示在每个单元测试之后执行
- **@BeforeAll :**表示在所有单元测试之前执行
- **@AfterAll :**表示在所有单元测试之后执行
- **@Tag :**表示单元测试类别，类似于JUnit4中的@Categories
- **@Disabled :**表示测试类或测试方法不执行，类似于JUnit4中的@Ignore
- **@Timeout :**表示测试方法运行如果超过了指定时间将会返回错误
- **@ExtendWith :**为测试类或测试方法提供扩展类引用

```java
@DisplayName("junit5功能测试")
//@SpringBootTest
public class Junit5Test {
    @Autowired
    JdbcTemplate jdbcTemplate;

    @DisplayName("测试DisplayName注解")
    @Test
    void testDisplayName(){
        System.out.println(1);
        System.out.println(jdbcTemplate);
    }

    @Disabled//禁用
    @DisplayName("测试方法2")
    @Test
    void test2(){
        System.out.println(2);
    }

    @RepeatedTest(5)//重复测试
    @Test
    void test3(){
        System.out.println(5);
    }
    /**
     * 规定方法超时时间，超出时间测试出异常
     * @throws InterruptedException
     */
    @Timeout(value = 5,unit = TimeUnit.MILLISECONDS)
    @Test
    void testTimeOut() throws InterruptedException {
        Thread.sleep(1);
    }


    @BeforeEach
    void testBeforeEach(){
        System.out.println("测试就要开始了！");
    }


    @AfterEach
     void testAfterEach(){
        System.out.println("测试结束了！");
    }

    @BeforeAll
    static void testBeforeAll(){
        System.out.println("所有测试就要开始了！");
    }

    @AfterAll
    static void testAfterAll(){
        System.out.println("所有测试已经结束了！");
    }
}
```

#### 8.3 断言

断言（assertions）是测试方法中的核心部分，用来对测试需要满足的条件进行验证。这些断言方法都org.junit.jupiter.api.Assertions 的静态方法。JUnit 5 内置的断言可以分成如下几个类别：

**检查业务逻辑返回的数据是否合理。**

**所有的测试运行结束以后，会有一个详细的测试报告；**

1. 简单断言

   | 方法            | 说明                                 |
   | --------------- | ------------------------------------ |
   | assertEquals    | 判断两个对象或两个原始类型是否相等   |
   | assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
   | assertSame      | 判断两个对象引用是否指向同一个对象   |
   | assertNotSame   | 判断两个对象引用是否指向不同的对象   |
   | assertTrue      | 判断给定的布尔值是否为 true          |
   | assertFalse     | 判断给定的布尔值是否为 false         |
   | assertNull      | 判断给定的对象引用是否为 null        |
   | assertNotNull   | 判断给定的对象引用是否不为 null      |

   ```java
   /**
    * 断言：前面断言失败，后面的代码都不会执行
    */
   @DisplayName("测试简单断言")
   @Test
   void testSimpleAssertions(){
       int cal = cal(2, 3);
       //相等
       assertEquals(6,cal,"业务逻辑计算失败");
       Object obj1 = new Object();
       Object obj2 = new Object();
       //判断两个对象是否一样
       assertSame(obj1,obj2,"两个对象不一样");
   }
   ```

2. 数组断言

   通过 assertArrayEquals 方法来判断两个对象或原始类型的数组是否相等

   ```java
   @Test
   @DisplayName("array assertion")
   void array(){
       assertArrayEquals(new int[]{1,3},new int[]{1,2},"数组内容不相等");
   }
   ```

3. 组合断言

   assertAll 方法接受多个 org.junit.jupiter.api.Executable 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言

   ```java
   /**
    * 组合断言，声明的所有断言全部成功，才能往下走
    */
   @Test
   @DisplayName("组合断言")
   void all(){
      assertAll("test",
              () -> assertTrue(true && true),
              () -> assertEquals(1,2)
              );
   }
   ```

4. 异常断言

   ```java
   @DisplayName("异常断言")
   @Test
   void testException(){
       //断定业务逻一定出现异常
       assertThrows(ArithmeticException.class,() -> {
           int i = 10 / 1;
       },"业务逻辑正常执行？");
   }
   ```

5. 快速失败

   ```java
   @DisplayName("快速失败")
   @Test
   void testFail(){
       if(2 == 2){
           fail("测试失败");
       }
   }
   ```

6. 前置断言

   JUnit 5 中的前置条件（**assumptions【假设】**）类似于断言，不同之处在于**不满足的断言会使得测试方法失败**，而不满足的**前置条件只会使得测试方法的执行终止**。前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。

   ```java
   /**
    * 测试前置条件
    */
   @Test
   @DisplayName("测试前置条件")
   void testassumption(){
       Assumptions.assumeTrue(false,"结果不是true");
       System.out.println(111111);
   }
   ```

7. 嵌套测试

   JUnit 5 可以通过 Java 中的内部类和@Nested 注解实现嵌套测试，从而可以更好的把相关的测试方法组织在一起。在内部类中可以使用@BeforeEach 和@AfterEach 注解，而且嵌套的层次没有限制。

   ```java
   package com.hua;
   
   import org.junit.jupiter.api.BeforeEach;
   import org.junit.jupiter.api.DisplayName;
   import org.junit.jupiter.api.Nested;
   import org.junit.jupiter.api.Test;
   
   import java.util.EmptyStackException;
   import java.util.Stack;
   
   import static org.junit.jupiter.api.Assertions.*;
   
   @DisplayName("嵌套测试")
   public class TestingAStackDemo {
       Stack<Object> stack;
   
       @Test
       @DisplayName("is instantiated with new Stack()")
       void isInstantiatedWithNew() {
           new Stack<>();
           //嵌套测试的情况下，外层的Test不能驱动内层BeforeEach之类的方法提前/之后运行
           assertNotNull(stack);
       }
   
       @Nested
       @DisplayName("when new")
       class WhenNew {
   
           @BeforeEach
           void createNewStack() {
               stack = new Stack<>();
           }
   
           @Test
           @DisplayName("is empty")
           void isEmpty() {
               assertTrue(stack.isEmpty());
           }
   
           @Test
           @DisplayName("throws EmptyStackException when popped")
           void throwsExceptionWhenPopped() {
               assertThrows(EmptyStackException.class, stack::pop);
           }
   
           @Test
           @DisplayName("throws EmptyStackException when peeked")
           void throwsExceptionWhenPeeked() {
               assertThrows(EmptyStackException.class, stack::peek);
           }
   
           @Nested
           @DisplayName("after pushing an element")
           class AfterPushing {
   
               String anElement = "an element";
   
               @BeforeEach
               void pushAnElement() {
                   stack.push(anElement);
               }
   
               /**
                * 内层的test可以驱动外层的
                */
               @Test
               @DisplayName("it is no longer empty")
               void isNotEmpty() {
                   assertFalse(stack.isEmpty());
               }
   
               @Test
               @DisplayName("returns the element when popped and is empty")
               void returnElementWhenPopped() {
                   assertEquals(anElement, stack.pop());
                   assertTrue(stack.isEmpty());
               }
   
               @Test
               @DisplayName("returns the element when peeked but remains not empty")
               void returnElementWhenPeeked() {
                   assertEquals(anElement, stack.peek());
                   assertFalse(stack.isEmpty());
               }
           }
       }
   }
   ```

8. 参数化测试

   参数化测试是JUnit5很重要的一个新特性，它使得用不同的参数多次运行测试成为了可能，也为我们的单元测试带来许多便利。

   **@ValueSource**: 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型

   **@NullSource**: 表示为参数化测试提供一个null的入参

   **@EnumSource**: 表示为参数化测试提供一个枚举入参

   **@CsvFileSource**：表示读取指定CSV文件内容作为参数化测试入参

   **@MethodSource**：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)

   ```java
   @ParameterizedTest
   @DisplayName("参数化测试")
   @ValueSource(strings = {"1","2","3","4","5"})
   void testParameterized(String i){
       System.out.println(i);
   }
   
   @ParameterizedTest
   @DisplayName("参数化测试")
   @MethodSource("stringProvider")
   void testParameterized2(String i){
       System.out.println(i);
   }
   
   static Stream<String> stringProvider(){
       return Stream.of("apple","banana","KeyHX");
   }
   ```

## 九、指标监控

#### 9.1 SpringBoot Actuator

1. 简介

   未来每一个微服务在云上部署以后，我们都需要对其进行监控、追踪、审计、控制等。SpringBoot就抽取了Actuator场景，使得我们每个微服务快速引用即可获得生产级别的应用监控、审计等功能。

