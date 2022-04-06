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

## 4、RequestMapping注解的method属性

* @RequestMapping注解的method属性是一个数组，表示该映射能够匹配多种请求的方式。首先请求必须满足value属性，然后才必须满足method属性，如果不满足method属性，则会报405

```
@Controller
@RequestMapping("/hello")
public class RequestMappingController {
    @RequestMapping(
            value = {"/testRequestMapping","/test"},//满足一个就可以
            method = {RequestMethod.GET,RequestMethod.POST}//满足一个就可以
    )
    public String success(){
        return "success";
    }
}
```

```
<h1>首页</h1>
<a th:href="@{/hello/testRequestMapping}">测试RequestMapping注解的位置</a><br/>
<a th:href="@{/hello/test}">测试test注解的位置:get属性</a></br>
<form th:action="@{/hello/test}" method="post">//method只可以在表单中修改
    <input type="submit" value="测试method属性：post">
</form>
```

注：

1、对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解(可以替代RequestMapping)

处理get请求的映射-->@GetMapping

处理post请求的映射-->@PostMapping

处理put请求的映射-->@PutMapping

处理delete请求的映射-->@DeleteMapping

```
@GetMapping("/testGetMapping")//method必须是get
public String testGetMapping(){
    return "success";
}
```

2、目前浏览器只支持get和post，如果是put和delete，则默认按get处理

## 5、RequestMapping注解的params属性（了解）

* RequestMapping注解的params属性通过请求的请求参数匹配请求映射，需要全部满足其属性
* RequestMapping注解的params属性是一个字符串类型的数组，有四种表达式设置
  * "param"：要求请求映射所匹配的请求必须携带param请求参数
  * "!param"：要求请求映射所匹配的请求必须不能携带param请求参数
  * "param=value"：要求请求映射所匹配的请求必须携带param请求参数且param=value
  * "param!=value"：要求请求映射所匹配的请求必须携带param请求参数但是param!=value

```
@RequestMapping(value = "/testParamsAndHeaders",
                params = {"username","password=123456"}//必须同时匹配
)
public String teatParamsAndHeaders(){
    return "success";
}
```

```
<a th:href="@{/hello/testParamsAndHeaders(username='admin',password=123456)}">测试RequestMapping注解的params属性</a>
```

注意上图加入参数的写法，出错报404

## 6、RequestMapping注解的headers属性（了解）

@RequestMapping注解的headers属性通过请求的请求头信息匹配请求映射

@RequestMapping注解的headers属性是一个字符串类型的数组，可以通过四种表达式设置请求头信息和请求映射的匹配关系

"header"：要求请求映射所匹配的请求必须携带header请求头信息

"!header"：要求请求映射所匹配的请求必须不能携带header请求头信息

"header=value"：要求请求映射所匹配的请求必须携带header请求头信息且header=value

"header!=value"：要求请求映射所匹配的请求必须携带header请求头信息且header!=value

若当前请求满足@RequestMapping注解的value和method属性，但是不满足headers属性，此时页面显示404错误，即资源未找到

## 7、SpringMVC支持Ant风格的路径（重要）

* ？：表示任意单个字符（特殊符号不能匹配：？、/）

```
@RequestMapping("/a?a/testAnt")
public String testAnt(){
    return "success";
}
```

```
<a th:href="@{/hello/aba/testAnt}" >测试Ant风格的路径——测试使用？</a></br>
```

* *：表示任意的0个或多个字符

```
@RequestMapping("/a*a/testAnt")
public String testAnt(){
    return "success";
}
```

```
<a th:href="@{/hello/ahduisahduiaa/testAnt}" >测试Ant风格的路径——测试使用*</a></br>
```

* **：表示任意的一层或多层目录（用/表示一层一层的目录），只能单独的写在两个/中

```
@RequestMapping("/**/testAnt")
public String testAnt(){
    return "success";
}
```

```
<a th:href="@{/hello/a/a/testAnt}" >测试Ant风格的路径——测试使用**</a></br>
```

## 8、SpringMVC支持路径中的占位符（很重要）

原始方式：/deleteUser?id=1

rest方式：/deleteUser/1

SpringMVC路径中的占位符常用于restful风格中，当请求路径中**将某些数据通过路径的方式传到服务器**，就可以在相应的@RequestMapping注解的value属性中通过占位符传输数据，再通过@PathVariable注解将占位符所表示的数据赋值给形参

