## 1.Spring

### 1.1. 简介

* 2002，首次推出Spring框架的雏形：interface21框架！
* _Spring_框架即以interface21框架为基础，经过重新设计，并不断丰富其内涵，与2004年3月24日，发布了1.0正式版。
* **Rod Johnson**  Spring是一个轻量级的控制反转(IoC)和面向切面(AOP)的容器框架。
* spring理念：使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架！



* SSH：Struct2 + Spring + Hibernate！
* SSM：SpringMvc + Spring + Mybatis！



官网：https://spring.io/projects/spring-framework#overview

官方下载地址：[http://repo.spring.io/release/org/springframework/spring](https://repo.spring.io/release/org/springframework/spring)

Github：https://github.com/spring-projects/spring-framework/releases



```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.13</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.13</version>
</dependency>

```

### 1.2. 优点

* Spring是一个开源的免费的框架（容器）！
* Spring是一个轻量级的、非入侵式的框架！
* 控制反转（IOC），面向切面编程（AOP）
* 支持事务的处理，对框架整合的支持！

==Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架！==



### 1.3. 组成

![image-20220315144057120](https://s2.loli.net/2022/03/15/f8Zgc3pMl1EjhGd.png)

### 1.4. 拓展

在Spring的官网中：现代化的Java开发！说白了就是基于Spring的开发！

![image-20220315145414809](https://s2.loli.net/2022/03/15/CvgYHFqiaXKnRxc.png)



* Spring Boot
    * 一个快速开发的脚手架。
    * 基于SpringBoot可以快速的开发单个微服务。
    * 约定大于配置！
* Spring Cloud
    * SpringCloud 是基于SpringBoot实现的。



现在大多数都在使用SpringBoot进行快速开发，学习SpringBoot的前提，需要完全掌握Spring及SpringMVC！承上启下的作用！

## 2. IOC理论推导

1. UserDao 接口
2. UserDaoImpl 实现类
3. UserService 业务接口
4. UserServiceImpl 业务实现类

![image-20220315165651758](https://s2.loli.net/2022/03/15/H26xtMkvCdTG4FX.png)

在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改源码！如果程序代码量十分大，修改一次的成本代价十分昂贵！



我们使用Set接口实现：

```java
UserService.java
void setUserDao(UserDao userDao);

UserServiceImpl.java
//利用set实现动态值的注入
public void setUserDao(UserDao userDao){
    this.userDao = userDao;
}
```

* 之前，程序是主动创建对象！控制权在程序员手上！
* 使用了set注入后，程序不在具有主动性，而是变成了被动的接受对象！



这种思想，从本质上解决了问题，我们程序员不用再去管理对象的创建了。系统的耦合性大大降低~，可以更加专注的在业务的实现上！这是IOC的原型！

![image-20220315165723244](https://s2.loli.net/2022/03/15/7LDuOlM9jdCKkJ5.png)

### IOC本质

**控制反转IoC(Inversion of Control)，是一种设计思想，DI(依赖注入)是实现IoC的一种方法**，没有IoC的程序中 , 我们使用面向对象编程 , 对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方。

![image-20220315213011304](https://s2.loli.net/2022/03/15/YwSxIloT3HAhRmW.png)



采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

**控制反转是一种通过描述（XML或注解）并通过第三方去获取特定对象的方式。在Spring中实现控制反转的是Ioc容器，其实现方法是依赖注入（Dependency Injection,DI）。**

## 3.HelloSpring

1. 编写hello类

    ```java
    public class Hello {
       private String str;
    
       public String getStr() {
          return str;
       }
    
       public void setStr(String str) {
          this.str = str;
       }
    
       @Override
       public String toString() {
          return "Hello{" +
                "str='" + str + '\'' +
                '}';
       }
    }
    ```

2. 编写配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <!--使用Spring来创建对象，在Spring这些都称为Bean
    
        类型   变量名  =   new  Hello();
        Hello hello  =   new  Hello();
    
        id  =  变量名
        class  =  new 的对象；
        property  相当于给对象中的属性赋值！
        -->
        <bean id="hello" class="com.lian.pojo.Hello">
            <property name="str" value="Spring"/>
        </bean>
    
    </beans>
    ```

3. 测试

    ```java
    @Test
    public void test(){
       //获取Spring的上下文对象
       ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
       //我们的对象都在spring中管理了，我们要使用，直接取出来就可以了！
       Hello hello = (Hello) context.getBean("hello");
       System.out.println(hello.toString());
    }
    ```

### 修改UserDao接口及实现类

1. 编写UserDao接口和UserService接口以及实现类

    ![image-20220316153956364](https://s2.loli.net/2022/03/16/B7tUbHYvic3Je14.png)

2. 编写beans.xml配置文件将对象在配置文件中注册

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <bean id="MysqlImpl" class="com.lian.dao.UserDaoMysqlImpl"/>
        <bean id="OracleImpl" class="com.lian.dao.UserDaoOracleImpl"/>
        <bean id="UserServiceImpl" class="com.lian.service.UserServiceImpl">
            
            <!--ref： 引用spring容器中创建好的对象
                value：具体的值，基本数据类型！
            -->
            <property name="userDao" ref="OracleImpl"/>
        </bean>
    
    </beans>
    ```

3. 测试

    ```java
        @Test
       public void test(){
    //    //用户实际调用的是业务层，dao层他们不需要接触！！
    //    UserService userService = new UserServiceImpl();
    //
    //    userService.setUserDao(new UserDaoOracleImpl());
    //    userService.getUser();
    
          //获取ApplicationContext：拿到Spring的容器
          ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans.xml");
    
          //直接获取容器中的Bean对象
          UserServiceImpl userServiceImpl = (UserServiceImpl)applicationContext.getBean("UserServiceImpl");
          userServiceImpl.getUser();
       }
    }
    ```

### 思考问题？

* Hello 对象是谁创建的？

    hello 对象是Spring创建的

* Hello 对象的属性是怎么设置的？

    hello 对象的属性是由Spring容器设置的，

这个过程就叫控制反转：

控制：谁来控制对象的创建，传统应用程序的对象就是由程序本身控制创建的，使用Spring后，对象由Spring来创建的。

反转：程序本身不创建对象，而变成被动的接收对象。

依赖注入：就是利用set方法来进行注入的。

IOC是一种编程思想，由主动的编程变成被动的接收。

可以通过new ClassPathXmlApplicationContext去浏览底层源码。

**到了现在，我们不用再去程序中修改，要实现不同的操作，只需要在配置文件中进行修改，所谓的IoC，一句话搞定：对象由Spring 来创建，管理，装配！**

## 4.IOC创建对象的方式

1. 使用无参构造创建对象，默认！

2. 假设我们要使用有参构造创建对象。

    1. 下标赋值

        ```xml
        <bean id="user" class="com.lian.pojo.User">
            <constructor-arg index="0" value="java"/>
        </bean>
        ```

        需要在User类中加入有参构造函数！！

    2. 参数类型匹配

        ```xml
        <!--第二种方式：通过类型创建  不建议使用！有多个类型一致的参数时无法使用-->
        <bean id="user" class="com.lian.pojo.User">
            <constructor-arg type="java.lang.String" value="zhangSan"/>
        </bean>
        ```

    3. 参数名

        ```xml
        <!--直接通过参数名来设置-->
        <bean id="user" class="com.lian.pojo.User">
            <constructor-arg name="name" value="zhangSan"/>
        </bean>
        ```

总结：在配置文件加载的时候，容器中管理的对象就已经初始化了！

## 5.Spring配置

### 5.1.别名

```xml
<!--别名：如果添加了别名，可以通过别名获取对象-->
<alias name="user" alias="userNew"/>
```

### 5.2.Bean配置

```xml
<!--
id : bean 的唯一标识符，也就是相当于我们的对象名
class : bean 对象所对应的全限定名 : 包名 + 类名
name : 也是别名，而且name, 可以去多个别名
-->
<bean id="userT" class="com.lian.pojo.UserT" name="user2,u2">
    <property name="name" value="张三"/>
</bean>
```

### 5.3.import

一般用于团队开发，它可以将多个配置文件，导入合并为一个！！

假设，现在项目中有多个人开发，这三个人负责不同的类开发，不同的类需要注册在不同的bean中，我们可以利用import将所有人的beans.xml合并为一个总的！

* 张三

* 李四

* 王五

* applicationcontext.xml

    ```xml
    <import resource="beans.xml"/>
    <import resource="beans2.xml"/>
    <import resource="beans3.xml"/>
    ```

使用的时候，直接使用总的配置就可以了

## 6.DI依赖注入

### 6.1. 构造器注入

### 6.2. Set方式注入【重点】

* 依赖注入：Set注入！
    * 依赖：bean对象的创建依赖于容器！
    * 注入：bean独享中的所有属性，由容器来注入！

【环境搭建】

1. 复杂类型

    ```java
    public class Address {
       private String address;
    
       public String getAddress() {
          return address;
       }
    
       public void setAddress(String address) {
          this.address = address;
       }
    }
    ```

2. 真实测试对象

    ```java
    public class Student {
    
       public String name;
       private Address address;
       private String[] books;
       private List<String> hobbys;
       private Map<String,String> card;
       private Set<String> games;
       private String wife;
       private Properties info;
    }
    ```

3. 配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean id="student" class="com.lian.pojo.Student">
            <!--第一种，普通值注入，value-->
            <property name="name" value="张三"/>
    
        </bean>
    </beans>
    ```

完善

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.lian.pojo.Student">
        <!--第一种，普通注入，value-->
        <property name="name" value="张三"/>

        <!--第二种，Bean注入，ref-->
        <property name="address" ref="address"/>

        <!--数组注入-->
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>西游记</value>
                <value>水浒传</value>
                <value>三国演义</value>
            </array>
        </property>

        <!--List-->
        <property name="hobbys">
            <list>
                <value>听歌</value>
                <value>写代码</value>
                <value>看电影</value>
            </list>
        </property>

        <!--Map-->
        <property name="card">
            <map>
                <entry key="身份证" value="11111122223333344455"/>
                <entry key="银行卡" value="44444333555666778899"/>
            </map>
        </property>

        <!--Set-->
        <property name="games">
            <set>
                <value>LOL</value>
                <value>COC</value>
                <value>BOB</value>
            </set>
        </property>

        <!--null-->
        <property name="wife">
            <null/>
        </property>

        <!--Properties-->
        <property name="info">
            <props>
                <prop key="driver">201912345</prop>
                <prop key="url">12</prop>
                <prop key="username">root</prop>
                <prop key="password">111111</prop>
            </props>
        </property>
    </bean>

    <bean id="address" class="com.lian.pojo.Address">
        <property name="address" value="YiBin"/>
    </bean>
</beans>
```

### 6.3. 拓展方式注入

我们可以使用p命名空间和c命名空间进行注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p 命名空间注入，可以直接注入属性的值: property-->
    <bean id="user" class="com.lian.pojo.User" p:name="张三" p:age="18"/>

    <!--c 命名空间注入，通过构造器注入: construct-arg-->
    <bean id="user2" class="com.lian.pojo.User" c:age="18" c:name="张三"/>
</beans>
```

测试：

```java
@Test
public void test1(){
   ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
   User user = context.getBean("user",User.class);
   System.out.println(user);
}
```

> p命名空间和c命名空间不能直接使用需要导入xml约束！

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

### 6.4. Bean Scopes(Bean作用域)

|                            Scope                             | Description                                                  |
| :----------------------------------------------------------: | ------------------------------------------------------------ |
| [singleton](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-singleton) | (Default) Scopes a single bean definition to a single object instance for each Spring IoC container. |
| [prototype](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype) | Scopes a single bean definition to any number of object instances. |
| [request](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [session](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [application](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [websocket](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#websocket-stomp-websocket-scope) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |

1.单例模式（Spring默认机制）

```xml
<bean id="accountService" class="com.something.DefaultAccountService" scope="singleton"/>
```

![image-20220319190512951](https://s2.loli.net/2022/03/19/PqdecWjyZNXlK9n.png)

2.原型模式：每次从容器中get的时候，都会产生一个新对象！

```xml
<bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
```

![image-20220319190548162](https://s2.loli.net/2022/03/19/PsgroneZd2vVULA.png)

3.其余的 request、session、application、这些只能在web开发中使用到！

## 7.Bean的自动装配

* 自动装配是Spring满足bean依赖的一种方式！
* Spring会在上下文中自动寻找，并自动给bean装配属性！



在Spring中有三种装配的方式

1. 在xml中显示的装配
2. 在java中显示配置
3. 隐式的自动装配bean 【重要】



### 7.1.测试

环境搭建：一个人有两个宠物！





### 7.2.ByName自动装配

```xml
<!--
byName: 会自动在容器上下文中查找，和自己对象set方法后面的值对应的 beanid！
-->
<bean id="people" class="com.lian.pojo.People" autowire="byName">
    <property name="name" value="张三"/>
</bean>
```



### 7.3.ByType自动装配

```xml
<bean id="cat" class="com.lian.pojo.Cat"/>
<bean id="dog111" class="com.lian.pojo.Dog"/>

<!--
byName: 会自动在容器上下文中查找，和自己对象set方法后面的值对应的 beanid！ beanId与setXxx一致才可以使用
ByType: 会自动在容器上下文中查找，和自己对象属性类型相同的 bean！bean的名字全局唯一才可以使用 id可以省略
-->
<bean id="people" class="com.lian.pojo.People" autowire="byType">
    <property name="name" value="张三"/>
</bean>
```

小结：

* byName的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致！
* byType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致！



### 7.4.使用注解实现自动装配

jdk1.5支持的注解，Spring2.5就支持注解了！

The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML.

要使用注解须知：

1. 导入约束 context约束`xmlns:context="http://www.springframework.org/schema/context"`

2. 配置注解的支持 ==\<context:annotation-config/>==

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            https://www.springframework.org/schema/context/spring-context.xsd">
    
        <context:annotation-config/>
    
    </beans>
    ```



@Autowired

直接在属性上使用即可！也可以在set方法上使用！

使用Autowired我们可以不用编写Set方法了，前提是你这个自动装配的属性在IOC（Spring）容器中存在，且符合名字byName！

科普：

```xml
@Nullable     字段标记了这个注解，说明这个字段可以为null
```

```java
public @interface Autowired {
    boolean required() default true;
}
```

测试：

```java
public class People {
   //如果显示定义了Autowired中的required属性为false，说明这个对象可以为null，否者不允许为空
   @Autowired(required = false)
   private Cat cat;
   @Autowired
   private Dog dog;
   private String name;
}
```



如果`@Autowired`自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】完成的时候，我们可以使用`@Qualifier(value = "xxx")`去配置`@Autowired`的使用，指定一个唯一的bean对象注入！

```java
public class People {
	@Autowired
	@Qualifier(value = "cat11")
	private Cat cat;
  
	@Autowired
	@Qualifier(value = "dog11")
	private Dog dog;
	private String name;
}
```

使用java `@Resource`注解代替`@Autowired和@Qualifier(value="xxx")`来实现自动装配：

```java
public class People {
  
  @Resource(name = "cat11")
  private Cat cat;
  
  @Resource(name = "dog11")
  private Dog dog;
  private String name;
}
```



小结：

`@Resource`和`@Autowired`的区别:

* 都是用来自动装配的，都可以放在属性字段上
* `@Autowired`通过byType的方式实现，而且必须要求这个对象存在！【常用】
* `@Resource`默认通过byName的方式实现，如果找不到名字，则通过byType的方式实现！如果两个都找不到的情况下，就报错！【常用】
* 执行顺序不同：`@Autowired`通过byType-->byName的方式实现，`@Resource`默认通过byName-->byType的方式实现。



## 8.使用注解开发

在Spring4之后，要使用注解开发，必须保证aop的包导入了

![](https://s2.loli.net/2022/03/20/YosKJfHAjImLe6Z.png)

使用注解需要导入context约束，增加注解支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

1. bean

    ```xml
    <!--指定要扫描的包，这个包下的注解就会生效-->
    <context:component-scan base-package="com.lian.pojo"/>
    ```

    ```java
    //等价于 <bean id="user" class="com.lian.pojo.User"/>
    //@Component 组件
    
    @Component
    public class User {
       public String name = "张三";
    }
    ```

2. 属性如何注入

    ```java
    @Component
    public class User {
    
       public String name;
    
       //相当于<property name="name" value="xxx">
       @Value("张三")
       public void setName(String name) {
          this.name = name;
       }
    }
    ```

3. 衍生的注解

    `@Component`有几个衍生注解，我们在web开发中，会按照mvc三层架构分层！

    * dao  【@Repository】

    * service   【@Service】

    * controller  【@Controller】

        这四个注解功能都是一致的，都是代表将某个类注册到Spring容器中，装配Bean

4. 自动装配

    ```xml
    - @Autowired: 自动装配通过类型。名字
    如果Autowired不能唯一的自动装配上属性，则需要通过@Qualifiler(value = "xxx")
    - @Nullable:  字段标识了这个注解，说明这个字段可以为null;
    - @Resource:  自动装配通过名字。类型。
    - @Component:  组件，放在类上，说明这个类被Spring管理了，就是bean
    ```

5. 作用域

    ```java
    @Component
    @Scope("prototype")
    public class User {
    
    
    	public String name;
    
    	//相当于<property name="name" value="xxx">
    	@Value("张三")
    	public void setName(String name) {
    		this.name = name;
    	}
    }
    ```

6. 小结

    xml 与注解：

    * xml更加万能，适合任何场合！维护简单方便
    * 注解不是自己类使用不了，相对复杂！

    xml 与注解最佳实践：

    * xml 用来管理bean;
    * 注解只负责完成属性的注入！
    * 我们在使用的过程中只需要注意一个问题：必须让注解生效，就需要开启注解的支持

    ```xml
    <!--指定要扫描的包，这个包下的注解就会生效-->
    <context:component-scan base-package="com.lian"/>
    <context:annotation-config/>
    ```

## 9.使用Java的方式配置Spring

我们现在要完全不使用Spring的xml配置了，全权交给Java来做！

JavaConfig 是 Spring 的一个子项目，在 Spring4 之后它成为了一个核心功能！

![image-20220320210422039](https://s2.loli.net/2022/03/20/bsAqVlJzX4MTGRf.png)



实体类

```java
//这个注解的意思，就是说明这个类被Spring接管了，注册到了容器中
@Component
public class User {
   private String name;

   public String getName() {
      return name;
   }

   @Value("张三")
   public void setName(String name) {
      this.name = name;
   }

   @Override
   public String toString() {
      return "User{" +
            "name='" + name + '\'' +
            '}';
   }
}
```

配置文件

```java
//这个也会被Spring容器托管，因为它本身就是一个组件(@Component)
//@Configuration代表这是一个配置类，就和我们之前看的beans.xml一致
@Configuration
//@Import(类的class文件)   引入到这个配置类中
public class Config {

   //注册一个bean，就相当于我们写的一个bean标签
   //这个方法的名字就相当与bean标签中的id属性
   //这个方法的返回值，就相当于bean标签中的class属性
   @Bean
   public User myUser(){
      return new User(); //就是返回要注入到bean的对象！
   }
}
```

测试类

```java
public class MyTest {
   @Test
   public void test(){
      //如果完全使用了配置类方式去做，我们就只能通过 AnnotationConfig 上下文来获取容器，通过配置类的class对象加载！
      ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
      User myUser = context.getBean("myUser",User.class);
      System.out.println(myUser.getName());
   }
}
```

这种纯java的配置方式，在SpringBoot中随处可见

## 10.代理模式

为什么要学习代理模式？因为这就是SpringAOP的底层！【SpringAOP 和 SpringMVC】

代理模式的分类：

* 静态代理

* 动态代理

    ![image-20220321090318667](https://s2.loli.net/2022/03/21/PYJqkH5tm3BxuIv.png)

### 10.1.静态代理

角色分析：

* 抽象角色：一般会使用接口或者抽象类来解决
* 真实角色：被代理的角色
* 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作
* 客户：访问代理对象的人！

代码步骤：

1. 接口

    ```java
    //租房
    public interface Rent {
       public void rent();
    }
    ```

2. 真实角色

    ```java
    //房东
    public class Host implements Rent{
    
       @Override
       public void rent() {
          System.out.println("房东要出租房！！");
       }
    }
    ```

3. 代理角色

    ```java
    public class Proxy implements Rent{
    
       //组合
       private Host host;
    
       public Proxy(){
    
       }
    
       public Proxy(Host host) {
          this.host = host;
       }
    
       @Override
       public void rent() {
          host.rent();
       }
    
       //看房
       public void seeHouse(){
          System.out.println("中介带你看房");
       }
    
       //收中介费
       public void fee(){
          System.out.println("收中介费");
       }
    
       //合同
       public void heTong(){
          System.out.println("签租赁合同");
       }
    }
    ```

4. 客户端访问代理角色

    ```java
    public class Client {
       public static void main(String[] args) {
          //房东要出租房子
          Host host = new Host();
          //代理，中介帮房东出租房子，但是呢？代理角色一b会有一些附属操作！
          Proxy proxy = new Proxy(host);
          //你不用面对房东，直接找中介
          proxy.rent();
       }
    }
    ```

代理模式的好处：

* 可以使真实角色的操作更加纯粹！不用去关注一些公共的业务
* 公共业务也就交给代理角色！实现业务的分工！
* 公共业务发生拓展的时候，方便集中管理！

缺点：

* 一个真实角色就会产生一个代理角色，代码量会翻倍~开发效率会变低

### 10.2.加深理解

代码：对应08-demo02；

![image-20220321165935734](https://s2.loli.net/2022/03/21/YJP3Lt8OQbT1rHM.png)

### 10.3.动态代理

* 动态代理和静态代理角色一样
* 动态代理的代理类是动态生成的，不是我们直接写好的！
* 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
    * 基于接口----JDK 动态代理【我们这里使用】
    * 基于类-----cglib
    * java字节码实现----javasist



需要了解两个类：Proxy：代理  InvocationHandler：调用处理程序

==invoke执行它真正执行的方法，得到代理类==

动态代理的好处：

* 可以使真实角色的操作更加纯粹！不用去关注一些公共的业务
* 公共业务也就交给代理角色！实现业务的分工！
* 公共业务发生拓展的时候，方便集中管理！
* 一个动态代理类代理的是一个接口，一班就是对应一类的业务
* 一个动态代理类可以代理多个类，只要是实现了同一个接口即可

## 11.AOP

### 11.1.什么是AOP

AOP（Aspect Oriented Programming）意为：面向切面编程，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生泛型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![image-20220321222036620](https://s2.loli.net/2022/03/21/eJTZrYyqPgQFUEm.png)

### 11.2. Aop在Spring中的作用

==提供声明式事务；允许用户自定义切面==

* 横切关注点：跨越应用程序多个模块的方法和功能。即是，与我们的业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志，安全，缓存，事务等等....
* 切面（ASPECT）：横切关注点 被模块化 的特殊对象。即，它是一个类。
* 通知（Advice）：切面必须要完成的工作。即，它是类中的一个方法。
* 目标（Target）：被通知对象。
* 代理（Proxy）：向目标对象应用通知之后创建的对象。
* 切入点（PointCut）：切面通知 执行的 “ 地点 ” 的定义。
* 连接点（JoinPoint）：与切入点匹配的执行点。

![image-20220321223108423](https://s2.loli.net/2022/03/21/weHY6ubD8Xi2Ldy.png)

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Advice：

![image-20220321223222949](https://s2.loli.net/2022/03/21/GJOdBqLNDpjsgYz.png)

即 AOP 在不改变原有代码的情况下，去增加新的功能。

### 11.3.使用Spring实现AOP

【重点】使用AOP织入，需要导入一个依赖包！

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>
```



#### 方式一：使用spring的API接口【主要是SpringAPI接口实现】

1. 创建userService接口和userServiceImpl

    ```java
    public interface UserService {
    	public void add();
    	public void update();
    	public void delete();
    	public void select();
    
    }
    
    public class UserServiceImpl implements UserService{
       @Override
       public void add() {
          System.out.println("增加了一个用户");
       }
    
       @Override
       public void update() {
          System.out.println("更新了一个用户");
       }
    
       @Override
       public void delete() {
          System.out.println("删除了一个用户");
       }
    
       @Override
       public void select() {
          System.out.println("查询了一个用户");
       }
    }
    ```

2. 创建前置通知log和后置通知AfterLog

    ```java
    import org.springframework.aop.MethodBeforeAdvice;
    
    import java.lang.reflect.Method;
    
    public class log implements MethodBeforeAdvice {
    
       /**
        * method: 要执行的目标对象的方法
        * args: 参数
        * target: 目标参数
        * @Param: [method, args, target]
        * @return: void
        * @Date: 2022-03-21
        */
       @Override
       public void before(Method method, Object[] args, Object target) throws Throwable {
          System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了");
       }
    }
    
    import org.springframework.aop.AfterReturningAdvice;
    
    import java.lang.reflect.Method;
    
    public class AfterLog implements AfterReturningAdvice {
    
    	/**
    	 * 后置返回
    	 * returnValue: 返回值
    	 * @Param: [returnValue, method, args, target]
    	 * @return: void
    	 * @Date: 2022-03-21
    	 */
    	@Override
    	public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
    		System.out.println("执行了"+method.getName()+"方法，返回结果为: "+returnValue);
    	}
    }
    ```

3. 编写applicationContext.xml配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop
            https://www.springframework.org/schema/aop/spring-aop.xsd">
    
        <!--注册bean-->
        <bean id="userService" class="com.lian.service.UserServiceImpl"/>
        <bean id="log" class="com.lian.log.log"/>
        <bean id="afterLog" class="com.lian.log.AfterLog"/>
    
        <!--配置AOP:需要导入aop的约束-->
        <aop:config>
            <!--切入点 expression: 表达式,execution(要执行的位置！* * * * *)-->
            <aop:pointcut id="pointcut" expression="execution(* com.lian.service.UserServiceImpl.*(..))"/>
    
            <!--执行环绕增加！-->
            <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
            <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
        </aop:config>
    
    </beans>
    ```

4. 测试

    ```java
    public class MyTest {
       @Test
       public void test(){
          ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
          //动态代理代理的是接口：注意点
          UserService userService = context.getBean("userService", UserService.class);
          userService.add();
       }
    }
    ```

#### 方式二：使用自定义类实现【主要是切面定义】

自定义通知

```java
public class DiyPointCut {
   public void before(){
      System.out.println("================方法执行前=========");
   }
   public void after(){
      System.out.println("================方法执行后=========");
   }
}
```

```xml
<!--方式二：自定义类-->
<bean id="diy" class="com.lian.diy.DiyPointCut"/>

<aop:config>
    <!--自定义切面 ref要引用到类-->
    <aop:aspect ref="diy">
        <!--切入点-->
        <aop:pointcut id="point" expression="execution(* com.lian.service.UserServiceImpl.*(..))"/>
        <!--通知-->
        <aop:before method="before" pointcut-ref="point"/>
        <aop:after method="after" pointcut-ref="point"/>
    </aop:aspect>
</aop:config>
```

测试

```java
public class MyTest {
   @Test
   public void test(){
      ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
      //动态代理代理的是接口：注意点
      UserService userService = context.getBean("userService", UserService.class);
      userService.add();
   }
}
```

#### 方式三：使用注解实现

创建通知类

```java
@Component
@Aspect //标注这个类是一个切面
public class AnnotationPointCut {
   @Before("execution(* com.lian.service.UserServiceImpl.*(..))")
   public void before(){
      System.out.println("================方法执行前=========");
   }
   @After("execution(* com.lian.service.UserServiceImpl.*(..))")
   public void after(){
      System.out.println("================方法执行后=========");
   }
   //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点
   @Around("execution(* com.lian.service.UserServiceImpl.*(..))")
   public void around(ProceedingJoinPoint joinPoint) throws Throwable {
      System.out.println("环绕前");

      //获得签名
      Signature signature = joinPoint.getSignature();
      System.out.println("signature:"+signature);

      //执行方法
      Object proceed = joinPoint.proceed();
      System.out.println("环绕后");
   }
}
```

```xml
<bean id="userService" class="com.lian.service.UserServiceImpl"/>
<bean id="log" class="com.lian.log.log"/>
<bean id="afterLog" class="com.lian.log.AfterLog"/>

<context:annotation-config/>
<context:component-scan base-package="com.lian.diy"/>
<aop:aspectj-autoproxy/>
```

测试

```java
public class MyTest {
   @Test
   public void test(){
      ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
      //动态代理代理的是接口：注意点
      UserService userService = context.getBean("userService", UserService.class);
      userService.add();
   }
}
```

## 12.整合Mybatis

步骤：

1. 导入jar包

    * junit

        ```xml
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.1</version>
            <scope>test</scope>
        </dependency>
        ```

    * mybatis

        ```xml
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>
        ```

    * mysql数据库

        ```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        ```

    * spring相关的

        ```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        
        <!--Spring 操作数据库的话，还需要一个spring-jdbc-->
        <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-jdbc</artifactId>
          <version>5.1.9.RELEASE</version>
        </dependency>
        ```

    * aop织入

        ```xml
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>
        ```

    * mybatis-spring【new】

        ```xml
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>
        ```

2. 编写配置文件

3. 测试

### 12.1.回忆mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写mapper.xml

### 12.2.mybatis-Spring

#### ==**spring无法注入接口，只能注入接口的实现类，再通过set的方式将sqlSessionTemplate注入到实现类**==

#### 方式一：

1. 编写数据源

    ```xml
    <!--DataSource: 使用Spring的数据源替代Mybatis的配置  c3p0 dbcp  druid
        我们使用Spring提供的JDBC： org.springframework.jdbc.datasource
    -->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3308/mybatis?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=GMT%2B8&amp;useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="111111"/>
    </bean>
    ```

2. sqlSessionFactory

    ```xml
    <!--SqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource"/>
        <!--绑定mybatis配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/lian/mapper/*.xml"/>
    </bean>
    ```

3. sqlSessionTemplate

    ```xml
    <!--SqlSessionTemplate:就是我们使用的sqlSession-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--只能使用构造器注入sqlSessionFactory，没有set方法-->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>
    ```

4. 需要给接口加实现类

    ```java
    public interface UserMapper {
       public List<User> selectUser();
    }
    
    public class UserMapperImpl implements UserMapper{
    
    	//我们的所有操作都使用SqlSession来执行，现在使用SqlSessionTemplate;
    	private SqlSessionTemplate sqlSession;
    
    	public void setSqlSession(SqlSessionTemplate sqlSession) {
    		this.sqlSession = sqlSession;
    	}
    
    	@Override
    	public List<User> selectUser() {
    		UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    		return mapper.selectUser();
    	}
    }
    ```

5. 将自己写的实现类，注入到Spring中

    ```xml
    <import resource="spring-dao.xml"/>
    
    <bean id="userMapper" class="com.lian.mapper.UserMapperImpl">
        <property name="sqlSession" ref="sqlSession"/>
    </bean>
    ```

6. 测试使用即可

    ```java
    @Test
    public void test() throws Exception {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    
       UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
       List<User> userList = userMapper.selectUser();
       for (User user : userList) {
          System.out.println(user);
       }
    }
    ```

#### 方式二：

1. mapper实现类继承SqlSessionDaoSupport

    ```java
    public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {
       @Override
       public List<User> selectUser() {
          return getSqlSession().getMapper(UserMapper.class).selectUser();
       }
    }
    ```

2. SqlSessionDaoSupport需要注入sqlSessionFactory

    ```xml
    <bean id="userMapper2" class="com.lian.mapper.UserMapperImpl2">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
    ```

3. 测试

    ```java
    @Test
    public void test() throws Exception {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    
       UserMapper userMapper = context.getBean("userMapper2", UserMapper.class);
       List<User> userList = userMapper.selectUser();
       for (User user : userList) {
          System.out.println(user);
       }
    }
    ```

![image-20220323231112445](https://s2.loli.net/2022/03/23/WkX4LCKqb1VB7dG.png)

## 13.声明式事务

### 1.回顾事务

* 把一组业务当成一个业务来做; 要么都成功，要么都失败！
* 事务在项目开发中，十分的重要。涉及到数据的一致性问题，不能马虎！
* 确保完整性和一致性

事务的ACID原则：

* 原子性
* 一致性
* 隔离性
    * 多个业务可能操作同一个资源，防止数据损坏
* 持久性
    * 事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化的写道存储器中

### 2.spring中的事务管理

* 声明式事务：AOP

    ```xml
    <!--配置声明式事务-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="datasource"/>
    </bean>
    
    <!--结合aop实现事务的织入-->
    <!--配置事务通知:-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--给那些方法配置事务
            配置事务的传播特性: new  propagation
        -->
        <tx:attributes>
            <!--name与mapper接口中方法名相同-->
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="query" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>
    
    <!--配置事务切入-->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* com.lian.mapper.UserMapper.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>
    ```

    ![image-20220324142937862](https://s2.loli.net/2022/03/24/M8WuxDXeZSjfr3B.png)

* 编程式事务：需要在代码中，进行事务的管理

思考：

为什么需要事务？

* 如果不配置事务，可能存在数据提交不一致的情况下；
* 如果我们不在spring中去配置声明式事务，我们就需要在代码中手动配置事务！
* 事务在项目开发中十分重要，涉及到数据的一致性和完整性问题，不容马虎！





