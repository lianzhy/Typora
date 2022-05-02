

## MyBatis

#### 1.简介

##### 1.1. 什么是MyBatis

* MyBatis 是一款优秀的**持久层框架**
* 它支持自定义 SQL、存储过程以及高级映射。
* MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
* MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old        Java Objects，普通老式 Java 对象）为数据库中的记录。



如何获取MyBatis？

* maven仓库：

    ```XML
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.6</version>
    </dependency>
    ```

* Github：https://github.com/mybatis/mybatis-3/releases/tag/mybatis-3.5.9

* 中文文档：https://mybatis.org/mybatis-3/zh/index.html

##### 1.2. 持久化

数据持久化

* 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
* 内存：**断电即失**
* 数据库(jdbc)，io文件持久化。

**为什么需要持久化？**

* 有一些对象，不能让他丢掉。
* 内存太贵

##### 1.3. 持久层

Dao层，Service层，Controller层.....

* 完成持久化工作的代码块
* 层界限十分明显。

##### 1.4. 为什么需要Mybatis？

* 帮助程序员将数据存入到数据库中。

* 方便

* 传统的JDBC代码太复杂。简化。框架。 

* 不用Mybatis也可以。更容易上手。
* 优点：
    - 简单易学
    - 灵活
    - sql和代码的分离，提高了可维护性。
    - 提供映射标签，支持对象与数据库的orm字段关系映射】
    - 提供对象关系映射标签，支持对象关系组建维护
    - 提供xml标签，支持编写动态sql。

#### 2. 第一个Mybatis程序

思路：搭建环境-->导入Mybatis-->编写代码-->测试！

##### 2.1. 搭建环境

搭建数据库

```java
CREATE DATABASE `mybatis`;

USE `mybatis`;

CREATE TABLE `user`(
	`id` INT(20) NOT NULL PRIMARY KEY,
	`name` VARCHAR(30) DEFAULT NULL,
	`pwd` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user`(`id`,`name`,`pwd`) VALUES
(1,'hj','123456'),
(2,'xhj','123456'),
(3,'zhj','123456');
```

新建项目(Maven)

1. 新建一个普通的maven项目

2. 删除src目录

3. 导入maven依赖

    ```xml
    <dependencies>
            <!--mysql驱动-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.47</version>
            </dependency>
            <!--mybatis-->
            <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.5.6</version>
            </dependency>
            <!--junit-->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
            </dependency>
        </dependencies>
    ```


##### 2.2. 创建模块

* 编写mybatis的配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3308/mybatis?useSSl=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="111111"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

* 编写mybatis工具类

```java
/** sqlSessionFactory --> sqlSession
 * @description: MybatisUtil
 * @author: llzyy
 * @create: 2022-03-05 14:54
 */
public class MybatisUtil {

   private static SqlSessionFactory sqlSessionFactory;

   static{
      try {
         //使用mybatis的第一步，获取sqlSessionFactory对象
         String resource = "Mybatis-config.xml";
         InputStream inputStream = Resources.getResourceAsStream(resource);
         SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
      } catch (IOException e) {
         e.printStackTrace();
      }
   }

   //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
   //SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
   /** 获取sqlSession对象
    * @Description:
    * @Param: []
    * @return: org.apache.ibatis.session.SqlSession
    * @Date: 2022-03-05
    */
   public static SqlSession getSalSession(){
      return sqlSessionFactory.openSession();

   }
}
```

##### 2.3. 编写代码

* 实体类

    ```java
    public class User {
    
       private int id;
       private String name;
       private String pwd;
    
       public User() {
       }
    
       public User(int id, String name, String pwd) {
          this.id = id;
          this.name = name;
          this.pwd = pwd;
       }
    
       public int getId() {
          return id;
       }
    
       public void setId(int id) {
          this.id = id;
       }
    
       public String getName() {
          return name;
       }
    
       public void setName(String name) {
          this.name = name;
       }
    
       public String getPwd() {
          return pwd;
       }
    
       public void setPwd(String pwd) {
          this.pwd = pwd;
       }
    
       @Override
       public String toString() {
          return "User{" +
                "id=" + id +
                ", name=" + name +
                ", pwd=" + pwd +
                '}';
       }
    }
    ```

* Dao接口

    ```java
    public interface UserDao {
       List<User> getUserList();
    }
    ```

* 接口实现类由原来的UserDaoImpl转变为一个Mapper配置文件.

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <!--namespace=绑定一个对应的Dao/Mapper接口-->
    <mapper namespace="com.lianzhy.dao.UserDao">
        <!--select查询语句-->
        <select id="getUserList" resultType="com.lianzhy.pojo.User">
            select * from mybatis.user
        </select>
    </mapper>
    ```

