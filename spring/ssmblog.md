## 简单个人博客搭建

### 一.基本博客数据库编写

#### 1.1. sql语句编写

```sql
CREATE TABLE `articles` (
  `article_id` varchar(50) COLLATE utf8_croatian_ci NOT NULL COMMENT '博客id',
  `article_title` text CHARACTER SET latin1 COMMENT '博客标题',
  `article_content` longtext CHARACTER SET latin1 COMMENT '博客内容',
  `article_date` datetime DEFAULT NULL COMMENT '博客创建时间',
  `user_id` varchar(50) COLLATE utf8_croatian_ci DEFAULT NULL COMMENT '发布者',
  PRIMARY KEY (`article_id`),
  KEY `fk_user_id` (`user_id`),
  CONSTRAINT `fk_user_id` FOREIGN KEY (`user_id`) REFERENCES `users` (`user_id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_croatian_ci;

CREATE TABLE `users` (
  `user_id` varchar(50) COLLATE utf8_croatian_ci NOT NULL COMMENT '主键',
  `user_name` varchar(20) CHARACTER SET utf8 DEFAULT NULL COMMENT '登录名',
  `user_password` varchar(100) CHARACTER SET utf8 DEFAULT NULL COMMENT '密码',
  `user_nickName` varchar(50) CHARACTER SET utf8 DEFAULT NULL COMMENT '用户名',
  `user_email` varchar(50) CHARACTER SET utf8 DEFAULT NULL COMMENT '邮箱',
  PRIMARY KEY (`user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_croatian_ci;
```



#### 1.2. 数据库表模型

![image-20220415150916410](https://s2.loli.net/2022/04/15/JpxvOBUrlzqLHDd.png)

### 二.开发环境搭建

#### 2.1.基本搭建

##### 2.1.1. 创建一个为ssmblog的maven项目

##### 2.1.2. 导入jar支持和maven资源过滤

```xml
<dependencies>
   <!--Junit-->
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
   </dependency>
   <!--数据库驱动-->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.47</version>
   </dependency>
   <!-- 数据库连接池 -->
   <dependency>
       <groupId>com.mchange</groupId>
       <artifactId>c3p0</artifactId>
       <version>0.9.5.2</version>
   </dependency>

   <!--Servlet - JSP -->
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>servlet-api</artifactId>
       <version>2.5</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet.jsp</groupId>
       <artifactId>jsp-api</artifactId>
       <version>2.2</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>jstl</artifactId>
       <version>1.2</version>
   </dependency>

   <!--Mybatis-->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.2</version>
   </dependency>
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis-spring</artifactId>
       <version>2.0.2</version>
   </dependency>

   <!--Spring-->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>5.1.9.RELEASE</version>
   </dependency>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-jdbc</artifactId>
       <version>5.1.9.RELEASE</version>
   </dependency>
</dependencies>
```

```xml
<build>
  <resources>
    <resource>
      <directory>src/main/java</directory>
      <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
      </includes>
      <filtering>false</filtering>
    </resource>
    <resource>
      <directory>src/main/resources</directory>
      <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
      </includes>
      <filtering>false</filtering>
    </resource>
  </resources>
</build>
```

##### 2.1.3 创建mybatis-config.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
       PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>
```

##### 2.1.4. 创建applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

##### 2.1.5. 创建开发的目录

#### 2.2.Mybatis搭建

##### 2.2.1. 数据库配置文件database.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
jdbc.username=root
jdbc.password=123456
```

##### 2.2.2. IDEA关联数据库

##### 2.2.3. 编写Mybatis的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <typeAliases>
        <package name="com.blog.pojo"/>
    </typeAliases>

    <mappers>
        <package name="com.blog.dao"/>
    </mappers>
</configuration>
```

#### 2.3.Spring层

##### 2.3.1. 配置Spring整合MyBatis，这里我们使用c3p0连接池

##### 2.3.2. 编写Spring整合Mybatis的相关配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

   <!-- 配置整合mybatis -->
   <!-- 1.关联数据库文件 -->
   <context:property-placeholder location="classpath:database.properties"/>

   <!-- 2.数据库连接池 -->
   <!--数据库连接池
       dbcp 半自动化操作 不能自动连接
       c3p0 自动化操作（自动的加载配置文件 并且设置到对象里面）
   -->
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
       <!-- 配置连接池属性 -->
       <property name="driverClass" value="${jdbc.driver}"/>
       <property name="jdbcUrl" value="${jdbc.url}"/>
       <property name="user" value="${jdbc.username}"/>
       <property name="password" value="${jdbc.password}"/>

       <!-- c3p0连接池的私有属性 -->
       <property name="maxPoolSize" value="30"/>
       <property name="minPoolSize" value="10"/>
       <!-- 关闭连接后不自动commit -->
       <property name="autoCommitOnClose" value="false"/>
       <!-- 获取连接超时时间 -->
       <property name="checkoutTimeout" value="10000"/>
       <!-- 当获取连接失败重试次数 -->
       <property name="acquireRetryAttempts" value="2"/>
   </bean>

   <!-- 3.配置SqlSessionFactory对象 -->
   <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
       <!-- 注入数据库连接池 -->
       <property name="dataSource" ref="dataSource"/>
       <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
       <property name="configLocation" value="classpath:mybatis-config.xml"/>
   </bean>

   <!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中 -->
   <!--解释 ：https://www.cnblogs.com/jpfss/p/7799806.html-->
   <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
       <!-- 注入sqlSessionFactory -->
       <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
       <!-- 给出需要扫描Dao接口包 -->
       <property name="basePackage" value="com.kuang.dao"/>
   </bean>

</beans>
```

##### 2.3.3. spring整合service层

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd">

    <!--扫描service相关的包-->
    <context:component-scan base-package="com.blog.service"/>

    <!--接口实现类注入到IOC容器-->

    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据库连接池-->
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

#### 2.4. springMVC层

##### 2.4.1. 配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--DispatcherServlet-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <!--这里加载的是总的配置文件-->
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--encodingFilter-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```

##### 2.4.2. spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd
   http://www.springframework.org/schema/mvc
   https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--1.开启springmvc注解驱动-->
    <mvc:annotation-driven/>
    <!--2.静态资源默认servlet配置-->
    <mvc:default-servlet-handler/>

    <!--3.配置jsp 显示viewResolver-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--4.扫描web相关的bean-->
    <context:component-scan base-package="com.blog.controller"/>
</beans>
```

### 三.开发阶段

#### 3.1. 实体类编写

##### 3.1.1. user类

```java
package com.blog.pojo;

import com.blog.utils.IDUtils;
import com.blog.utils.MyBCrypt;
import org.springframework.stereotype.Component;

@Component("User")
public class User {
	private String userId;
	private String userName;
	private String userPassword;
	private String userNickName;
	private String userEmail;

	public User() {
	}

	public User(String userName, String userPassword,String userNickName,String userEmail){
		this.userId = IDUtils.getId();
		this.userName = userName;
		this.userPassword = MyBCrypt.coding(userPassword);
		this.userNickName=userNickName;
		this.userEmail=userEmail;
	}

	public User(String userId, String userName, String userPassword, String userNickName, String userEmail) {
		this.userId = userId;
		this.userName = userName;
		this.userPassword = userPassword;
		this.userNickName = userNickName;
		this.userEmail = userEmail;
	}

	public String getUserId() {
		return userId;
	}

	public void setUserId(String userId) {
		this.userId = userId;
	}

	public String getUserName() {
		return userName;
	}

	public void setUserName(String userName) {
		this.userName = userName;
	}

	public String getUserPassword() {
		return userPassword;
	}

	public void setUserPassword(String userPassword) {
		this.userPassword = userPassword;
	}

	public String getUserNickName() {
		return userNickName;
	}

	public void setUserNickName(String userNickName) {
		this.userNickName = userNickName;
	}

	public String getUserEmail() {
		return userEmail;
	}

	public void setUserEmail(String userEmail) {
		this.userEmail = userEmail;
	}

	@Override
	public String toString() {
		return "User{" +
				"userId='" + userId + '\'' +
				", userName='" + userName + '\'' +
				", userPassword='" + userPassword + '\'' +
				", userNickName='" + userNickName + '\'' +
				", userEmail='" + userEmail + '\'' +
				'}';
	}
}

```

##### 3.1.2. article类

```java
public class Article {
	private int articleId;
	private String articleTitle;
	private String articleContent;
	private Date articleData;
	private int userId;

	public Article() {
	}

	public int getArticleId() {
		return articleId;
	}

	public void setArticleId(int articleId) {
		this.articleId = articleId;
	}

	public String getArticleTitle() {
		return articleTitle;
	}

	public void setArticleTitle(String articleTitle) {
		this.articleTitle = articleTitle;
	}

	public String getArticleContent() {
		return articleContent;
	}

	public void setArticleContent(String articleContent) {
		this.articleContent = articleContent;
	}

	public Date getArticleData() {
		return articleData;
	}

	public void setArticleData(Date articleData) {
		this.articleData = articleData;
	}

	public int getUserId() {
		return userId;
	}

	public void setUserId(int userId) {
		this.userId = userId;
	}

	public Article(int articleId, String articleTitle, String articleContent, Date articleData, int userId) {
		this.articleId = articleId;
		this.articleTitle = articleTitle;
		this.articleContent = articleContent;
		this.articleData = articleData;
		this.userId = userId;
	}

	@Override
	public String toString() {
		return "article{" +
				"articleId=" + articleId +
				", articleTitle='" + articleTitle + '\'' +
				", articleContent='" + articleContent + '\'' +
				", articleData=" + articleData +
				", userId=" + userId +
				'}';
	}
}
```

#### 3.2. dao层编写

##### 3.2.1. 编写userMapper接口文件

```java
@Repository
public interface userMapper {
	//添加用户
	int addUser(User user);
	//修改用户
	int updateUser(User user);
	//根据id查询用户
	List<User> queryUserById(int id);
	//查询全部用户
	List<User> queryAllUser();
	//删除用户
	int deleteUser(int id);
}
```

##### 3.2.2. 编写articleMapper接口文件

##### 3.2.3. 在resources目录下创建对应接口包的目录并在其下面编写mapper.xml文件

* userMapper.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.blog.dao.userMapper">
    
        <insert id="addUser" parameterType="user">
            insert into ssmblog.users (user_id,user_name,user_password)
            values (#{userId},#{userName},#{userPassword});
        </insert>
    
        <select id="queryUserByName" resultType="com.blog.pojo.User">
            select *
            from ssmblog.users
            where user_name=#{userName};
        </select>
    
        <select id="queryAllUser" resultType="com.blog.pojo.User">
            select *
            from ssmblog.users;
        </select>
    
    </mapper>
    ```
    
    

