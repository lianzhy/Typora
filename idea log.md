#### 2022-1-17

#####  关于方法泛型参数在方法类型前面出现泛型的问题。

![](https://s2.loli.net/2022/01/17/6N1eymjhOIMW59S.jpg)
[solution](https://blog.csdn.net/yaomingyang/article/details/79263414)
	泛型方法在方法名称前面有一个<T>声明，它的作用是告诉编译器编译的时候就识别它的类型，如果传入的T是A类型，那么你就不可以将B类型传入方法中去。

##### Druid是数据库连接池技术，DBUtils是JDBC的框架。

##### Java可变长参数方法的使用

​	Java从JDK1.5以后，允许定义形参长度可变的参数从而允许为方法指定数量不确定的形参。

在定义方法时最后一个形参类型后增加3个点即(...);则表明改形参可以接受多个参数值，多个参数值会被当做数组传入。

​	语法格式：__public void method(TypeName... param)__

```Java
public static void main(String[] args){
    // TODO Auto-generated method stub
    par("张"，"陈","刘");
}

public static void par(String... strings){
    for(String s : strings){
        Soutem.out.print(s);
    }
}
```

* 调用时，如果同时能匹配固定参数和可变长参数的方法，会优先匹配固定参数方法。
* 如过同时和2个包含可变参数的方法想匹配，则编译器会报错，因为编译器不知道该调用那个方法。
* 一个方法只能有一个可变参数，且可变参数应为最后一个参数。

##### mysql中引号的用法(反引号``,单引号'',双引号"")

* __单引号：__ SQL使用单引号来环绕文本值，如果是数值不要使用单引号。

    ```SQL
    正确：SELECT * FROM Person WHERE FirstName='Bush'
    错误：SELECT * FROM Person WHERE FirstName=Bush
    INSERT INTO t_user(id,username,`password`,email) VALUES
        (NULL,'admin2','111111','lianzhyu@qq.com')
    ```

* __反引号：__ 为了区分MYSQL的保留字与普通字符而引入的符号。

    ```SQL
    SELECT `select` FROM `test` WHERE select='字段值'
    ```

* __双引号：__ 双引号的用法和单引号有所类似，大多数数据库都支持单引号和双引号的互换。

#### 2022-1-18

##### req.getRequestDispatcher()

​	一个请求跨多个<span style="color:red;">Servlet</span>时，需要使用请求转发和请求包含。

   __return：__ RequestDispatcher对象。

* <span style="color:red;">请求包含</span>： 由两个Servlet共同完成响应体（留头由留尾）。ASservlet请求包含到BServlet，那么AServlet既可以设置响应头，也可以完成响应头。

    ``` Java
    req.include( request , response );
    ```

    ![请求包含](https://s2.loli.net/2022/01/18/9s7OxfSGt6lbFAd.jpg)

* <span style="color:red;">请求转发</span>： 由下一个Servlet完成响应体，当前Servlet可以设置响应头（留头不留体）。AServlet请求转发到BServlet，那么AServlet不能够使用response.getWriter()和response.getOutputStream()向客户端输出响应体，但可以使用response.setContentType("text/html;charset=utf-8")设置相应头。而在BServlet中可以输出响应体。

    ```Java
    req.forward( request , response );
    ```

![请求转发](https://s2.loli.net/2022/01/18/yuk1gpTrFn5E8C6.jpg)

需注意的是，无论是请求转发还是请求包含，都在一个请求范围内！使用同一个request和response！

__request域__ 

​	一个请求会创建一个request对象，若在一个请求中跨越了多个Servlet，那么这些Servlet可以使用request来共享数据。同一个请求范围内使用<span style="color:red">request.setAttribute()</span>和<span style="color:red">request.getAttribute()</span>来传值！前一个Servlet调用setAttribute()保存值，后一个Servlet调用getAttribute()获取值。

__请求转发和重定向__

​	1.请求转发是一个请求一次响应，而重定向是两次请求两次响应。

​	2.请求转发地址不变化，而重定向会显示后一个请求的地址。这是因为请求转发是服务器的行为，是容器控制的转向，整个过程处于同一个请求中，因此客户端浏览器不会显示转向后的地址；但重定向是客户端的行为，重新发送了请求，整个过程不在同一个请求中，因此客户端浏览器会显示跳转后的地址。

​	3.请求转发只能转发到本项目其它Servlet，而重定向不只能重定向到本项目的其它Servlet，还能定向到其它项目。

​	4.请求转发是服务端的行为，只需给出转发的Servlet路径，而重定向需要给出requestURLI，既包含项目名。

<p align="center"><Strong>请求转发</Strong></p>

![请求转发](https://s2.loli.net/2022/01/18/vzHgmMnOYEaC6By.jpg)

<p align="center"><Strong>重定向</Strong></p>

![重定向](https://s2.loli.net/2022/01/18/CIk6ixyEaBAMbGY.jpg)

#### 2022-1-22

##### Beanutils 工具类

使用commons-beanutils-1.9.4.jar和commons-logging-1.2.jar报错

```java
java.lang.NoClassDefFoundError: org/apache/commons/collections/FastHashMap
```

解决：导入commons-collections-3.2.2.jar包解决。

#### 2022-2-08

##### JDBC数据库的事务管理

```java
Connection conn = JdbcUtils.getConnection();
try{
	conn.setAutoCommit(false); //设置为手动管理事务
    执行一系列的jdbc操作
    conn.commit();  //手动提交事务
}catch(Exception e){
    conn.rollback();  //回滚事务
}
```

![image-20220208205657145](https://s2.loli.net/2022/02/08/xdBYCtXO91S7vKg.png)

##### 使用Filter过滤器统一给所有的Service方法都加上 try-catch。来进行事务的管理

![image-20220208210415366](https://s2.loli.net/2022/02/08/YopcEWseP83Cahy.png)

#### 2022-2-09

##### List 和 JSON 的相互转换

​	当把 json 转会 list 的时候无法将 list 中的Map转换为Person对象。

```java
List<Person> personList = new ArrayList<>();
personList.add(new Person(1,"abc"));
personList.add(new Person(2,"def"));
personList.add(new Person(3,"ghi"));

//创建Gson对象实例
Gson gson = new Gson();
//把list集合转换为json字符串
String s = gson.toJson(personList);
System.out.println(s);

List<Person> list = gson.fromJson(s, personList.getClass());

System.out.println(list);
Person person = list.get(0);
System.out.println(person);
```

编写一个PersonListType的类用来继承TypeToken

```java
public class PersonListType extends TypeToken<ArrayList<Person>> {
}
```

修改代码：

```java
List<Person> list = gson.fromJson(s, new PersonListType().getType());
```

#### 2022-2-10

##### JavaBean是一个遵循特定写法的Java类，它通常具有如下特点：

* __这个Java类必须具有一个无参的构造函数。__
* __属性必须私有化。__
* __私有化属性必须通过public类型的方法暴露给其他程序，并且方法的命名也必须遵循一定的命名规范。__

#### 2022-3-7

mybatis配置文件错误：

> ==Caused by: com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: 1 字节的 UTF-8 序列==

解决：将UTF-8修改为UTF8

#### 2022-3-28

SpringMVC：找到多个名为spring_web的片段。这是不合法的相对排序：

> 在web.xml配置文件中添加`<absolute-ordering/>`

#### 2022-4-14

> “名为“xxx”的Bean的类型应该是“com”.xxx’，但实际上是’com.sun.proxy.$Proxy22’类型的。”

spring使用的动态代理有两种：JDK Proxy 和CGLIB。
使用前者必须实现至少一个接口才能实现对方法的拦截。
使用后者需要两个jar包：asm.jar和cglib.jar

```java
ApplicationContext ac =
		new ClassPathXmlApplicationContext("applicationContext.xml");
System.out.println(ac);
 UserServiceImpl bean = ac.getBean("userService",UserServiceImpl.class); 
```

**问题出在这句代码上 UserServiceImpl bean = ac.getBean(“userService”,UserServiceImpl.class);不能用接口的实现类（ UserServiceImpl）来转换Proxy的实现类，它们是同级，应该用共同的接口(UserService)来转换 **

把代码改为 UserService bean = ac.getBean(“userService”,UserService.class);

**简单地说就是要用该类的接口来转换，而且必须是该类的接口**

> spring 动态代理Bean named 'userService' is expected to be of type 'cn.msg.service.impl.UserServiceImpl'报错

这个问题出现的原因：一般在使用annotation的方式注入spring的bean 出现的，具体是由于spring采用代理的机制导致的。

默认动态代理是jdk动态代理，而jdk动态代理不支持类注入也就是依赖注入的对象不能是类，只能是接口。

**解决方法：proxy-target-class**
proxy-target-class属性值决定是基于接口的还是基于类的代理被创建。首先说明下proxy-target-class="true"和proxy-target-class="false"的区别，为true则是基于类的代理将起作用（需要cglib库），为false或者省略这个属性，则标准的JDK 基于接口的代理将起作用。
所以遇到这种问题可以在以下标签中**根据需求**设置代理：

```bash
<tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true"/>
<aop:config proxy-target-class="true">
<cache:annotation-driven proxy-target-class="true"/>
```

#### 2022-4-15

> MySQL Error 1170 (42000): BLOB/TEXT Column Used in Key Specification Without a Key Length

MySQL不允许在BLOB/TEXT,TINYBLOB, MEDIUMBLOB, LONGBLOB, TINYTEXT, MEDIUMTEXT, LONGTEXT,VARCHAR建索引，因为前面那些列类型都是可变长的，MySQL无法保证列的唯一性，只能在BLOB/TEXT前n个字节上建索引

对于gbk（一个汉字占两个字节）编码的字段，只能前383个字符建索引;对于utf8（一个汉字占三个字节）编码的字段，只能前255个字符建索引；对于latin编码的字段，只能前767个字符建索引；

#### 2022-4-17

##### 使用JDBC连接数据库时报了异常

> <span style="color:red;">Loading class `com.mysql.jdbc.Driver’. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver’. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.</span>

**有道翻译：加载类\“com.mysql.jdbc.Driver”。这是弃用。新的驱动程序类是’ com.mysql.cj.jdbc.Driver’。驱动程序是通过SPI自动注册的，手动加载驱动程序类通常是不必要的。**

> <span style="color:red;">java.sql.SQLException: The server time zone value ‘ÖÐ¹ú±ê×¼Ê±¼ä’ is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.</span>

**有道翻译：java.sql。SQLException异常:服务器时区价值”OÐ¹u±e×¼e±¼的识别或代表多个时区。如果您想利用时区支持，您必须配置服务器或JDBC驱动程序(通过serverTimezone配置属性)来使用更具体的时区值。**

第一个原因解决办法是：将相关配置文件中的 **driverClass**进行替换

```properties
username=root
password=root
url=jdbc:mysql://localhost:3306/test
# driverClass=com.mysql.jdbc.Driver 
# 将上面那行替换成下面那行
driverClass=com.mysql.cj.jdbc.Driver
```

第二个原因解决办法是：在url后加上serverTimezone属性

```properties
username=root
password=root
url=jdbc:mysql://localhost:3306/test?serverTimezone=UTC
# driverClass=com.mysql.jdbc.Driver 
# 将上面那行替换成下面那行
driverClass=com.mysql.cj.jdbc.Driver
```

#### 2022-4-18

> before start of result set错误

`ResultSet rs=st.executeQuery();`
//直接`rs.getString("Name")` 会报错<span style="color:red;">before start of result set</span>
//要使用

`while(rs.next()){
	rs.get...;
 }`
//如果确定了rs里面只有一条数据可以不用循环，直接使用`if(rs.next())`即可
`if(rs.next()){
     rs.get...;
 }`

百度得到：即使你十分确定能搜出记录，也不可以在没有`rs.next()`之前直接对rs进行取值。
这涉及到rs对象的存储方法。里面说白了就是指针。没next，指针根本没指向对应记录

> 关于使用BeanUtils.populate(bean,value)来注入bean，`req.getParameterMap()`无法获取到提交的表单的信息。

解决：表单中的name属性需要与bean实体类中的属性名一致。

#### 2022-5-3

shiro config 启动报错   bean shiroFilterFactoryBean 未找到

> 最新版的jar不能是config中的方法不能是getshiroFilterFactoryBeane必须是shiroFilterFactoryBean,不然会报找不到的错误