##### 2.4. 测试

Maven资源过滤问题(实现接口配置文件未在target中更新导致，手动添加或者在父子pom.xml以下代码)：

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

> org.apache.ibatis.binding.BindingException: Type interface com.lianzhy.dao.UserDao is not known to the MapperRegistry.
>
> ==Mapper 未在核心配置文件中注册==

```xml
<!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册！-->
<mappers>
    <mapper resource="com/lianzhy/dao/UserMapper.xml"></mapper>
</mappers>
```

##### MapperRegistry是什么？

核心配置文件中注册Mappers

* junit测试

```java
@Test
public void test(){
   //第一步，获得sqlSession对象
   SqlSession sqlSession = MybatisUtil.getSqlSession();
   
   //方式一，执行sql
   UserDao userDao = sqlSession.getMapper(UserDao.class);
   List<User> userList = userDao.getUserList();

   for (User user : userList) {
      System.out.println(user);
   }

   //关闭sqlSession
   sqlSession.close();
}
```

遇到的问题：

1. 配置文件未注册
2. 绑定接口错误
3. 方法名不对
4. 返回类型不对
5. maven导出资源问题

**SqlSessionFactoryBuilder** 一旦创建了SqlSessionFactoryBuilder，就不再需要它了，方法作用域

**SqlSessionFactory** 一旦创建就因该在应用的运行期间一直存在。

**SqlSession** 每次收到 HTTP 请求，就可以打开一个 SqlSession，返回一个响应后，就关闭它。          这个关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中。

### 3.CRUD

**namespace**：namespace中的包名要与Dao/mapper 接口的包名一致。

#### 3.1.select (选择查询语句)

* id：就是对应namespace中的方法名(接口中的方法);
* resultType：Sql语句执行的返回值！
* parameterType：参数类型！

1. 编写接口

    ```java
    List<User> getUserList();
    User getUserById(int id);
    int addUser(User user);
    int updateUser(User user);
    int deleteUser(int id);
    ```

2. 编写对应的mapper中的sql语句

    ```xml
    <select id="getUserList" resultType="com.lianzhy.pojo.User">
        select * from mybatis.user
    </select>
    <!--根据id查询用户-->
    <select id="getUserById" resultType="com.lianzhy.pojo.User" parameterType="int">
        select * from mybatis.user where id = #{id}
    </select>
    ```

3. 测试

    ```java
    @Test
    public void test(){
       //第一步，获得sqlSession对象
       SqlSession sqlSession = MybatisUtil.getSqlSession();
    
       try {
          //方式一，执行sql
          UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
          List<User> userList = userMapper.getUserList();
    
          for (User user : userList) {
             System.out.println(user);
          }
       }catch (Exception e){
          e.printStackTrace();
       }finally {
          //关闭sqlSession
          sqlSession.close();
       }
    }
    ```

#### 3.2.Insert

```xml
<!--添加用户-->
<!--对象中的属性可以直接取出来-->
<insert id="addUser" parameterType="com.lianzhy.pojo.User">
    insert into mybatis.user (id,name,pwd) values (#{id},#{name},#{pwd})
</insert>
```

#### 3.3.Update

```xml
<!--修改用户-->
<update id="updateUser" parameterType="com.lianzhy.pojo.User">
    update mybatis.user set name=#{name},pwd=#{pwd} where id=#{id};
</update>
```

#### 3.4.Delete

```xml
<!--删除用户-->
<delete id="deleteUser" parameterType="int">
    delete from mybatis.user where id=#{id};
</delete
```

> 注意：==增删改需要提交事务==

resources绑定mapper要用地址

==`<mapper resource="com/lianzhy/dao/UserMapper.xml"></mapper>`==

#### 3.5.万能的Map

实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map！！

==通过使用map键值对来改变实现类的配置文件中参数名字匹配的问题==

接口：`int addUser2(Map<String,Object> map)`

接口实现类配置文件：

```xml
<insert id="addUser2" parameterType="map">
    insert into mybatis.user (id,name,pwd) values (#{userid},#{username},#{password})
</insert>
```

测试代码：

```java
@Test
public void addUser2(){
   SqlSession sqlSession = MybatisUtil.getSqlSession();

   UserMapper mapper = sqlSession.getMapper(UserMapper.class);

   Map<String,Object> map = new HashMap<String,Object>();
   map.put("userid", 5);
   map.put("username", "王五");
   map.put("password", "123321");
   
   mapper.addUser2(map);

   sqlSession.commit();
   sqlSession.close();
}
```

Map传递参数，直接在sql中取出key即可！【parameterType="map"】

