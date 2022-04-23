# SSM_PRO

## 1、C3P0作用

传统的JDBC数据库连接使用DriverManager来获取，每次向数据库建立连接的时候都要将Connection加载到内存中，再验证用户名和密码。需要获取数据库连接的时候，就像数据库要求一个，执行完成再断开连接，这样的方式会消耗大量的资源和时间。多人同时连接操作的时候会占用很多的系统资源，如果在数据库连接的时候，程序异常关闭，会导致内存泄漏。

数据库连接池技术解决上面的连接问题。基本思想：为数据库建立一个缓冲池，预先在缓冲池中放入一定数量的连接，当需要建立连接的时候，就从中取出一个，使用完毕再放回去。数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是重新建立一个。

常见数据库连接池：**c3p0**，**dbcp**，**HikariCP**，**Druid**

## 2、mysql驱动

mysql-connector-java，是用于将不同编程语言的应用程序连接到MySQL数据库服务器的驱动程序，没有的话JDBC无法访问数据库（无法注册驱动）。java.sql.Driver 接口是所有 JDBC 驱动程序需要实现的接口，在程序中不需要直接去访问实现了 Driver 接口的类，而是由驱动程序管理器类(java.sql.DriverManager)去调用这些Driver实现。



![](https://picture2-1310712259.cos.ap-nanjing.myqcloud.com/32.png)

## 3、servlet作用

servlet专门用来接收客户端的请求，专门接收客户端的请求数据，然后调用底层service处理数据并生成结果
浏览器http请求------》tomcat服务器-------》到达servlet-----》执行doget，dopost方法----》返回数据
<1>客户端发送请求到服务器端
<2>服务器将请求信息发送至Servlet
<3>Servlet生成响应内容并将其传给服务器。
<4>服务器将响应返回给客户端。
servlet里的三大作用域：

   request(请求)：它的作用范围是一次请求和响应，是三个作用域中最小的。
   session（会话）：它的作用比request要大一点，一次会话过程中，它的作用域就一直存在，(默认是30分钟)
   servletcontext：它作用范围最大，作用于整个服务器中。（Application）

## 4、Spring与SpringMVC包扫描

在一个项目中，容器不一定只有一个，Spring可以包括多个容器，而且容器存在上下层的关系。在一个项目中引入Spring和SpringMVC两个框架，它就是两个容器，Spring是父容器，SpringMVC是子容器，并且在Spring父容器中注册的Bean对于SpringMVC容器中是可见的，而在SpringMVC容器中注册的Bean对于Spring父容器中是不可见的，也就是子容器可以看见父容器中的注册的Bean，反之就不行。Spring父容器负责所有其他非@Controller注解的Bean的注册，而SpringMVC只负责@Controller注解的Bean的注册，使得他们各负其责、明确边界。

```
<mvc:annotation-driven />
```

上面配置声明了@RequestMapping、@RequestBody、@ResponseBody等。并且，该配置默认加载很多的参数绑定方法，比如json转换解析器等。