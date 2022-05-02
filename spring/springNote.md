	在实际的开发中，服务器端应用程序通常采用三层体系架构，分别为表现层(web)、业务逻辑层(service)、持久层(dao)。

Spring 致力于 Java EE 应用各层的解决方案，对每一层都提供了技术支持。

- 在表现层提供了对 Spring MVC、Struts2 等框架的整合；
- 在业务逻辑层提供了管理事务和记录日志的功能；
- 在持久层还可以整合 MyBatis、Hibernate 和 JdbcTemplate 等技术，对数据库进行访问。

#### Spring体系结构

![image-20220210165038507](https://s2.loli.net/2022/02/10/ZCo5mhcNbfwsliI.png)

###### 1.Data Access/Integration (数据访问/集成)

​	数据访问/集成层包括 JDBC、ORM、OXM、JMS 和 Transactions 模块。

* JDBC 模块：提供了一个 JDBC 的样例模板，使用这些模板能消除传统冗长的 JDBC 编码还有必须的事务控制，而且能享受到 Spring 管理事务的好处。
* ORM 模块：提供与流行的 “对象-关系” 映射框架无缝集成的 API，包括 JPA、JDO、Hibernate 和 MyBatis 等。而且还可以使用 Spring 事务管理，无需额外控制事务。
* OXM 模块：提供了一个支持 Object/XML 映射的抽象层实现，如 JAXB、Castor、XMLBeans、JiBX 和 XStream。将 Java 对象映射成 XML 数据，或者将 XML 数据映射成 Java 对象。
* JMS 模块：指 Java 消息服务，提供一套 “消息生产者、消息消费者” 模板用于更加简单的使用 JMS，JMS 用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信。
* Transactions 事务模块：支持编程和声明式事务管理。

###### 2.Web 模块

​	Spring 的 Web 层包括 Web、Servlet、WebSocket 和 Portlet 组件。

* Web 模块：提供了基本的 Web 开发集成特性，例如多文件上传功能、使用 Servlet 监听器的 IOC 容器初始化以及 Web 应用上下文。
* Servlet 模块：提供了一个 Spring MVC Web 框架实现。Spring MVC 框架提供了基于注解的请求资源注入、更简单的数据绑定、数据验证等及一套非常易用的 JSP 标签，完全无缝与 Spring 其他技术协作。
* WebSocket 模块：提供了简单的接口，用户只要实现响应的接口就可以快速的搭建 WebSocket Server，从而实现双向通讯。
* Portlet 模块：提供了在 Portlet 环境中使用 MVC 实现，类似 Web-Servlet 模块的功能。

###### 3.Core Container （Spring 的核心容器）

​	Spring 的核心容器是其他模块建立的基础，由 ==Beans 模块、Core 核心模块、Context 上下文模块和 SpEL 表达式语言模块==组成，没有这些核心容器，也不可能有 AOP、Web 等上层的功能。具体介绍如下。

- Beans 模块：提供了框架的基础部分，包括控制反转和依赖注入。
- Core 核心模块：封装了 Spring 框架的底层部分，包括资源访问、类型转换及一些常用工具类。
- Context 上下文模块：建立在 Core 和 Beans 模块的基础之上，集成 Beans 模块功能并添加资源绑定、数据验证、国际化、Java EE 支持、容器生命周期、事件传播等。ApplicationContext 接口是上下文模块的焦点。
- SpEL 模块：提供了强大的表达式语言支持，支持访问和修改属性值，方法调用，支持访问及修改数组、容器和索引器，命名变量，支持算数和逻辑运算，支持从 Spring 容器获取 Bean，它也支持列表投影、选择和一般的列表聚合等。

###### 4. AOP、Aspects、Instrumentation 和 Messaging

​	在 Core Container 之上是 AOP、Aspects 等模块。

* AOP 模块：提供了面向切面编程实现，提供比如日志记录、权限控制、性能统计等通用功能和业务逻辑分离的技术，并且能动态的把这些功能添加到需要的代码中，这样各司其职、降低业务逻辑和通用功能的耦合。
* Aspects 模块：提供与 AspectJ 的集成，是一个功能强大且成熟的面向切面编程 (AOP) 框架。
* Instrumentation 模块：提供了类工具的支持和类加载器的实现，可以在特定的应用服务器中使用。
* Messaging 模块：Spring 4.0 以后增加了消息（Spring-messaging）模块，该模块提供了对消息传递体系结构和协议的支持。

###### 5. Test 模块

​	Test 模块：Spring 支持 Junit 和 TestNG 测试框架，而且还额外提供了一些基于 Spring 的测试功能，比如在测试 Web 框架时，模拟 Http 请求的功能。

Spring 包结构

| 名称 | 作用                                                         |
| :-------------: | ------------- |
| docs  | 包含 Spring 的 API 文档和开发规范  |
| libs   | 包含开发需要的 jar 包和源码包 |
| schema | 包含开发所需要的 schema 文件，在这些文件中定义了 Spring 相关配置文件的约束 |

libs 目录：

| 名称 | 作用 |
| :----------- | :----------- |
| spring-core-x.x.xx.jar       | 包含 Spring 框架基本的核心工具类，Spring 其他组件都要用到这个包中的类，是其他组件的基本核心。 |
| spring-beans-x.x.xx.jar      | 所有应用都要用到的，它包含访问配置文件、创建和管理 Bean 以及进行 Inversion of Control（IoC）或者 Dependency Injection（DI）操作相关的所有类。 |
| spring-context-x.x.xx.jar    | Spring 提供在基础 IoC 功能上的扩展服务，此外还提供许多企业级服务的支持，如邮件服务、任务调度、JNDI 定位、EJB 集成、远程访问、缓存以及各种视图层框架的封装等。 |
| spring-expression-x.x.xx.jar | 定义了 Spring 的表达式语言。    需要注意的是，在使用 Spring 开发时，除了 Spring 自带的 JAR 包以外，还需要一个第三方 JAR 包 commons.logging 处理日志信息。 |

==创建 ApplicationContext 对象时使用了 ClassPathXmlApplicationContext 类，这个类用于加载 Spring 配置文件、创建和初始化所有对象（Bean）。==

ApplicationContext.getBean() 方法用来获取 Bean，该方法返回值类型为 Object

```java
ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
```

![image-20220210210723702](https://s2.loli.net/2022/02/10/KLh5HEOuAMkwqiP.png)	bean.xml 文件中的 property 标签下的 name 属性需与Bean对象中的属性名一致，value  值为注入到 Bean 对象下匹配到的属性。一个 Bean 对象一个 bean 标签，一个属性一个 property 标签。

#### Spring IoC （控制反转）

​	IoC 是 Inversion of Control 的简写，它不是一门技术，而是一种设计思想，是一个重要的面向对象编程法则。

​	Spring 通过 IoC 容器来管理所有 Java 对象的实例化和初始化，控制对象之间的依赖关系。我们将由 IoC 容器管理的 Java 对象称为 Spring Bean，它与使用关键字 new 创建的 Java 对象没有任何区别。

​	调用者掌握着被调用者对象创建的控制权。

​	但在 Spring 应用中，Java 对象创建的控制权是掌握在 IoC 容器手里，大致步骤：

​		1.开发人员通过 XML 配置文件、注解、Java配置类等方式，对 Java 对象进行定义，例如在 XML 配置文件中使用 <bean> 标签、在 Java 类上使用 @Component 注解等。

​		2.Spring 启动时，IoC 容器会自动根据对象定义，将这些对象创建并管理起来。这些被IoC 容器创建并管理的对象称为 Spring Bean。

​		3.当我们想要使用某个 Bean 时，可以直接从 IoC 容器中获取（例如通过 ApplicationContext 的 getBean() 方法），而不需要手动通过代码（例如 new Object() 的方式）创建。

​	原本调用者是主动的一方，想要什么，就自己创建；但在 Spring 应用中，IoC 容器掌握着主动权，调用者则变成了被动的一方，被动的等待 IoC 容器创建它所需要的对象(Bean)。这个过程在职责层面发生了控制权的反转，把原来调用者通过代码实现的对象的创建，反转给 IoC 容器来帮忙实现，因此我们将这个过程成为 Spring 的 “控制反转”。

#### 依赖注入（DI）

​	依赖注入（Dependency Injection，DI）使用 ”依赖注入“ 来替代 “控制反转”。

​	简单来说，依赖关系就是在一个对象中使用到另一个对象，即==<span style="color:red;">对象中存在一个属性，该对象时另一个类的对象。</span>==

```java
public class B {    
    String bid;
    A a;
}
```

​	B 中存在一个 A 类型的对象属性 a，此时我们就可以说 B 对象依赖于对象 a。而依赖注入就是基于这种 “依赖关系” 而产生的。

​	==控制反转核心思想就是由 Spring 负责对象的创建。在对象创建的过程中，Spring 会自动的根据依赖关系，将它依赖的对象注入到当前对象中，这就是所谓的 “依赖注入”。==

​	依赖注入的本质是 Spring Bean 属性注入的一种。只不过这个属性是对象属性而已。

#### IoC 容器的两种实现

​	IoC 思想基于 IoC容器实现，IoC容器底层其实就是一个 Bean 工厂。Spring 框架为我们提供了了两种不同类型 IoC容器：BeanFactory 和 ApplicationContext。

###### BeanFactory

​	IoC 容器的基本实现，Spring 提供的最简单的 IoC 容器，由org.springframework.beans.factory.BeanFactory 接口定义。

​	==BeanFactory 采用懒加载（lazy-load）机制，容器在加载配置文件时并不会立刻创建 Java 对象，只有程序中获取（使用）这个对对象时才会创建。==

> ​	注意：BeanFactory 是 Spring 内部使用接口，通常情况下不提供给开发人员使用。 

###### ApplicationContext

​	ApplicationContext 是 BeanFactory 接口的子接口，是它的拓展。增加了 AOP(面向切面编程)、国际化、事务支持等。

常用的实现类：

| 实现类                          | 描述                                                         | 示例代码                                                     |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ClassPathXmlApplicationContext  | 加载类路径 ClassPath 下指定的 XML 配置文件，并完成 ApplicationContext 的实例化工作 | ApplicationContext applicationContext = new ClassPathXmlApplicationContext(String configLocation); |
| FileSystemXmlApplicationContext | 加载指定的文件系统路径中指定的 XML 配置文件，并完成 ApplicationContext 的实例化工作 | ApplicationContext applicationContext = new FileSystemXmlApplicationContext(String configLocation); |

> ​	在上表的示例代码中，参数 configLocation 用于指定 Spring 配置文件的名称和位置，如 Beans.xml。

#### Spring Bean 属性注入

​	所谓 Bean 属性注入，简单点说就是将属性注入到 Bean 中的过程，而这属性既可以是普通属性，也可以是一个对象（Bean）。

​	Spring 主要通过一下两种方式实现属性注入：

* 构造函数注入
* setter 注入（又称设值注入）

###### 构造函数注入

​	通过 Bean 的带参构造函数，实现 Bean 的属性注入。

使用构造函数实现属性注入大致步骤：

 	1. 在 Bean 中添加一个有参构造函数，构造函数内的每一个参数代表一个需要注入的属性；
 	2. 在 Spring 的 XML 配置文件中，通过 <beans> 及其子元素 <bean> 对 Bean 进行定义；
 	3. 在 <bean> 元素内使用 <constructor-arg> 元素，对其构造函数内的属性进行赋值，Bean 的构造函数内有多少参数，就需要使用多少个 <constructor-arg> 元素。

```java
private static final Log LOGGER = LogFactory.getLog(BeanTest.class);

@Test
public void MainApp(){
   //获取ApplicationContext容器
   ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml");
   //获取名为 Student 的 Bean
   Student student = applicationContext.getBean("student", Student.class);
   //通过日志打印学生信息
   LOGGER.info(student.toString());
}
```

![image-20220211231612695](https://s2.loli.net/2022/02/11/dZvSNq5VQUiRTCf.png)

###### setter 注入

​	通过 Bean 的 setter 方法，将属性注入到 Bean 的属性中。

​	==在 Spring 的实例化 Bean 的过程中，IoC 容器首先会调用默认的构造方法（无参构造方法）实例化 Bean（Java 对象），然后通过 Java 的反射机制调用这个 Bean 的 setXxx() 方法，将属性注入到 Bean 中。==

​	使用 setter 注入的方式进行属性注入：

	1. 在 Bean 中提供一个默认的无参构造函数（在没有其他带参构造函数的情况下，可省略），并为所有需要注入的属性提供一个 setXxx() 方法；
	1. 在 Spring 的 XML 配置文件中，使用 <beans> 及其子元素 <bean> 对 Bean 进行定义；
	1. 在 <bean> 元素内使用 <property> 元素对各个属性进行赋值。

![image-20220211233110114](https://s2.loli.net/2022/02/11/AmzyGwthBgoHeCq.png)

###### 短命名空间注入

​	通过构造函数或 setter 方法进行属性注入时，通常是在 <bean> 元素中嵌套 <property> 和 <constructor-arg> 元素来实现的。

​	Spring 框架提供了 2 种短命名空间，可以简化 Spring 的 XML 配置


| 短命名空间 | 简化的 XML 配置             | 说明                                     |
| :--------: | :------------------------------------: | :--------------------------------------: |
| p 命名空间 | <bean> 元素中嵌套的 <property> 元素    | 是 setter 方式属性注入的一种快捷实现方式 |
| c 命名空间 | <bean> 元素中嵌套的 <constructor> 元素 | 是构造函数属性注入的一种快捷实现方式     |

__p 命名空间注入（setter 方式注入）__

​	首先我们需要在配置文件的 <beans> 元素中导入一下 XML 约束。

> 1. xmlns:p="http://www.springframework.org/schema/p"

​	在导入 XML 约束后，我们就能通过以下形式实现属性注入。

> 2. ```xml
>     <bean id="Bean 唯一标志符" class="包名+类名" p:普通属性="普通属性值" p:对象属性-ref="对象的引用">
>     ```

使用 p 命名空间注入依赖时，必须注意：

* Java 类中必须有 setter 方法；
* Java 类中必须有无参构造器（类中不包含任何带参构造函数的情况，无参构造函数默认存在）。
* 在使用 p 命名空间实现属性注入前，XML 配置的 <beans> 元素内必须先导入 p 命名空间的 XML 约束。

```xml
<bean id="dept" class="com.lianzhy.pojo.Dept" p:deptName="技术部" p:deptNo="11"></bean>
<bean id="employee" class="com.lianzhy.pojo.Employee" p:dept-ref="dept" p:empName="小李" p:empNo="10"></bean>
```

__c 命名空间注入（构造函数注入）__

​	首先我们需要在配置文件的 <beans> 元素中导入一下 XML 约束。

> 1. xmlns:c="http://www.springframework.org/schema/c"

​	在导入 XML 约束后，我们就能通过以下形式实现属性注入。

> 2. ```xml
>     <bean id="Bean 唯一标志符" class="包名+类名" c:普通属性="普通属性值" c:对象属性-ref="对象的引用">
>     ```

使用 c 命名空间注入依赖时，需注意：

* Java 类中必须包含对应的带参构造器；
* 在使用 c 命名空间实现属性注入前，XML 配置的 <beans> 元素内必须先导入 c 命名空间的 XML 约束。

```xml
<bean id="employee" class="net.biancheng.c.Employee" c:empName="小胡" c:dept-ref="dept" c:empNo="999"></bean>    
<bean id="dept" class="net.biancheng.c.Dept" c:deptNo="2222" c:deptName="测试部"></bean>
```



![image-20220212200633833](https://s2.loli.net/2022/02/12/sVnK3h1ZD5Uopw2.png)

#### Spring 注入内部Bean

​	我们将定义在 <bean> 元素的 <property> 或 <construtor-arg> 元素内部的 Bean，成为内部 Bean。

__setter 方式注入内部 Bean__

```xml
<bean id="outerBean" class="……">       
	<property name="……" >            
        <!-- 定义内部 Bean --> 
        <bean class="……"> 
            <property name="……" value="……" ></property>      
            ……  
        </bean>
    </property>
</bean>
```

> 注意：内部 Bean 都是匿名的，不需要指定 id 和 name 的。即使制定了，IoC 容器也不会将它作为区分 Bean 的标识符，反而会无视 Bean 的 Scope 标签。因此内部 Bean 几乎总是匿名的，且总会随着外部的 Bean 创建。内部 Bean 是无法被注入到它所在的  Bean 以外的任何其他 Bean 的。

__构造函数方式注入内部 Bean__

```xml
<bean id="……" class="……">    
	<constructor-arg name="……">        
    	<!--内部 Bean-->
        <bean class="……">
        	<constructor-arg name="……" value="……"></constructor-arg>                 ……
        </bean>
    </constructor-arg>
</bean>
```

#### Spring 注入集合

​	在 Bean 标签下的 <property> 元素中，使用以下元素配置 Java 集合类型的属性和参数。

|  标签   |                             说明                             |
| :-----: | :----------------------------------------------------------: |
| <list>  |               用于注入 list 类型的值，允许重复               |
|  <set>  |              用于注入 set 类型的值，不允许重复               |
|  <map>  | 用于注入 key-value 的集合，其中 key 和 value 都可以是任意类型 |
| <props> | 用于注入 key-value 的集合，其中 key 和 value 都是字符串类型  |

__Spring 注入其他类型的属性__

​	通过使用 <null/> 元素将 Null 值注入到 Bean 中。

__Spring 注入空字符串__

​	Spring 会将属性中空参数直接当作空字符串来处理。

__注入字面量__

​	在 XML 配置中 “<”、“>”、“&” 等特殊字符是不能直接保存的，因此可以通过两种方式将包含特殊符号的属性注入到 Bean 中。

###### 转义

| 特殊字符 | 转义字符 |
| :------: | :------: |
|    &     |  \&amp;   |
|    <     |   \&lt;   |
|    >     |   \&gt;   |
|    ＂    |  \&quot;  |
|    ＇    |  \&apos;  |

> `<property name="propertyLiteral" value="&lt;C语言中文网&gt;"></property>`
>
> ` <C语言中文网>`

转义过程中，需要注意以下几点：

* 转义序列字符之间不能有空格；
* 转义序列必须以 “ ; ” 结束；
* 单独出现的 “ & ” 不会被认为是转义的开始；
* 区分大小写；

###### 使用短字符串 <![CDATA[]]>

​	通过短字符串 <![CDATA[]]> 将包含特殊符号的属性值包裹起来，让 XML 解析，以属性原本的样子注入到 Bean 中。

​	使用段字符串 <![CDATA[]]> 需要注意以下几点：

* 此部分不能在包含 “ ]]> ”；
* 不允许嵌套使用；
* “ ]]> ” 中不能包含空格或者换行；

> ` <value><![CDATA[<c.biancheng.net>]]></value>`
>
> ` <c.biancheng.net>`

__级联属性赋值__

​	我们可以在 <bean> 和 <property> 子元素中，为它所依赖的 Bean 的属性进行赋值，这就是 ”级联属性赋值“ 。

​	使用级联属性赋值时，需要注意以下三点：

* Java 类中必须有 setter 方法；
* Java 类中必须有无参构造器（默认存在）；
* 依赖其他 Bean 的类中，必须提供一个它依赖的 Bean 的 getXxx() 方法；

```xml
<!--注入依赖的 Bean-->
<property name="dependBean" ref="dependBean"></property>
<!--级联属性赋值-->
<property name="dependBean.dependProperty" value="级联属性赋值"></property>
```

#### Spring Bean 作用域

​	默认情况下，所有的 Spring Bean 都是单例的，也就是说在整个 Spring 应用中，Bean 实例只有一个。

​	通过在 <bean> 元素中添加 scope 属性来配置 Spring Bean 的作用范围。

|  作用范围   | 描述                                                         |
| :---------: | ------------------------------------------------------------ |
|  singleton  | 默认值，单例模式，表示在 Spring 容器中只有一个 Bean 实例     |
|  prototype  | 原型模式，表示每次通过 Spring 容器获取 Bean 时，容器都会创建一个新的 Bean 实例。 |
|   request   | 每次 HTTP 请求，容器都会创建一个 Bean 实例。该作用域只在当前 HTTP Request 内有效。 |
|   session   | 同一个 HTTP Session 共享一个 Bean 实例，不同的 Session 使用不同的 Bean 实例。该作用域仅在当前 HTTP Session 内有效。 |
| application | 同一个 Web 应用共享一个 Bean 实例，该作用域在当前 ServletContext 内有效。    与 singleton 类似，但 singleton 表示每个 IoC 容器中仅有一个 Bean 实例，而一个 Web 应用中可能会存在多个  IoC 容器，但一个 Web 应用只会有一个 ServletContext，也可以说 application 才是 Web  应用中货真价实的单例模式。 |
|  websocket  | websocket 的作用域是 WebSocket ，即在整个 WebSocket 中有效。 |

> 注意：在以上 6 种 Bean 作用域中，除了 singleton 和 prototype 可以直接在常规的 Spring IoC 容器（例如  ClassPathXmlApplicationContext）中使用外，剩下的都只能在基于 Web 的 ApplicationContext  实现（例如 XmlWebApplicationContext）中才能使用，否则就会抛出一个 IllegalStateException 的异常。

__Singleton__

​	Singleton 是 IoC 作用域容器的默认。

​	Spring IoC 容器中只会存在一个共享的 Bean 实例。这个 Bean 实例将存储在高速缓存中，所有对于这个 Bean 的请求和引用，只要 id 与这个 Bean 定义相匹配，都会返回这个缓存中的对象实例。

__Prototype__

​	Spring 容器会在每次请求该 Bean 时，都创建一个新的 Bean 实例。

> 从某种意义上说，Spring IoC 容器对于 prototype bean 的作用就相当于 Java 的 new 操作符。它只负责 Bean 的创建，至于后续的生命周期管理则都由客户端代码完成。

#### Spring Bean生命周期

​	传统 Java 应用中，Bean 的生民周期很简单，使用 Java 关键字 new 进行 Bean 的实例化后，这个 Bean 就可以使用了。一旦 这个 Bean 长期不被使用，Java 自动进行垃圾回收。

​	而 Spring 中 Bean 的生命周期大致分为：

	1. Bean 的实例化  
	1. Bean 属性赋值
	1. Bean 的初始化
	1. Bean 的使用
	1. Bean 的销毁

__Spring 生命周期流程__

![image-20220213205442760](https://s2.loli.net/2022/02/13/2BlVyK8WZdC4LzQ.png)

Bean 生命周期的整个执行过程描述如下：

 	1. Spring 启动，查找并加载需要被 Spring 管理的 Bean ，对 Bean 进行实例化。
 	2. 对 Bean 进行属性注入。
 	3. 如果 Bean 实现了 BeanNameAware 接口，则 Spring 调用 Bean 的 setBeanName() 方法传入当前 Bean 的 id 值。
 	4. 如果 Bean 实现了 BeanFactoryAware 接口，则 Spring 调用 setFactory() 方法传入当前工厂实例的引用。
 	5. 如果 Bean 实现了 ApplicationContextAware 接口，则 Spring 调用setApplicationContext() 方法传入当前 ApplicationContext 实例的引用。
 	6. 如果 Bean 实现了 BeanPostProcessor 接口，则 Spring 调用该接口的预初始化方法 postProcessBeforeInitialzation() 对 Bean 进行加工操作，此处非常重要，Spring 的 AOP 就是利用它实现的。
 	7. 如果 Bean 实现了 InitializingBean 接口，则 Spring 将调用 afterPropertiesSet() 方法。
 	8. 如果在配置文件中通过 init-method 属性指定了初始化方法，则调用该初始化方法。
 	9. 如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的初始化方法 postProcessAfterInitialization()。此时 Bean 已经可以被应用系统使用了。
 	10. 如果在 <bean> 中指定了该 Bean 的作用域为 singleton，则将该 Bean 放入 Spring IoC 的缓存池中，触发 Spring 对该 Bean 的生命周期管理；如果在 <bean> 中指定了该 Bean 的作用域为 proptotyoe ，则将该 Bean 交给调用者，调用者管理该 Bean 的生命周期，SPringle 不在管理该 Bean。
 	11. 如果在 Bean 实现了 DisposableBean 接口，则 Spring 会调用 destory() 方法销毁 Bean；如果在配置文件中通过 destory-method 属性指定了 Bean 的销毁方法，则 Spring 将调用该方法对 Bean 进行销毁。

__自定义 Bean 的生命周期__

​	我们可以在 Spring Bean 生命周期的某个特定时刻，指定一些生命周期回调方法完成一些自定义的操作，对 Bean 的生命周期进行管理。

Bean 的生命周期回调方法主要有两种：

* 初始化回调方法：在 Spring Bean 被初始化后调用，执行一些自定义的回调操作。
* 销毁回调方法：在 Spring Bean 被销毁前调用，执行一些自定义的回调操作。

我们可以通过 3 种方式定义自定义 Bean 的生命周期回调方法：

* 通过接口实现
* 通过 XML 配置实现
* 使用注解实现

==如果一个 Bean 中有多种生命周期回调方法时，优先级顺序为：注解 > 接口 > XML 配置。==

__通过接口实现__

​	通过实现 InitializingBean 和 DisposableBean 接口，指定 Bean 的生命周期回调方法。

|  回调方式  |       接口       |         方法         |                             说明                             |
| :--------: | :--------------: | :------------------: | :----------------------------------------------------------: |
| 初始化回调 | InitializingBean | afterPropertiesSet() | 指定初始化回调方法，这个方法会在 Spring Bean 被初始化后被调用，执行一些自定义的回调操作。 |
|  销毁回调  |  DisposableBean  |      destroy()       | 指定销毁回调方法，这个方法会在 Spring Bean 被销毁前被调用，执行一些自定义的回调操作。 |

> 注意：通常情况下，我们不建议通过这种方式指定生命周期回调方法，这是由于这种方式会导致代码耦合性过高。