对象传递参数，直接在sql中取出对象的属性即可！【parameterType="Object"】

只有一个基本类型参数的情况下，可以直接在sql中取到！省略不写

多个 参数用Map，或者**注解**

#### 3.6.模糊查询

1. Java代码执行的时候，传递通配符 % %

`List<User> userList = userMapper.getUserLike("%hj%");`

1. 在sql拼接中使用通配符！

`select * from user where name like "%"#{value}"%"`

### 4.配置解析

#### 4.1.核心配置文件

* mybatis-config.xml
* MyBatis 的配置文件包含了 会深深影响 MyBatis 行为的设置和属性信息。

configuration（配置）          

- [properties（属性）](https://mybatis.net.cn/configuration.html#properties)

- [settings（设置）](https://mybatis.net.cn/configuration.html#settings)

- [typeAliases（类型别名）](https://mybatis.net.cn/configuration.html#typeAliases)

- [typeHandlers（类型处理器）](https://mybatis.net.cn/configuration.html#typeHandlers)

- [objectFactory（对象工厂）](https://mybatis.net.cn/configuration.html#objectFactory)

- [plugins（插件）](https://mybatis.net.cn/configuration.html#plugins)

- environments（环境配置）

    * environment（环境变量）

        - transactionManager（事务管理器）

        - dataSource（数据源）

- [databaseIdProvider（数据库厂商标识）](https://mybatis.net.cn/configuration.html#databaseIdProvider)

- [mappers（映射器）](https://mybatis.net.cn/configuration.html#mappers)

#### 4.2.环境配置（environments）

Mybatis 可以配置成适应多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

学会使用多套运行环境 `default="id"`

Mybatis默认的事务管理器就是JDBC ，连接池：POOLED

#### 4.3.属性(properties)

我们可以通过properties属性来实现引用配置文件

这些属性都是可外部配置且可动态替换的，既可在典型的 Java 属性文件中配置，亦可通过peoperties元素的子元素来传递。【db.properties】

![image-20220307222157786](https://s2.loli.net/2022/03/07/QcjNLmlz1sepHY8.png)

properties 标签需写在最上面

编写一个配置文件

db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3308/mybatis?characterEncoding=utf-8&amp;serverTimezone=UTC&amp;useSSL=false
username=root
password=111111
```

在核心配置文件中引入properties文件

```xml
<!--引入外部配置文件-->
<properties resource="db.properties">
    <property name="username" value="root"/>
    <property name="password" value="111111"/>
</properties>
```

* 可以直接引入外部文件
* 可以在其中增加一些属性
* 如果两个文件中有同一字段，优先使用外部配置文件的！！

#### 4.4.类型别名(typeAliases)

* 类型别名是为 java 类型设置一个短的名字。
* 仅在于用来减少类完全限定名的冗余。

```xml
<!--可以给实体类起别名-->
<typeAliases>
    <typeAlias type="com.lian.pojo.User" alias="User"/>
</typeAliases>
```

也可以指定一个包名，Mybatis 会在包名下面搜索需要的 java Bean，比如：

扫描实体类的包，它默认别名就为这个类的类名，首字母小写！

```xml
<!--可以给实体类起别名-->
<typeAliases>
    <package name="com.lian.pojo"/>
</typeAliases>
```

在实体类比较少的时候，使用第一种方式。如果实体类比较多的时候，使用第二种方式。

第一种可以自定义别名，第二种则不行，如果非要改，需要在实体类上增加注解。

```java
@Alias("user")
public class User {}
```

八大基本类型别名`_类型名` 它的包装类的别名为==基本类型名==

#### 4.5.设置（settings）

|       设置名       |                             描述                             |                            有效值                            | 默认值 |
| :----------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----: |
|    cacheEnabled    |    全局性地开启或关闭所有映射器配置文件中已配置的任何缓存    |                        true \| false                         |  true  |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 |                        true \| false                         | false  |
|      logImpl       |    指定 MyBatis 所用日志的具体实现，未指定时将自动查找。     | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |

#### 4.6.其他

* [typeHandlers（类型处理器）](https://mybatis.net.cn/configuration.html#typeHandlers)

* [objectFactory（对象工厂）](https://mybatis.net.cn/configuration.html#objectFactory)

* [plugins（插件）](https://mybatis.net.cn/configuration.html#plugins)
    * mybatis-generator-core
    * mybatis-plus
    * 通用mapper

#### 4.7.映射器（mappers）

MapperRegistry：注册绑定我们的Mapper文件。

方式一：

```xml
<mappers>
    <mapper resource="com/lian/dao/UserMapper.xml"></mapper>
</mappers>
```

方式二：使用class方式绑定

```xml
<mappers>
    <mapper class="com.lian.dao.UserMapper"></mapper>
</mappers>
```

> 注意：
>
> * 接口和它的配置文件必须同名！
> * 接口和他的Mapper配置文件必须在同一包下！

方式三：使用扫描包注入绑定

```xml
<mappers>
    <package name="com.lian.dao"/>
</mappers>
```

>  注意：
>
> * 接口和它的配置文件必须同名！
> * 接口和他的Mapper配置文件必须在同一包下！

#### 4.8.生命周期和作用域

![image-20220308125016528](https://s2.loli.net/2022/03/08/QGe6J7sn4LuTjPp.png)

生命周期，和作用域，是至关重要的，因为错误的使用会导致 非常严重的**并发问题**。

**SqlSessionFactoryBuilder：**

* 一旦创建了SqlSessionFactory，就不再需要它了
* 局部变量

**SqlSessionFactory**：

* 说白了就是可以想象为：数据库连接池
* SqlSessionFactory一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。
* 因此SqlSessionFactory的最佳作用域为应用作用域。
* 最简单的就是使用单例模式或者静态单例模式。

**SqlSession**

* 连接到连接池的一个请求！
* 用 完之后需要关闭，否则资源被占用！
* SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳作用域是请求或方法作用域。

![image-20220308130023393](https://s2.loli.net/2022/03/08/OuZLEU7eKP5vk6M.png)

这里面的每个Mapper，就代表一个具体的业务。

### 5.解决属性名和字段名不一致的问题

解决方法：

* 起别名

```xml
<select id="getUserById" resultType="User">
    select id,name,pwd as password from mybatis.user where id = #{id}
</select>
```

#### 5.1.resultMap

结果集映射

```xml
id name pwd
id name password
```

```xml
<!--根据id查询用户-->
<!--结果集映射-->
<resultMap id="UserMap" type="User">
    <!--column数据库中字段,property实体类中的属性-->
    <result column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="pwd" property="password"/>
</resultMap>
 
<select id="getUserById" resultMap="UserMap" parameterType="int">
    select * from mybatis.user where id = #{id}
</select>
```

* `resultMap` 元素是 MyBatis 中最重要最强大的元素
* ResultMap 的设计思想是，对于简单的语句根本不需要配置显示的结果集映射，而对于复杂一点的语句只需要描述他们的关系就行了。
* `ResultMap` 最优秀的地方在于，根本不需要显式的用到他们。

### 6.日志

#### 6.1.日志工厂

如果一个数据库操作，出现了异常，我们需要排错，日志就是最好的助手！

曾经：soutt、debug

现在：日志工厂！

![image-20220308163026988](https://s2.loli.net/2022/03/08/fxkALZmi89S5qho.png)

* SLF4J 
* LOG4J       【掌握】
* LOG4J2 
* JDK_LOGGING 
* COMMONS_LOGGING 
* STDOUT_LOGGING    【掌握】 
* NO_LOGGING              

在Mybatis具体使用哪一个日志实现，在设置中设定！

**STDOUT_LOGGING标准日志输出**

在mybatis核心配置文件中，配置我们的日志

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

![image-20220308180655825](https://s2.loli.net/2022/03/08/omfS1N7AecLWgXM.png)

#### 6.2.Log4j

什么是Log4j？

* Log4j是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是[控制台](https://baike.baidu.com/item/控制台/2438626)、文件、[GUI](https://baike.baidu.com/item/GUI)组件
* 我们也可以控制每一条日志的输出格式;
* 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。
* 通过一个[配置文件](https://baike.baidu.com/item/配置文件/286550)来灵活地进行配置，而不需要修改应用的代码。

1. 先导入Log4j包

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2. log4j.properties

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold = DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern = [%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File = ./log/log4j.log
log4j.appender.file.MaxFileSize = 10mb
log4j.appender.file.Threshold = DEBUG
log4j.appender.file.layout = org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern = [%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis = DEBUG
log4j.logger.java.sql = DEBUG
log4j.logger.java.sql.Statement = DEBUG
log4j.logger.java.sql.ResultSet = DEBUG
log4j.logger.java.sql.PreparedStatement = DEBUG
```

3. 配置log4j为日志的实现

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

4. Log4j的使用！直接测试运行

![image-20220309133913588](https://s2.loli.net/2022/03/09/VIkzgKNnyhXs3iQ.png)

**简单使用**

1. 在要使用 Log4j 的类中，导入包 import org.apache.log4j.Logger;
2. 日志对象，参数为当前类的 class 字节码文件；

```java
static Logger logger = Logger.getLogger(UserMapperTest.class);
```

3. 日志级别

```java
logger.info("info：进入了testLog4j");
logger.debug("debug：进入了testLog4j");
logger.error("error：进入了testLog4j");
```

### 7.分页

#### 7.1.使用Limit分页

`SELECT * from user limit startIndex,pageSize;`

`SELECT * from user limit 3;  #[0,n]`

使用Mybatis实现分页，核心SQL

1. 接口

```java
List<User> getUserByLimit(Map<String,Integer> map);
```

2. Mapper.xml

```xml
<!--分页查询-->
<select id="getUserByLimit" parameterType="map" resultMap="UserMap">
    select * from mybatis.user limit #{startIndex},#{pageSize}
</select>
```

3. 测试

```java
@Test
public void getUserByLimit(){
   SqlSession sqlSession = MybatisUtil.getSqlSession();
   UserMapper mapper = sqlSession.getMapper(UserMapper.class);
   Map<String,Integer> map = new HashMap<String,Integer>();
   map.put("startIndex",0);
   map.put("pageSize",2);

   List<User> userByLimit = mapper.getUserByLimit(map);
   for (User user : userByLimit) {
      System.out.println(user);
   }

   sqlSession.close();
}
```

#### 7.2.使用RowBounds分页

比在使用SQL实现分页

1. 接口

```
List<User> getUserByRowBounds();
```

2. mapper.xml

```xml
<select id="getUserByRowBounds" resultMap="UserMap">
    select * from mybatis.user
</select>
```

3. 测试

```java
@Test
public void getUserByRowBounds(){
   SqlSession sqlSession = MybatisUtil.getSqlSession();

   //RowBounds实现
   RowBounds rowBounds = new RowBounds(1, 2);

   //通过java代码实现分页
   List<User> userList = sqlSession.selectList("com.lian.dao.UserMapper.getUserByRowBounds",null,rowBounds);

   for (User user : userList) {
      System.out.println(user);
   }

   sqlSession.close();
}
```

#### 7.3. 分页插件

![image-20220309154614865](https://s2.loli.net/2022/03/09/6M3BOyn5FXwmRz7.png)

### 8.使用注解开发

#### 8.1.面向接口编程

#### 8.2.使用注解开发

1. 注解在接口上实现

```java
@Select("select * from user")
List<User> getUsers();
```

2. 需要在核心配置文件中绑定接口

```xml
<!--绑定接口-->
<mappers>
    <mapper class="com.lian.dao.UserMapper"/>
</mappers>
```

3. 测试

本质：反射机制实现

底层：动态代理！

![image-20220309162626882](https://s2.loli.net/2022/03/09/Z8sOErQh9AxuwH6.png)

**Mybatis详细的执行流程！**

![未命名文件](https://s2.loli.net/2022/03/09/kPlmwUvpg4CiI6W.png)

#### 8.3.注解的CRUD

我们可以在工具类创建的时候实现自动提交事务！

```java
public static SqlSession getSqlSession(){
   return sqlSessionFactory.openSession(true);
}
```

编写接口，增加注解

```java
public interface UserMapper {

   @Select("select * from user")
   List<User> getUsers();

   //方法存在多个参数，所有参数前面必须加上@param
   @Select("select * from user where id = #{id}")
   User getUserById(@Param("id") int id);

   @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{password})")
   int addUser(User user);

   @Update("update user set name=#{name},pwd=#{password} where id=#{id}")
   int updateUser(User user);

   @Delete("delete from user where id=#{uid}")
   int deleteUser(@Param("uid") int id);
}
```

测试类

> ==注意：我们必须要将接口注册到核心配置文件中==

**关于`@param()`注解**

* 基本类型的参数或者String类型，需要加上
* 引用类型不需要加
* 如果只有一个基本类型的话，可以忽略，但是建议加上！
* 我们在SQL中引用的就是我们这里设定的`@param()`中设定的属性名！



**#{}   ${}的区别**

- \#{}是预编译处理，$ {}是字符串替换。
- MyBatis在处理#{}时，会==将SQL中的#{}替换为?号==，使用==PreparedStatement的set方法来赋值==；MyBatis在处理 \$ { } 时，就是==把 ${ } 替换成变量的值==。
- 使用 #{} 可以有效的防止SQL注入，提高系统安全性。

### 9.Lombok

​	Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
​	Never write another getter or equals method again, with one  annotation your class has a fully featured builder, Automate your  logging variables, and much more. 		

* java library
* plugs
* build tools
* with one annotation your class



使用步骤：

1. zaiIDEA中安装Lombok插件！
2. 在项目中导入Lombok的jar包

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
</dependency>
```

3. 在实体类上加注解即可

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class User {
   @Getter@Setter
   private int id;
   private String name;
   private String password;
}
```

**@Getter and @Setter**
@FieldNameConstants
**@ToString**
**@EqualsAndHashCode**
**@AllArgsConstructor**, @RequiredArgsConstructor and **@NoArgsConstructor**
@Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
**@Data**                 @Builder                   @SuperBuilder                    @Singular
@Delegate         @Value     @Accessors      @Wither      @With     @SneakyThrows
@val      @var

说明：

```
@Data：无参构造，get、set、tostring、hashcode、equals
@AllArgConstructor
@NoArgConstructor
@EqualsAndHashCode
@ToString
@getter and @setter
```

### 10.多对一处理

多对一：

![image-20220309224914442](https://s2.loli.net/2022/03/09/gJefOYXoPL92hm3.png)

* 多个学生，对应一个老师
* 对于学生这边而言，关联 ...  多个学生，关联一个老师【多对一】
* 对于老师而言，集合，一个老师，有很多学生【一对多】

![image-20220310081353468](https://s2.loli.net/2022/03/10/PZfETGK3bigUnYk.png)

SQL：

```sql
CREATE TABLE `teacher` (
    `id` INT(10) NOT NULL,
    `name` VARCHAR(30) DEFAULT NULL,
    PRIMARY KEY (`id`)
)  ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO teacher(`id`,`name`) VALUES (1,'秦老师');
CREATE TABLE `student`(
	`id` INT(10) NOT NULL,
	`name`VARCHAR(30) DEFAULT NULL,
    `tid` INT(10) DEFAULT NULL,
    PRIMARY KEY (`id`),
	KEY `fktid` (`tid`),
	CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`) )  ENGINE=INNODB DEFAULT CHARSET=utf8
	
INSERT INTO `student` (`id`,`name`,`tid`) VALUES ('1','小明','1');
INSERT INTO `student` (`id`,`name`,`tid`) VALUES ('2','小红','1');
INSERT INTO `student` (`id`,`name`,`tid`) VALUES ('3','小张','1');
INSERT INTO `student` (`id`,`name`,`tid`) VALUES ('4','小李','1');
INSERT INTO `student` (`id`,`name`,`tid`) VALUES ('5','小王','1'); 
```

#### 测试环境搭建

	1. 导入lombok
	1. 新建实体类  Teacher，Student
	1. 建立Mapper接口
	1. 建立Mappe.xml文件
	1. 在核心配置文件中绑定注册我们的Mapper接口或者文件！【方式很多，随机选择】
	1. 测试查询是否成功！

#### 按照查询嵌套处理

```java
<mapper namespace="com.lian.dao.StudentMapper">

    <!--
    思路：
        1、查询所有的学生信息
        2、根据查询出来学生的id，寻找对应的老师  子查询
    -->
    <select id="getStudent" resultMap="StudentTeacher">
        select * from student
    </select>
    <resultMap id="StudentTeacher" type="Student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>
        <!--复杂的属性，我们需要单独处理  对象：association 集合：collection
        -->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
    </resultMap>
    <select id="getTeacher" resultType="Teacher">
        select * from teacher where id=#{id}
    </select>
</mapper>
```

#### 按照结果嵌套处理

```xml
<!--按照结果嵌套处理-->
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id sid,s.name sname,t.name tname
    from student s,teacher t
    where s.tid=t.id
</select>

<resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname"/>
    </association>
</resultMap>
```

回顾Mysql 多对一的方式

* 子查询
* 联表查询

### 11.一对多

比如：一个老师拥有多个学生

对于老师而言，就是一对多的关系！

1. 环境搭建

**实体类**

```java
@Data
public class Student {
   private int id;
   private String name;
   private int tid;
}
```

```java
@Data
public class Teacher {
   private int id;
   private String name;

   //一个老师有多个学生
   private List<Student> students;
}
```

**按结果查询**

```xml
<!--按结果嵌套查询-->
<select id="getTeacher" resultMap="TeacherStudent">
    select s.id sid,s.name sname,t.name tname,t.id tid
    from student s,teacher t
    where s.tid=#{tid} and t.id=#{tid}
</select>

<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <!--复杂的属性，我们需要单独处理  对象：association  集合：collection
        javaType  指定属性类型
        集合中的泛型信息，我们使用ofType获取
    -->
    <collection property="students" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```

**按嵌套查询**

```xml
<select id="getTeacher2" resultMap="TeacherStudent2">
    select * from mybatis.teacher where id=#{tid}
</select>
<resultMap id="TeacherStudent2" type="Teacher">
    <collection property="students" javaType="ArrayList" ofType="Student" select="getStudentByTeacherId" column="id"/>
</resultMap>
<select id="getStudentByTeacherId" resultType="Student">
    select * from mybatis.student where tid=#{tid}
</select>
```

**小结**

1. 关联 - association 【多对一】
2. 集合 - collection  【一对多】
3. javaType  & ofType
    * javaType  用来指定实体类中的 属性的类型
    * ofType   用来指定映射Llist或者集合中的pojo类型，泛型中的约束类型！

注意点：

* 保证SQL的可读性，尽量保证通俗易懂
* 注意一对多和多对一中，属性名和字段的问题！
* 如果问题不好排查错误，可以使用日志，建议使用Log4j



**慢SQL  1s    1000s**

面试高频

* Mysql引擎
* InnoDB底层原理
* 索引
* 索引优化

### 12.动态SQL

==**什么是动态SQL：动态SQL就是根据不同的条件生成不同的SQL语句**==

```
如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis  之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3  替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

if
choose (when, otherwise)
trim (where, set)
foreach
```

**搭建环境**

```SQL
CREATE TABLE `blog` (
	`id` varchar(50) NOT NULL COMMENT '博客id',
    `title` varchar(100) NOT NULL COMMENT '博客标题',
    `author` varchar(30) NOT NULL COMMENT '博客作者',
    `create_time` datetime NOT NULL COMMENT '创建时间',
	`views` int(30) NOT NULL COMMENT `浏览量`
)  ENGINE=InnoDB DEFAULT CHARSET=utf8
```

创建一个基础工程

1. 导包
2. 编写配置文件
3. 编写实体类

```java
@Data
public class Blog {
   private int id;
   private String title;
   private String author;
   private Date createTime;
   private int views;
}
```

4. 编写实体类对应Mapper接口和Mapper.xml配置文件

#### IF

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog where 1=1
    <if test="title != null">
    	and title = #{title}
    </if>
    <if test="author != null">
    	and author = #{author}
    </if>
</select>
```

#### choose (when, otherwise)

```xml
<select id="queryBlogChoose" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <choose>
            <when test="title != null">
                title = #{title}
            </when>
            <when test="author != null">
                AND author = #{author}
            </when>
            <otherwise>
                and views = #{views}
            </otherwise>
        </choose>
    </where>
</select>
```

#### trim (where, set)

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <if test="title != null">
            and title = #{title}
        </if>
        <if test="author != null">
            and author = #{author}
        </if>
    </where>
</select>
```

> ==where标签会解决在没条件的时候自动去掉where，以及条件前的 AND 和 OR 等，至少有一个子元素的条件才会插入“WHERE”==
>
> ==set元素会动态前置 SET 关键字，同时也会删掉无关的逗号，因为用了条件语句之后很可能就会在生成的 SQL 语句的后面留下这些逗号。==

```xml
<update id="updateBlog" parameterType="map">
    update mybatis.blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    where id = #{id}
</update
```

==**所谓的动态SQL，本质还是SQL语句，只是我们可以在SQL层面，去执行一个逻辑代码**==

#### sql片段

有的时候会将一些功能的部分抽取出来，方便复用！

1. 使用SQL标签抽取公共的部分

```xml
<sql id="if-title-author">
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</sql>
```

2. 在需要使用的地方使用Include标签引用即可

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <include refid="if-title-author"/>
    </where>
</select>
```

注意事项：

* 最好基于单表来定义SQL片段！
* 不要存在where标签



#### Foreach 

```sql
select * from user where 1=1 and 

  <foreach item="id" index="index" collection="ids"
      open="(" separator="or" close=")">
        #{id}
  </foreach>

(id=1 or id=2 or id=3)


```

![image-20220312155357909](https://s2.loli.net/2022/03/12/5Pqpz89xSciKjw6.png)

```xml
<select id="queryBlogForeach" parameterType="map" resultType="blog">
    select * from mybatis.blog

    <where>
        <foreach collection="ids" item="id" open="and ("
                 separator="or" close=")">
            id=#{id}
        </foreach>
    </where>
</select>
```

==动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了==

### 13.缓存

#### 13.1. 简介

```
查询   ： 连接数据库  ，耗资源！
	一次查询的结果，给他暂存在一个可以直接取到的地方！--> 内存  ： 缓存

我们可以再次查询相同数据的时候，直接走缓存，就不用走数据库了
```



1. 什么是缓存 [ Cache ]？

    * 存在内存中的临时数据。
    * 将用户经常查询的数据存放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。

    ![image-20220312163840306](https://s2.loli.net/2022/03/12/x1Jefb6GQmBnkRl.png)

2. 为什么使用缓存？
    * 减少和数据库的交互次数，减少系统开销，提高系统效率。

3. 什么样的数据能使用缓存？
    * 经常查询且不经常改变的数据。【可以使用缓存】

#### 13.2.Mybatis缓存

* Mybatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。
* Mybatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**
    * 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
    * 二级缓存需要手动开启和配置，它是基于namespace级别的缓存。
    * 为了提高拓展性，Mybatis定义了缓存接口Cache。我们可以通过实现Cache接口来实现自定义二级缓存


#### 13.3.一级缓存

* 一级缓存也叫本地缓存：
    * 与数据库同一次会话期查询到的数据会放在本地缓存中。
    * 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库；

测试步骤：

1. 开启日志！
2. 测试在一个Session中查询两次相同的记录
3. 查看日志输出

![image-20220313125341497](https://s2.loli.net/2022/03/13/jsCUk7OVMW56g13.png)

缓存失效的情况：

1. 查询不同的东西
2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存！

![image-20220313130452227](https://s2.loli.net/2022/03/13/BQyO19ntx6RXIlh.png)

3. 查询不同的Mapper.xml

4. 手动清理缓存

![image-20220313130653308](https://s2.loli.net/2022/03/13/kSG6sReNmW5l891.png)

小结：一级缓存默认开启，只在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段！

一级缓存就是Map。

#### 13.4.二级缓存

* 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存。
* 基于namespace级别的缓存，一个命名空间，对应一个二级缓存；
* 工作机制
    * 一个会话查询一条数据，这个数据就会被放在当前绘话的一级缓存中；
    * 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
    * 新的会话查询信息，就可以从二级缓存中获取内容；
    * 不同的mapper查出的数据会放在自己对应的缓存（map）中；

步骤：

1. 开启全局缓存

```xml
<!--显式的开启全局缓存-->
<setting name="cacheEnabled" value="true"/>
```

2. 在要使用二级缓存的Mapper中开启

```xml
<!--在当前Mapper.xml中使用二级缓存-->
<cache/>
```

​	也可以自定义参数

```xml
<!--在当前Mapper.xml中使用二级缓存-->
<cache
 eviction="FIFO"
 flushInterval="60000"
 size="512"
 readOnly="true"/>
```

3. 测试
    1. 问题：我们需要将实体类序列化！![image-20220313133705202](https://s2.loli.net/2022/03/13/32xZjJI1LmWOhC7.png)

小结：

* 只要开启了二级缓存，在同一个Mapper下就有效
* 所有的数据都会先放在一级缓存中；
* 只有当会话提交，或者关闭的时候，才会提交搭配二级缓存中！

#### 13.5.缓存原理

![image-20220314150405602](https://s2.loli.net/2022/03/14/c2bsmv9nMCAjDNW.png)

#### 13.6.自定义缓存-ehcache

Ehcache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存

要在程序中使用ehcache：

1. 先导入jar包

    ```xml
    <!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
    <dependency>
        <groupId>org.mybatis.caches</groupId>
        <artifactId>mybatis-ehcache</artifactId>
        <version>1.2.1</version>
    </dependency>
    ```

2. 编写配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
             updateCheck="false">
        <!--
           diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
           user.home – 用户主目录
           user.dir  – 用户当前工作目录
           java.io.tmpdir – 默认临时文件路径
         -->
        <diskStore path="java.io.tmpdir/Tmp_EhCache"/>
        <!--
           defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
         -->
        <!--
          name:缓存名称。
          maxElementsInMemory:缓存最大数目
          maxElementsOnDisk：硬盘最大缓存个数。
          eternal:对象是否永久有效，一但设置了，timeout将不起作用。
          overflowToDisk:是否保存到磁盘，当系统宕机时
          timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
          timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
          diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
          diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
          diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
          memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
          clearOnFlush：内存数量最大时是否清除。
          memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
          FIFO，first in first out，这个是大家最熟的，先进先出。
          LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
          LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
       -->
        <defaultCache
                eternal="false"
                maxElementsInMemory="10000"
                overflowToDisk="false"
                diskPersistent="false"
                timeToIdleSeconds="1800"
                timeToLiveSeconds="259200"
                memoryStoreEvictionPolicy="LRU"/>
    
        <cache
                name="cloud_user"
                eternal="false"
                maxElementsInMemory="5000"
                overflowToDisk="false"
                diskPersistent="false"
                timeToIdleSeconds="1800"
                timeToLiveSeconds="1800"
                memoryStoreEvictionPolicy="LRU"/>
    
    </ehcache>
    ```

3. 在Mapper.xml中导入

    ```xml
    <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
    ```



Redis数据库做缓存！！K-V



### 练习：24道练习题实战
