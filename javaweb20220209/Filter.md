#### Filter过滤器

###### 1、Filter

​		Filter过滤器是JavaWeb的三大组件之一。三大组件分别是：Servlet组件、Listener监听器、Filter过滤器。

​		Filter过滤器它是JavaEE的规范，也就是接口。

​		Filter过滤器它的作用是：<span style="color:red;">拦截请求</span>，过滤响应。

拦截请求常见的应用场景有：权限检查，日记操作，事务管理........

###### 2、要求：在你的web工程下，有一个admin目录。这个admin目录下的所有资源（html页面、jpg图片、jsp文件、等等）都必须是用户登录之后才允许访问。

​		思考：根据之前我们学过的内容。我们知道，用户登录之后都会把用户登录的信息保存到Session域中。所以要检查用户是否登录，可以判断Session中是否包含有用户登录的信息即可！

jsp实现Filter(仅jsp页面可以使用)

```jsp
<%
    Object user = session.getAttribute("user");
    //如果等于null，说明还没有登录
    if(user == null){
        request.getRequestDispatcher("/login.jsp").forward(request, response);
        return;
    }
  %>
```

Filter实现流程：

![image-20220202195548684](https://s2.loli.net/2022/02/02/95GdTZt4RWYNzb2.png)

​	web.xml配置：

```xml
<!--Filter标签用于配置一Filter过滤器-->
    <filter>
        <!--给Filter起一个全类名-->
        <filter-name>AdminFilter</filter-name>
        <!--配置Filter的全类名-->
        <filter-class>com.lianzhy.filter.AdminFilter</filter-class>
    </filter>
    <!--filter-mappingp配置Filter的拦截路径-->
    <filter-mapping>
        <!--filter-name表示当前的拦截路径给那个filter使用-->
        <filter-name>AdminFilter</filter-name>
        <!--url-pattern配置拦截路径
            /  表示请求地址为：http://ip:port/工程路径/ ===>> 映射到idea的web目录
            /admin/*  请求地址为：http://ip:port/工程路径/admin/*
        -->
        <url-pattern>/admin/*</url-pattern>
    </filter-mapping>
```

Filter过滤器的使用步骤：

​		①：编写一个类去实现Filter接口

​		②：实现过滤方法doFilter()

​		③：到web.xml中去配置Filter的拦截路径

###### 3、Filter的生命周期

​		Filter的生命周期包含几个方法

​				1、构造器方法

​				2、Init初始化方法

​						第1，2步，在web工程启动的时候执行（Filter已经创建）

​				3、doFilter过滤方法

​						第3步，每次拦截到请求，就会执行

​				4、destroy销毁

​						第4步，停止web工程的时候，就会执行（停止web工程，也会销毁Filter过滤器）

###### 4、FilterConfig类

​	FilterConfig它是Filter过滤器的配置文件夹。

​	Tomcat每次创建Filter的时候，也会创建一个FilterConfig类，这里包含了Filter配置文件的配置信息。

​	FilterConfig类的作用是获取Filter过滤器的配置内容

​		1，获取Filter的名称  filter-name的内容

```java
	filterConfig.getFileName();
```

​		2，获取Filter中配置的init-param初始化参数

```xml
	<init-param>
        <param-name>username</param-name>
        <param-value>root</param-value>
	</init-param>
	<init-param>
        <param-name>url</param-name>
        <param-value>jdbc:mysql://localhost:3306/test</param-value>
	</init-param>	
```

```java
	filterConfig.getInitParamter("username");
	filterConfig.getInitParamter("url");
```

​		3，获取ServletContext对象

```java
	filterConfig.getServletContext();
```

###### 5、FilterChain过滤器链

​	Filter   过滤器                 Chain  链条

​	FilterChain ，就是过滤器链（多个过滤器如何一起工作）

![image-20220204203235289](https://s2.loli.net/2022/02/04/jDGW6TCyRdotglN.png)

######   6、Filter的拦截路径

​		精确匹配  (请求地址必须为：http://ip:port/工程路径/target.jsp)

```xml
	<url-pattern>/target.jsp</url-pattern>
```

​		目录匹配  (请求地址必须为：http://ip:port/工程路径/admin/*)

```xml
	<url-pattern>/admin/*</url-patten>
```

​		后缀名匹配  (请求地址必须以  *.html结尾)

```xml
	<url-pattern>*.html</url-pattern>
```

Filter过滤器它只关心请求的地址是否匹配，不关心请求的资源是否存在！！