```
@RequestMapping("/testPath/{id}")
public String testPath(@PathVariable("id")Integer id){//将占位符中的id赋值给形参
    System.out.println("id:" + id);
    return "success";
}
```

```
<a th:href="@{/hello/testPath/1}" >支持路径中的占位符——testPath</a></br>
```

# 四、SpringMVC获取请求参数

## 1、通过ServletAPI获取

将HttpServletRequest作为控制器方法的形参，此时HttpServletRequest类型的参数表示封装了当前请求的报文对象

```
@RequestMapping("/testServletAPI")
//形参位置的request表示当前请求
public String testServletAPI(HttpServletRequest request){
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    System.out.println("username=" + username + ",password=" + password);
    return "success";
}
```

```
<a th:href="@{/testServletAPI(username='admin',password=123456)}">测试使用ServletAPI获取请求参数</a>
```

## 2、通过控制器方法形参获取请求参数

在控制器方法的形参位置，**设置和请求参数同名的形参**，当浏览器发送请求，匹配请求映射，在前端控制器DispatcherServlet中就会将请求参数赋值给相应的形参

```
<form th:action="@{/testParam}" method="post">
        用户名：<input type="text" name="username"><br/>
        密 码：<input type="password" name="password"><br/>
        兴趣爱好：<input type="checkbox" name="hobby" value="a">a
                 <input type="checkbox" name="hobby" value="b">b
                 <input type="checkbox" name="hobby" value="c">c<br/>
                 <input type="submit" value="测试使用控制器形参获取请求参数">
</form>
```

```
@RequestMapping("testParam")
public String testParam(String username,String password,String[] hobby){
    //若请求参数中出现多个同名的请求参数，可以在控制方法的形参位置设置字符串类型或字符串数组，接受此请求参数
    //若使用字符串类型的形参，最终结果为请求参数的每一个值之间使用逗号进行拼接
    System.out.println("username=" + username + ",password=" + password + ",hobby=" + Arrays.toString(hobby));
    return "success";
}
```

## 3、@RequestParam

* @RequestParam是将请求参数和控制器方法的形参创建映射关系

* @RequestParam注解一共有三个属性：

  * value：指定为形参赋值的请求参数的参数名

  * required：设置是否必须传输此请求参数，默认值为true

    若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null

  * defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值

```
@RequestParam(value = "user_name",required = false,defaultValue = "hehe") String username,//形参与请求参数名字不一样时，通过注解解决
```

## 4、@RequestHeader

* @RequestHeader是将请求头信息和控制器方法的形参创建映射关系
* 注解一共有三个属性：value，required，defaultValue，用法同上

```
@RequestHeader(value = "Host",required = false,defaultValue = "wahaha") String host
```

## 5、@CookieValue

* @CookieValue是将cookie数据和控制器方法的形参创建映射关系
* 注解一共有三个属性：value，required，defaultValue，用法同上

## 6、通过POJO获取请求参数

可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名与实体类中的属性名一直，那么请求参数就会为此属性赋值

```
<form th:action="@{/testBean}" method="post">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    性别：<input type="radio" name="sex" value="男">男<input type="radio" name="sex" value="女">女<br>
    年龄：<input type="text" name="age"><br>
    邮箱：<input type="text" name="email"><br>
    <input type="submit" value="使用实体类接受请求参数">
</form>
```

```
@RequestMapping("/testBean")
public String testBean(User user){
    System.out.println(user);
    return "success";
}
```

```
public class User {
    private Integer id;
    private String username;
    private String password;
    private Integer age;
    private String sex;
    private String email;
    public User() {
    }
    public User(Integer id, String username, String password, Integer age, String sex, String email) {
        this.id = id;
        this.username = username;
        this.password = password;
        this.age = age;
        this.sex = sex;
        this.email = email;
    }
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getPassword() {
        return password;
    }
    public void setPassword(String password) {
        this.password = password;
    }
    public Integer getAge() {
        return age;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    public String getSex() {
        return sex;
    }
    public void setSex(String sex) {
        this.sex = sex;
    }
    public String getEmail() {
        return email;
    }
    public void setEmail(String email) {
        this.email = email;
    }
    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", age=" + age +
                ", sex='" + sex + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

## 7、解决乱码

解决获取请求参数的乱码问题，在web.xml中进行注册

```
<!--解决乱码-->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

# 五、域对象

## 1、使用servletAPI向request域对象共享数据

