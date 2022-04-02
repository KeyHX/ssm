# 一、SpringMVC简介



![](https://picture-1310712259.cos.ap-nanjing.myqcloud.com/SpringMvc%E6%9E%B6%E6%9E%84.png)

## 1、什么是MVC

* MVC是一个软件架构的思想，将软件按照模型、视图、控制器来划分
* M：Module，模型层，指工程中的JavaBean，作用是处理数据

* JavaBean分为两类：

  * 一类称为实体类Bean：专门存储业务数据，如Student、User等

  * 一类称为业务处理Bean：指Service或Dao对象，专门用来处理业务逻辑和数据访问

* V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据

* C：Contoller，控制层，指工程中的Servlet，作用是接受请求和响应浏览器

* MVC的工作流程

  用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终相应给浏览器



## 2、什么是SpringMVC

SpringMVC是Spring为表述层开发提供的一整套完整的开发，三层架构分别为表述层（或表示层）、业务逻辑层、数据访问层（持久层），表述层分为前台页面和后台Servlet。SpringMVC就是基于的Servlet，SpringMVC是Java EE项目表述层开发的首选方案

## 3、SpringMVC的特点

* 与IOC容器等基础设施无缝对接
* 基于原生的Servlet，对很多功能进行了封装，通过功能强大的前端控制器DispathcherServlet，对请求和相应进行统一的处理
* 代码简洁，提高开发效率
* 内部组件化程度高，想要什么功能配置相应的组件即可

# 二、搭建框架

## 1、新建一个project - java

## 2、创建一个Maven工程

​	  （new project - maven）

​		在pom.xml中引入依赖

```
 <!--打包方式-->
    <packaging>war</packaging>
    <!--添加依赖-->
    <dependencies>
        <!-- SpringMVC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.1</version>
        </dependency>

        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>

        <!-- ServletAPI -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>

        <!-- Spring5和Thymeleaf整合包 -->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
            <version>3.0.12.RELEASE</version>
        </dependency>
    </dependencies>
```

## 3、引入web工程

* 先在main下创建webapp(new directory)在Project Structure中点开创建工程路径，最右边+号加入xml文件（注意修改路径到src-main-webapp下）

## 4、配置web.xml

注册SpringMVC的前端控制器DispatcherServlet,前端控制器能对浏览器发来的请求进行统一的处理

### 1.默认配置的方式

此前端控制器配置如下，SpringMVC的配置文件（非下面的配置）默认位于WEB-INF下，默认名称为<servlet-name>-servlet.xml，例如，以下配置文件所对应的SpringMVC的配置文件位于WEB-INF下，文件名为SpringMVC-servlet.xml

```xml
<!--配置SpringMVC的前端控制器，对浏览器发送的请求进行统一的处理-->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
        /所匹配的请求可以是/login或html或js或css方式的请求路径
        但是不能匹配jsp的请求（本质就是一个Servlet）
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

### 2.扩展配置方式

可通过init-param标签设置SpringMVC配置文件的位置和名称，通过load-on-startup标签设置SpringMVC前端控制器DispatcherServlet的初始化时间

```
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
    <init-param>
        <!-- contextConfigLocation为固定值 -->
        <param-name>contextConfigLocation</param-name>
        <!-- 使用classpath:表示从类路径查找配置文件，例如maven工程中的src/main/resources -->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!-- 
 		作为框架的核心组件，在启动过程中有大量的初始化操作要做
		而这些操作放在第一次请求时才执行会严重影响访问速度
		因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
	-->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
        设置springMVC的核心控制器所能处理的请求的请求路径
        /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
        但是/不能匹配.jsp请求路径的请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

注：

\<url-pattern>标签中使用/和/*的区别：

/所匹配的请求可以是/login或.html或.js或.css方式的请求路径，但是/不能匹配.jsp请求路径的请求

因此就可以避免在访问jsp页面时，该请求被DispatcherServlet处理，从而找不到相应的页面

/*则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用/\*的写法

## 5、创建请求控制器(先6再5)

由于前端控制器对浏览器发送的请求进行了统一的处理，但是请求有不同的处理过程，因此需要创建处理请求的具体的类，即请求控制器

请求控制器中每一个处理请求的方法为控制器方法。控制器由普通的java类担任，需要通过注解@Controller将其标识为一个控制层组件（还需要组件的扫描，就是创建一个对象使用的），交给Spring的IOC容器管理，这样SpringMVC才能够识别控制器的存在

```
@Controller
public class HelloController {
    
}
```

## 6、配置springMVC的配置文件

在resource创建

```
<!--组件扫描-->
<context:component-scan base-package="com.hua.mvc.controller"></context:component-scan>

<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <!--配置视图解析器的优先级-->
    <property name="order" value="1"/>
    <!--编码-->
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8" />
                </bean>
            </property>
        </bean>
    </property>
</bean>
```

## 7、测试HelloWord

* 创建处理请求的方法，实现对首页的访问

```
    //处理前端控制器中的“/“请求的方法
    @RequestMapping(value = "/")//请求映射：将当前请求与控制器方法创建映射关系
    public String index(){
        //返回视图名称
        return "index";//被视图解析器解析，加上前后缀
    }
```

* 设置超链接访问

  * index.html中设置超链接

  ```
  <!DOCTYPE html>
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
      <meta charset="UTF-8">
      <title>首页</title>
  </head>
  <body>
  <h1>首页</h1>
  <!--@{}表示当前绝对路径-->
  <a th:href="@{/target}">访问目标页面：target.html</a>//@{}括号里面的内容对应着@RequestMapping("/target")括号内容
  
  </body>
  </html>
  ```

  * 请求控制器中处理请求

  ```
  @RequestMapping("/target")
  public String toTarget(){
      return "target";
  }
  ```

## 8、小结

* 浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatherServlet处理，前端控制器再去读取SpringMVC的核心配置文件，通过组件扫描找到控制器，将请求地址和控制器中的@RequestMapping()注解的value值进行匹配，如果匹配成功，该注解所标识的控制器方法就是处理请求的方法，处理请求的方法需要返回一个String类型的视图名称，该视图名称会被视图解析器解析，加上前后缀组成视图的路径

​       ① 前端控制器处理请求(web.xml)    ——>  ② 找到SpringMVC配置文件  ——> ③ 组件扫描到控制器方法

* 搭建框架流程

​        ①  新建一个项目

​		②  新建一个maven，在main下新建一个webapp，Project Structure ——> Modules ——> 选择maven，新建一个web（记得改路径到webapp）

​		③  配置前端控制器web.xml

​		④  在resource下创建springMVC配置文件，并配置信息

​		⑤  创建一个类为处理不同请求的控制器类(注解为@RequestMapping)

# 三、RequestMapping注解

## 1、RequestMapping的功能

@RequestMapping注解的功能就是将请求和处理请求的控制器方法关联起来，建立映射关系，SpringMVC接收到指定的请求，就是找到在映射关系中对应的控制器方法来处理这个请求，每一个请求只能有一个对应控制器方法

## 2、RequestMapping注解的位置

@RequestMapping标识一个类：设置映射请求的请求路径的初始信息

@RequestMapping标识一个方法：设置映射请求的请求路径的具体信息

```
@Controller
@RequestMapping("/hello")
public class RequestMappingController {
    @RequestMapping("/testRequestMapping")
    public String success(){
        return "success";
    }
}
```

```
<a th:href="@{/hello/testRequestMapping}">测试RequestMapping注解的位置</a>
```

## 3、RequestMapping注解的value属性

* @RequestMapping注解的value属性通过请求的请求地址匹配请求映射，value属性是一个字符串类型的数组，表明该请求映射能够匹配多个请求地址所对应的请求，该属性值必须设置

  ```java
  @Controller
  @RequestMapping("/hello")
  public class RequestMappingController {
      @RequestMapping(
              value = {"/testRequestMapping","/test"}//满足一个就可以
      )
      public String success(){
          return "success";
      }
  }
  ```

  ```
  <a th:href="@{/hello/testRequestMapping}">测试RequestMapping注解的位置</a><br/>
  <a th:href="@{/hello/test}">测试test注解的位置</a></br>
  ```