```
<a th:href="@{/testRequestByServlet}">通过ServletAPI向request域对象共享数据</a>
```

```
//使用ServletAPI向request域对象共享数据
@RequestMapping("/testRequestByServlet")
public String testRequestByServlet(HttpServletRequest request){
    request.setAttribute("testRequestScope","hello,servletAPI");
    return "success";
}
```

```
success<br/>
<p th:text="${testRequestScope}"></p>
```

## 2、使用ModelAndView向request域对象共享数据（重要）

* ModelAndView有两个功能：
  * 向request域对象添加数据
  * 设置视图的名称

```
<a th:href="@{/testModelAndView}">通过testModelAndView向request域对象共享数据</a><br/>
```

```
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){//ModelAndView有两个功能：第一个Model，设置数据；第二个View，设置视图
    ModelAndView mav = new ModelAndView();
    //处理模型数据，即向请求域request共享数据
    mav.addObject("testRequestScope","hello,ModelAndView");
    //设置视图名称
    mav.setViewName("success");
    return mav;
}
```

## 3、使用Model向request域对象共享数据

```
<a th:href="@{/testModel}">通过Model向request域对象共享数据</a><br/>
```

```
@RequestMapping("/testModel")
public String testModel(Model model){
    model.addAttribute("testRequestScope","hello,model");
    return "success";
}
```

## 4、使用Map向request域对象共享数据

```
<a th:href="@{/testMap}">通过Map向request域对象共享数据</a><br/>
```

```
@RequestMapping("/testMap")
public String testMap(Map<String,Object> map){
    map.put("testRequestScope","hello,map");
    return "success";
}
```

## 5、使用ModelMap向request域对象中共享数据

```
<a th:href="@{/testModelMap}">通过ModelMap向request域对象共享数据</a><br/>
```

```
@RequestMapping("/testModelMap")
public String testModelMap(ModelMap modelMap){
    modelMap.addAttribute("testRequestScope","hello,modelMap");
    return "success";
}
```

## 6、Model、ModelMap、Map之间的关系

Model、ModelMap、Map类型的参数本质上都是BindingAwareModelMap类型的

## 7、向Session域共享数据

```
@RequestMapping("/testSession")
public String testSession(HttpSession session){
    session.setAttribute("testSessionScope","hello,session");
    return "success";
}
```

```
<a th:href="@{/testSession}">通过ServletAPI向session域对象共享数据</a><br/>
```

```
<p th:text="${session.testSessionScope}"></p>
```

## 8、向applictaion域共享数据

```
@RequestMapping("/testApplication")
public String testApplication(HttpSession session){
    ServletContext application = session.getServletContext();
    application.setAttribute("testApplication","hello,application");
    return "success";
}
```

```
<a th:href="@{/testApplication}">通过ServletAPI向application域对象共享数据</a><br/>
```

```
<p th:text="${application.testApplication}"></p>
```

# 六、SpringMVC的视图

* SpringMVC中的视图是View接口，视图的作用渲染数据，将模型Model中的数据展示给用户

* SpringMVC视图的种类有很多，默认有转发视图InternalResourceView和重定向视图RedirectView

* 若使用的视图技术为Thymeleaf，在SpringMVC的配置文件中配置了Thymeleaf的视图解析器，由此视图解析器解析之后所得到的是ThymeleafView

## 1、ThymeleafView

当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被SpringMVC配置文件中所配置的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会通过转发的方式实现跳转

```
@RequestMapping("/testThymeleafView")
public String testThymeleafView(){
    return "success";
}
```

## 2、转发视图

SpringMVC中默认的转发视图是InternalResourceView

SpringMVC创建转发视图的情况：

当控制器方法中所设置的视图名称以“forward：”为前缀时，创建InternalResourceView视图，此时的视图名称不会被ThymeleafView解析器解析，而是会将前缀“forward：”去掉，剩余部分作为最终的路径通过转发的方式实现跳转

```
@RequestMapping("/testThymeleafView")
public String testThymeleafView(){
    return "success";
}

@RequestMapping("/testForward")
public String testForward(){
    return "forward:/testThymeleafView";
}
```

## 3、重定向视图

SpringMVC中默认的重定向视图是RedirectView

当控制器方法中所设置的视图名称以“refirect：”为前缀时，创建RedirectView视图，此时的视图名称不会被ThymeleafView解析器解析，而是会将前缀“refirect：”去掉，剩余部分作为最终的路径通过重定向的方式实现跳转

转发和重定向区别：转发是浏览器发送一次请求(服务器内部跳转一次，但是浏览器中的地址栏不发生变化)。重定向是浏览器发送两次请求，第一次访问Servlet，第二次访问重定向的地址（浏览器地址栏中的地址发生变化）。转发可以获取请求域中的数据，重定向不可以，因为转发是一次请求，用到的request是同一个。转发可以访问WEB-INF中的资源，但是重定向不可以，因为WEB-INF下的内容具有安全性，只能通过服务器内部进行访问。

```
@RequestMapping("/testThymeleafView")
public String testThymeleafView(){
    return "success";
}

@RequestMapping("/testForward")
public String testForward(){
    return "forward:/testThymeleafView";
}

@RequestMapping("/testRedirect")
public String testRedirect(){
    return "redirect:/testThymeleafView";
}
```

## 4、视图控制器

当控制器方法中，仅仅用来实现页面跳转，即只需要设置视图的名称时，可以将处理器方法使用view-controller标签进行表示

```
//    @RequestMapping("/")
//    public String index(){
//        return "index";
//    }
```

```
<!--
    path:设置处理的请求地址
    view-name：设置请求地址对应的视图名称
-->
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>//这一行代码实现的是上一段代码块的功能
```

```
<!--开启mvc的注解驱动，不开启的话index里面的链接全部失效-->
<mvc:annotation-driven/>
```

## 5、使用JSP时的配置

在视图使用的是jsp时，变化的地方：springMVC的配置，不再使用ThymeleafView进行配置

```
<!--扫描组件-->
<context:component-scan base-package="com.hua.mvc.controller"></context:component-scan>

<!--jsp不设置任何的前缀也是转发的效果，所以可以采用转发视图-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/templates/"></property>
    <!--后缀-->
    <property name="suffix" value=".jsp"></property>
</bean>
```

在写href时也有变化

```
<body>
    <h1>首页</h1>
    <a href="${pageContext.request.contextPath}/success">success.jsp</a>
</body>
```

# 七、RESTFul

## 1、RESTFul简介

REST：Representational State Transfer

* 资源的表述

​	资源是一种看待服务器的方式，服务器可以看成是由许多离散的资源组成的。它不仅仅可以代表服务器上的一个文件、数据库的表等。资源的表述是对资源在某个特定时刻的状态的表述，可以在服务器与客户端之间进行转移（交换）。资源的表述有多种格式，如HTML/JSON/音视频等等

* 状态的转移

在客户端与服务器之间转移代表资源状态的表述，通过转移和操作资源的表述，来间接实现资源的表述

## 2、RESTFul的实现

在HTTP协议里面，四个表示操作方式的动词：GET(查询资源)、POST(新建资源)、PUT(更新资源)、DELETE(删除)

REST风格提倡URL地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问好键值对的方式携带请求参数，而是将要发送给服务器的数据作为URL地址的一部分。

## 3、四个操作的实现

* get请求分为两种

  * 查询所有用户的信息

    ```
    @RequestMapping(
            value = "/user",
            method = {RequestMethod.GET}
    )
    public String getAllUser(){
        System.out.println("查询所有用户信息");
        return "success";
    }
    ```

    ```
    <a th:href="@{/user}">查询所有用户信息</a><br/>//默认是get请求
    ```

  * 根据id查询用户信息

    ```
    @RequestMapping(value = "/user/{id}",method = RequestMethod.GET)
    public String getUserById(){
        System.out.println("根据id查询用户信息");
        return "success";
    }
    ```

    ```
    <a th:href="@{/user/1}">根据id查询用户信息</a><br/>
    ```

* post请求

  ```
  @RequestMapping(value = "/user",method = RequestMethod.POST)
  public String insertUser(String username,String password){
      System.out.println("添加用户信息：" + username + "," + password);
      return "success";
  }
  ```

  ```
  <form th:action="@{/user}" method="post">
      用户名：<input type="text" name="username"><br/>
      密 码：<input type="password" name="password"><br/>
            <input type="submit" value="添加">
  </form>
  ```

* put请求

要先配置HiddenHttpMethodFilter，以供put和delete功能的实现，在web.xml中进行配置，要在配置编码过滤器之后，不然还会出现乱码

```
<!--配置HiddenHttpMethodFilter-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

```
@RequestMapping(value = "/user",method = RequestMethod.PUT)
public String updateUser(String username,String password){
    System.out.println("修改用户信息：" + username + "," + password);
    return "success";
}
```

```
<!--
    method写的不是post和get，默认按照get来处理
    修改用户信息
-->
<form th:action="@{/user}" method="post">
    <input type="hidden" name="_method" value="put">//name和value是固定写法
    用户名：<input type="text" name="username"><br/>
    密 码：<input type="password" name="password"><br/>
    <input type="submit" value="修改"><br/>
</form>
```

# 八、RESTFul案例

## 1、搭环境（完整可参考）

* 1、创建一个maven，并配置maven的环境

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

* 2、在src\main下创建一个model：webapp，project strcture下创建一个web.xml

  ```
   <!--
      配置编码过滤器,设置编码之前不能获取任何的请求参数，所以配置编码过滤器要在配置HiddenHttpMethodFilter之前
  -->
   <filter>
       <filter-name>CharacterEncodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
           <param-name>encoding</param-name>
           <param-value>UTF-8</param-value>
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
  
   <!--配置处理请求方式put和delete的HiddenHttpMethodFilter-->
   <filter>
       <filter-name>HiddenHttpMethodFilter</filter-name>
       <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
   </filter>
   <filter-mapping>
       <filter-name>HiddenHttpMethodFilter</filter-name>
       <url-pattern>/*</url-pattern>
   </filter-mapping>
  
   <!--配置前端控制器-->
   <servlet>
       <servlet-name>DispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springMVC.xml</param-value>
       </init-param>
       <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
       <servlet-name>DispatcherServlet</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
  ```

* 3、在resources下创建一个springMVC.xml

  ```
  <!--扫描组件-->
  <context:component-scan base-package="com.hua.rest"></context:component-scan>
  
  <!--配置视图解析器-->
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

## 2、案例的其他环境

* 1、创建实体类

​		employee类

```
public class Employee {
    private Integer id;
    private String lastName;

    private String email;
    //1 male, 0 female
    private Integer gender;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Integer getGender() {
        return gender;
    }

    public void setGender(Integer gender) {
        this.gender = gender;
    }

    public Employee(Integer id, String lastName, String email, Integer gender) {
        super();
        this.id = id;
        this.lastName = lastName;
        this.email = email;
        this.gender = gender;
    }

    public Employee() {
    }
```

* 2、dao包

```
@Repository
public class EmployeeDao {
    private static Map<Integer, Employee> employees = null;

    static{
        employees = new HashMap<Integer, Employee>();

        employees.put(1001, new Employee(1001, "E-AA", "aa@163.com", 1));
        employees.put(1002, new Employee(1002, "E-BB", "bb@163.com", 1));
        employees.put(1003, new Employee(1003, "E-CC", "cc@163.com", 0));
        employees.put(1004, new Employee(1004, "E-DD", "dd@163.com", 0));
        employees.put(1005, new Employee(1005, "E-EE", "ee@163.com", 1));
    }

    private static Integer initId = 1006;

    public void save(Employee employee){
        if(employee.getId() == null){
            employee.setId(initId++);//先赋值在添加
        }
        employees.put(employee.getId(), employee);
    }

    public Collection<Employee> getAll(){
        return employees.values();
    }

    public Employee get(Integer id){
        return employees.get(id);
    }

    public void delete(Integer id){
        employees.remove(id);
    }
}
```

## 3、案例的功能清单

| 功能                 | URL地址     | 请求方式 |
| -------------------- | ----------- | -------- |
| 访问首页             | /           | GET      |
| 查询全部数据         | /employee   | GET      |
| 删除                 | /employee/2 | DELETE   |
| 跳转到添加数据的页面 | /toAdd      | GET      |
| 执行保存             | /employee   | POST     |
| 跳转到更新数据的页面 | /employee/2 | GET      |
| 执行更新             | /employee   | PUT      |

## 4、功能实现：访问首页

* 1、在springMVC中配置

```
<!--配置访问首页-->
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>
```

* 2、创建首页界面：index.heml界面

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    <h1>首页</h1>
    <a th:href="@{/employee}">查看员工信息</a>
</body>
</html>
```

## 5、功能实现：查询全部数据

* 控制器方法

```
//查询全部数据
@RequestMapping(value = "/employee",method = {RequestMethod.GET})
public String getAllEmployee(Model model){
    Collection<Employee> employeeList = employeeDao.getAll();
    model.addAttribute("employeeList",employeeList);//向request域中存入数据
    return "employee_list";
}
```

* 创建显示数据页面

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Employee Info</title>
    <script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
</head>
<body>

    <table border="1" cellpadding="0" cellspacing="0" style="text-align: center;" id="dataTable">
        <tr>
            <th colspan="5">Employee Info</th>
        </tr>
        <tr>
            <th>id</th>
            <th>lastName</th>
            <th>email</th>
            <th>gender</th>
            <th>options(<a th:href="@{/toAdd}">add</a>)</th>
        </tr>
        <tr th:each="employee : ${employeeList}">
            <td th:text="${employee.id}"></td>
            <td th:text="${employee.lastName}"></td>
            <td th:text="${employee.email}"></td>
            <td th:text="${employee.gender}"></td>
            <td>
                <a class="deleteA" @click="deleteEmployee" th:href="@{'/employee/'+${employee.id}}">delete</a>
                <a th:href="@{'/employee/'+${employee.id}}">update</a>
            </td>
        </tr>
    </table>
</body>
</html>
```

## 6、功能实现:删除

* 创建处理delete请求方式的表单

```
<!-- 作用：通过超链接控制表单的提交，将post请求转换为delete请求 -->
<form id="delete_form" method="post">
    <!-- HiddenHttpMethodFilter要求：必须传输_method请求参数，并且值为最终的请求方式 -->
    <input type="hidden" name="_method" value="delete"/>
</form>
```

* 删除超链接绑定点击事件

​		引入vue.js

```
<script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
```

​		删除超链接

```
<a class="deleteA" @click="deleteEmployee" th:href="@{'/employee/'+${employee.id}}">delete</a>
```

​		通过vue处理点击事件

```
<script type="text/javascript">
    var vue = new Vue({
        el:"#dataTable",
        methods:{
            //event表示当前事件
            deleteEmployee:function (event) {
                //通过id获取表单标签
                var delete_form = document.getElementById("delete_form");
                //将触发事件的超链接的href属性为表单的action属性赋值
                delete_form.action = event.target.href;
                //提交表单
                delete_form.submit();
                //阻止超链接的默认跳转行为
                event.preventDefault();
            }
        }
    });
</script>
```

* 控制器方法

```
@RequestMapping(value = "/employee/{id}", method = RequestMethod.DELETE)
public String deleteEmployee(@PathVariable("id") Integer id){
    employeeDao.delete(id);
    return "redirect:/employee";
}
```

## 7、功能实现：跳转到添加数据的页面

* 配置跳转

```
<mvc:view-controller path="/toAdd" view-name="employee_add"></mvc:view-controller>
```

* 创建employee_add

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Add Employee</title>
</head>
<body>

<form th:action="@{/employee}" method="post">
    lastName:<input type="text" name="lastName"><br>
    email:<input type="text" name="email"><br>
    gender:<input type="radio" name="gender" value="1">male
    <input type="radio" name="gender" value="0">female<br>
    <input type="submit" value="add"><br>
</form>

</body>
</html>
```

## 8、功能实现：执行保存

* 控制器方法

```
@RequestMapping(value = "/employee", method = RequestMethod.POST)
public String addEmployee(Employee employee){
    employeeDao.save(employee);
    return "redirect:/employee";
}
```

## 9、功能实现：跳转到更新页面

* 修改超链接

```
<a th:href="@{'/employee/'+${employee.id}}">update</a>
```

* 控制器方法

```
@RequestMapping(value = "/employee/{id}", method = RequestMethod.GET)
public String getEmployeeById(@PathVariable("id") Integer id, Model model){
    Employee employee = employeeDao.get(id);
    model.addAttribute("employee", employee);
    return "employee_update";
}
```

* 创建employee_update.html

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Update Employee</title>
</head>
<body>

<form th:action="@{/employee}" method="post">
    <input type="hidden" name="_method" value="put">
    <input type="hidden" name="id" th:value="${employee.id}">
    lastName:<input type="text" name="lastName" th:value="${employee.lastName}"><br>
    email:<input type="text" name="email" th:value="${employee.email}"><br>
    <!--
        th:field="${employee.gender}"可用于单选框或复选框的回显
        若单选框的value和employee.gender的值一致，则添加checked="checked"属性
    -->
    gender:<input type="radio" name="gender" value="1" th:field="${employee.gender}">male
    <input type="radio" name="gender" value="0" th:field="${employee.gender}">female<br>
    <input type="submit" value="update"><br>
</form>

</body>
</html>
```

## 10、功能实现：执行更新

* 控制器方法

```
@RequestMapping(value = "/employee", method = RequestMethod.PUT)
public String updateEmployee(Employee employee){
    employeeDao.save(employee);
    return "redirect:/employee";
}
```

# 九、HttpMessageConverter

* HttpMessageConverter是报文信息转换器，将请求报文转换为Java对象，或将Java对象转换为响应报文。请求报文：浏览器向服务器请求信息；响应报文：服务器响应浏览器的请求
* HttpMessageConverter提供了两个注解和两个类型：@RequestBody，@ResponseBody，RequestEntity，ResponseEntity

## 1、@RequestBody--标注形参

@RequestBody是注解，可以获取**请求体**，需要在控制器方法中设置一个形参，使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值

```
@RequestMapping(value = "/testRequestBody")
public String testRuquestBody(@RequestBody String requestBody){
    System.out.println("requestBody:" + requestBody);
    return "success";
}
```

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>首页</h1>
<form th:action="@{/testRequestBody}" method="post">
    <input type="text" name="username"><br/>
    <input type="password" name="password"><br/>
    <input type="submit" value="测试@RequestBody">
</form>
</body>
</html>
```

运行的结果为：requestBody:username=admin&password=admin

## 2、RequestEntity -- 标注形参

RequestEntity是类型，可以获取请求报文的信息，分为请求头和请求体（只有post才有请求头和请求体）

```
@RequestMapping("/testRequestEntity")
public String testRuquestEntity(RequestEntity<String> requestEntity){
    //requestEntity表示整个请求报文的信息
    System.out.println("请求头:" + requestEntity.getHeaders());
    System.out.println("请求体:" + requestEntity.getBody() );
    return "success";
}
```

```
<form th:action="@{/testRequestEntity}" method="post">
    <input type="text" name="username"><br/>
    <input type="password" name="password"><br/>
    <input type="submit" value="测试@RequestEntity">
</form>
```

## 3、@ResponseBody -- 标注控制器方法

ResponseBody可以响应浏览器的请求

```
@RequestMapping("/testResponse")
public void testResponce(HttpServletResponse response) throws IOException {
    response.getWriter().write("hello,response");
}

@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
    //作为方法的返回值就可以达到上面一个方法的效果
    return "hello,ResponseBody";
}
```

```
<a th:href="@{/testResponse}">通过servletAPI的response对象响应浏览器的数据</a><br/>

<a th:href="@{/testResponseBody}">通过@ResponseBody响应浏览器的数据</a><br/>
```

## 4、@ResponseBody处理json

处理json的步骤：

1、导入json的依赖

```
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```

2、在springMVC中开启mvc注解驱动，此时在HandlerAdaptor中会自动装配一个消息转换器，可以将响应到浏览器的java对象转换为json格式

```
<mvc:annotation-driven/>
```

3、在控制器方法上使用@ResponseBody注解进行标识

4、将java对象直接作为控制器方法的返回值返回，就会自动转换为json格式的字符串

```
@RequestMapping("/testReaponseUser")
@ResponseBody
public User testReaponseUser(){
    return new User(1001,"admin","123456",23,"man");
}
```

## 5、@RestController注解

@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了@Controller注解，并且为其中的每个方法添加了@ResponseBody注解

## 6、ResponseEntity  -- 标注返回值类型

ResponseEntity用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文。作用：文件下载



# 十、文件的上传和下载

## 1、文件的下载

使用ResponseEntity实现文件下载的功能

```
@RequestMapping("/testDown")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
    //获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    //获取服务器中文件的真实路径
    String realPath = servletContext.getRealPath("/static/img/picture.jpg");
    //创建输入流
    InputStream is = new FileInputStream(realPath);
    //创建字节数组，is.available()获取文件所有字节数
    byte[] bytes = new byte[is.available()];
    //将流读到字节数组中，读入的是文件对应的所有字节
    is.read(bytes);


    //创建HttpHeaders对象设置响应头信息
    MultiValueMap<String, String> headers = new HttpHeaders();
    //设置要下载方式以及下载文件的名字
    headers.add("Content-Disposition", "attachment;filename=picture.jpg");
    //设置响应状态码
    HttpStatus statusCode = HttpStatus.OK;
    //创建ResponseEntity对象
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<byte[]>(bytes, headers, statusCode);
    //关闭输入流
    is.close();
    return responseEntity;
}
```

```
<a th:href="@{testDown}">下载picture</a>
```

## 2、文件的上传

文件上传必须是form表单，post请求，并且添加属性enctype="multipart/form-data"

SpringMVC中将上传的文件封装到MultipartFile对象中，通过此对象获取文件的相关信息

上传步骤：

1、添加依赖

```
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

2、在SpringMVC的配置文件中添加配置

```
<!--必须通过文件解析器的解析才能将文件转换为MultipartFile对象-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
```

3、控制器方法

```
    @RequestMapping("/testUp")
    public String testUp(MultipartFile photo,HttpSession session) throws IOException {
//        System.out.println(photo.getName());//获取当前表单元素的name属性值
//        System.out.println(photo.getOriginalFilename());//获取当前上传文件的名字
        String fileName = photo.getOriginalFilename();
        ServletContext servletContext = session.getServletContext();
        String photoPath = servletContext.getRealPath("photo");
        File file = new File(photoPath);
        //判断photoPath所对应的路径是否存在
        if(!file.exists()){
            //不存在就创建
            file.mkdir();
        }
        //加上分隔符和文件名
        String finalPath = photoPath + File.separator + fileName;
        photo.transferTo(new File(finalPath));
        return "success";
    }
```

## 3、文件上传时文件名重名的问题

使用UUID解决文件重名的问题

```
 @RequestMapping("/testUp")
    public String testUp(MultipartFile photo,HttpSession session) throws IOException {
//        System.out.println(photo.getName());//获取当前表单元素的name属性值
//        System.out.println(photo.getOriginalFilename());//获取当前上传文件的名字
        //获取上传的文件的文件名
        String fileName = photo.getOriginalFilename();
        //获取上传的文件的后缀名
        String suffixName = fileName.substring(fileName.lastIndexOf("."));
        //将UUID作为文件名
        String uuid = UUID.randomUUID().toString();
        //将uuid和后缀名拼接的结果作为最终的文件名
        fileName = uuid + suffixName;

        //通过ServletContext获取服务器中photo目录的路径
        ServletContext servletContext = session.getServletContext();
        String photoPath = servletContext.getRealPath("photo");
        File file = new File(photoPath);
        //判断photoPath所对应的路径是否存在
        if(!file.exists()){
            //不存在就创建
            file.mkdir();
        }
        //加上分隔符和文件名
        String finalPath = photoPath + File.separator + fileName;
        photo.transferTo(new File(finalPath));
        return "success";
    }
```



# 十一、拦截器的配置

## 1、拦截器的配置

SpringMVC中的拦截器用于拦截控制器方法的执行

SpringMVC中的拦截器需要实现HandlerInterception

SpringMVC的拦截器必须在SpringMVC的配置文件中进行配置

```
<!--
    配置拦截器
-->
<mvc:interceptors>
    <!--此配置默认对所有的DispatcherServlet请求都会拦截-->
    <!--<bean class="com.hua.mvc.interceptors.FirstInterceptor"></bean>-->
    <!--<ref bean="firstInterceptor"></ref>-->

    <!--此配置可以设置当前的拦截路径-->
    <mvc:interceptor>
        <!--/** 表示拦截所有的请求-->
        <mvc:mapping path="/**"/>
        <!--下面的是排除一些请求不拦截-->
        <mvc:exclude-mapping path="/"/>
        <bean class="com.hua.mvc.interceptors.FirstInterceptor"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

## 2、拦截器的三个抽象方法

SpringMVC中的拦截器的三个抽象方法

preHandle：控制器方法执行之前执行preHandle()，其返回的Boolean类型表示是否放行，返回true放行，返回false表示拦截，即不调用控制器方法

postHandle：控制器方法执行之后执行postHandle

afterComplation：处理完视图和模型数据，渲染视图完毕之后执行afterComplation

## 3、多个拦截器的执行顺序

* 若每个拦截器的preHandle都返回true

此时多个拦截器的执行顺序和拦截器在SpringMVC的配置文件和配置的顺序有关

preHandle会按照配置的顺序执行，而postHandle和afterComplation会按照配置的反序执行

* 若某个拦截器的preHandle返回了false

preHandle返回false和它之前的拦截器的preHandle都会执行，postHandle都不执行，返回false的拦截器之前的拦截器的afterComplation都会执行















































































