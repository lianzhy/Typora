

# 1、SpringCloud入门概述

## 1.1 SpringCloud是什么

springcloud官网： https://spring.io/projects/spring-cloud#learn



![image-20210201211026978](https://s2.loli.net/2022/05/24/YGHMTSODZeV6QNI.png)



![image-20210201211150517](https://s2.loli.net/2022/05/24/7xmqYMhjCp3L5Hf.png)

## 1.2 SpringBoot与SpringCloud的关系

![image-20210201211354609](https://s2.loli.net/2022/05/24/uXKMywA9IqRmrEF.png)

## 1.3 Dubbo 和 SpringCloud技术选型

### 1、分布式+服务治理Dubbo

目前成熟的互联网架构:应用服务化拆分＋消息中间件

![image-20210201212331080](https://s2.loli.net/2022/05/24/ZCYtxEmyUbfBPJO.png)





### 2、Dubbo 和 SpringCloud对比



可以看一下社区活跃度

https://github.com/dubbo

https://github.com/springcloud



![image-20210201212745280](https://s2.loli.net/2022/05/24/bSuKH8qrvs7aP53.png)



![image-20210201212830068](https://s2.loli.net/2022/05/24/ikyJ84BlKDRG3Wn.png)

![image-20210201213109728](https://s2.loli.net/2022/05/24/IyRXrzNb8GSPjhD.png)



## 1.4 SpringCloud能干什么

![image-20210201213607037](https://s2.loli.net/2022/05/24/DjUk4fR1unrASTJ.png)



## 1.5 SpringCloud在哪下

官网 ： https://spring.io/projects/spring-cloud/

![image-20210201213917897](https://s2.loli.net/2022/05/24/u1rAjf3HWYnskRg.png)



![image-20210201213933327](https://s2.loli.net/2022/05/24/Ru5zVUKIWiMLDHs.png)

![image-20210201213945729](https://s2.loli.net/2022/05/24/vUWcZ1FROraTYIl.png)



# 2、总体介绍

![image-20210201214803071](https://s2.loli.net/2022/05/24/XYQuC5DbHMf1ISA.png)

## 2.1SpringCloud版本选择



![image-20210201214850789](https://s2.loli.net/2022/05/24/2qw1ublPIMip5Qv.png)

![image-20210201215047918](https://s2.loli.net/2022/05/24/b9QjkFEl7t3zDZX.png)

![image-20210201214945469](https://s2.loli.net/2022/05/24/NP1OZJjwzBqcCmo.png)

# 3、创建父工程



直接创建一个名为SpringCloud的Maven空项目即可



**然后后面全部的项目都是父工程的一个子模块，并且都是maven的空项目**



建立一个数据库：db01

![image-20210202221055411](https://s2.loli.net/2022/05/24/VvsaUtZp2YGFeqR.png)

## pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.wu</groupId>
    <artifactId>SpringCloud</artifactId>
    <version>1.0-SNAPSHOT</version>
   
    <!--打包方式-->
    <packaging>pom</packaging>

    <!--定义版本号-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <lombok.version>1.18.16</lombok.version>
        <log4j.version>1.2.17</log4j.version>
        <logback-core.version>1.2.3</logback-core.version>
        <mysql-connector-java.version>8.0.21</mysql-connector-java.version>
        <druid.version>1.1.23</druid.version>
        <mybatis-spring-boot-starter.version>2.1.4</mybatis-spring-boot-starter.version>
        <spring-boot-dependencies.version>2.3.8.RELEASE</spring-boot-dependencies.version>
        <spring-cloud-dependencies.version>Hoxton.SR9</spring-cloud-dependencies.version>
    </properties>

	<!--这只是依赖管理，如果子项目需要用到依赖，只是省略了版本号而已，实际上还是要导依赖-->
    <dependencyManagement>
        <dependencies>
            <!--很重要的包：springcloud的依赖-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud-dependencies.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--springboot-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot-dependencies.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--数据库-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector-java.version}</version>
            </dependency>
            <!--数据源-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <!--mybatis的springboot启动器-->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis-spring-boot-starter.version}</version>
            </dependency>
            <!--日志测试-->
            <!--junit-->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <!--lombok-->
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
            </dependency>
            <!--log4j-->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-core</artifactId>
                <version>${logback-core.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <build>
        <!--打包插件-->
    </build>

</project>
```





# 4、pojo管理：springcloud-api

现在这个api拆分出来只管pojo，这个微服务就写完了

![image-20210201233314885](https://s2.loli.net/2022/05/24/ZsiBDugoycTvH7F.png)

### pom.xml

```xml
<!--当前的Module自己需要的依赖，如果父依赖中已经配置了，这里就不用写了-->
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
</dependencies>  
```

### Dept.java

```java
@Data
@NoArgsConstructor
@Accessors(chain = true)  //链式写法
//所有的实体类务必实现序列化,通讯需求
public class Dept implements Serializable {//Dept,实体类 orm 类表关系映射
    private static final long serialVersionUID = 708560364349809174L;
    private Long deptno; //主键
    private String dname;

    //看下这个数据存在哪个数据库的字段~ 微服务 ，一个服务对应一个数据库
    //同一个信息可能存在不同的数据库
    private String db_source;

    public Dept(String dname) {
        this.dname = dname;
    }

}
```

# 5、服务提供者：springcloud-provider-dept-8001

![image-20210201233640050](https://s2.loli.net/2022/05/24/WELFQthxyr1u7OM.png)





### pom.xml

```xml
<dependencies>
    <!--我们需要拿到实体类，所以要配置api module-->
    <dependency>
        <groupId>com.wu</groupId>
        <artifactId>springcloud-api</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <!--junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
    </dependency>
    <!--日志门面-->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-core</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <!--test-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-test</artifactId>
    </dependency>
    <!--web-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--jetty：尝试着用这个当应用服务器，与Tomcat没什么区别-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
    <!--热部署工具-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

### DeptMapper

```java
@Mapper
@Repository
public interface DeptMapper {
    //添加部门
    boolean addDept(Dept dept);

    //根据ID查询部门
    Dept queryById(@Param("deptno") long id);

    //查询全部部门
    List<Dept> queryall();
}
```

### DeptService

```java
public interface DeptService {
    boolean addDept(Dept dept);

    Dept queryById(long id);

    List<Dept> queryall();

}
```

### DeptServiceImpl

```java
@Service
public class DeptServiceImpl implements DeptService {

    @Autowired
    private DeptMapper deptMapper;

    @Override
    public boolean addDept(Dept dept) {
        return deptMapper.addDept(dept);
    }

    @Override
    public Dept queryById(long id) {
        return deptMapper.queryById(id);
    }

    @Override
    public List<Dept> queryall() {
        return deptMapper.queryall();
    }
}
```



### DeptMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.wu.springcloud.mapper.DeptMapper">
    <insert id="addDept" parameterType="Dept">
        insert into dept(dname,db_source)
        values (#{dname},DATABASE());
    </insert>

    <select id="queryById" resultType="Dept" parameterType="Long">
        select * from dept where deptno = #{deptno};
    </select>

    <select id="queryall" resultType="Dept">
        select * from dept;
    </select>
</mapper>
```



### mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- configuration核心配置文件 -->
<configuration>
    <settings>
        <!--开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
    </settings>
</configuration>
```



### application.yml

```yml
server:
  port: 8001

#mybatis配置
mybatis:
  type-aliases-package: com.wu.springcloud.pojo
  config-location: classpath:mybatis/mybatis-config.xml
  mapper-locations: classpath:mybatis/mapper/*.xml

#spring的配置
spring:
  application:
    name: springcloud-provider-dept  # 3个服务名称一致是前提
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  #数据源
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/db01?useUnicode=true&characterEncoding=UTF-8&useSSL=true&serverTimezone=UTC
    username: root
    password: Wl123456
```



### DeptController

```java
//提供Restfull服务!!
@RestController
public class DeptController {

    @Autowired
    private DeptServiceImpl deptService;

    @PostMapping("/dept/add")
    public boolean addDept(Dept dept) {
        return deptService.addDept(dept);
    }


    @GetMapping("/dept/get/{id}")
    public Dept getDept(@PathVariable("id") Long id) {
        Dept dept = deptService.queryById(id);
        if (dept == null) {
            throw new RuntimeException("Fail");
        }
        return dept;
    }

    @GetMapping("/dept/list")
    public List<Dept> queryAll() {
        return deptService.queryall();
    }

}
```



### 主启动类DeptProvider_8001

```java
//启动类
@SpringBootApplication
public class DeptProvider_8001 {
    public static void main(String[] args) {
        SpringApplication.run(DeptProvider_8001.class, args);
    }
}
```

最后启动项目访问Controller里面的接口测试即可，这个pojo类在别的项目里面，我们照样可以拿到，这就是微服务的简单拆分的一个小例子

# 6、服务消费者：springcloud-consumer-dept-80



![image-20210202201457445](https://s2.loli.net/2022/05/24/IUJTGrA4Z8F2hHu.png)



### pom.xml

```xml
<!--消费者只需要  实体类  +  Web 依赖即可-->
<dependencies>
    <dependency>
        <groupId>com.wu</groupId>
        <artifactId>springcloud-api</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--热部署工具-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

这里要用到  `RestTemplate` ,但是它的类中没有Bean，所以我们要把它注册到Bean中

### ConfigBean

```java
@Configuration
public class ConfigBean {  //@Configuration ... 相当于spring中的配置文件 applicationContext.xml文件
    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```



### DeptConsumerController

```java
@Controller
public class DeptConsumerController {

    //消费者 : 不因该有service层
    //RestTemplate  有很多方法给我们直接调用  !  它的类中没有Bean所以要我们自己把它注册到Bean中
    //(url, 实体：Map, Class<T> responseType)
    @Autowired
    private RestTemplate restTemplate;  //提供多种便捷访问远程http服务的方法，简单的restful服务模板

    private static final String REST_URL_PREFIX = "http://localhost:8001";

    @RequestMapping("/consumer/dept/get/{id}")
    @ResponseBody
    public Dept getDept(@PathVariable("id") Long id) {
        //service不在本项目中，所以要去远程项目获取
        //远程只能用  get 方式请求，那么这里也只能通过 get 方式获取
        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/get/" + id, Dept.class);
    }

    @RequestMapping("/consumer/dept/add")
    @ResponseBody
    public boolean add(Dept dept) {
        //远程只能用  post 方式请求，那么这里也只能通过 post 方式获取
        return restTemplate.postForObject(REST_URL_PREFIX + "/dept/add", dept, Boolean.class);
    }

    @RequestMapping("/consumer/dept/list")
    @ResponseBody
    public List<Dept> queryAll() {
        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/list", List.class);
    }

}
```

然后你会发现，原来远程的post请求直接在url是拒绝访问的，但是在这个里面可以访问，只是结果为null

### application.yml

```yml
server:
  port: 80
```





### 主启动类DeptConsumer_80

```java
@SpringBootApplication
public class DeptConsumer_80 {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer_80.class, args);
    }
}
```



最后启动服务提供者  springcloud-provider-dept-8001

然后启动服务消费者 springcloud-consumer-dept-80

通过服务消费者的url请求去获取服务提供者对应的请求，照样可以拿到



# 7、Eureka服务注册与发现

## 7.1、什么是Eureka

![image-20210202194106171](https://s2.loli.net/2022/05/24/rE9P47vsjdR52GJ.png)

![image-20210202194635513](\SpringCloud.assets\image-20210202194635513.png)





## 7.2、原理讲解

![image-20210202194819584](https://s2.loli.net/2022/05/24/Qa8s4kV7dDxWhIA.png)

![image-20210202194844907](https://s2.loli.net/2022/05/24/QWl2e6jIahd8LRv.png)

![image-20210202194857876](SpringCloud.assets\image-20210202194857876.png)



![image-20210202194917865](https://s2.loli.net/2022/05/24/8TxJuhrBtycPjHZ.png)

![image-20210202195102974](https://s2.loli.net/2022/05/24/YT9QMVBCJOldp67.png)

![image-20210202195011174](\SpringCloud.assets\image-20210202195011174.png)



![image-20210202195004133](https://s2.loli.net/2022/05/24/JdTZ6omr2e8NQbj.png)





## 7.3、springcloud-eureka-7001

### pom.xml

```xml
<!--导包~-->
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka-server</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <!--热部署工具-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```



### application.yml

```yml
server:
  port: 7001
  servlet:
    context-path: /eureka

#Eureka配置
eureka:
  instance:
    hostname: localhost # Eureka服务端实例的名字
  client:
    register-with-eureka: false  # 表示是否向Eureka注册中心注册自己
    fetch-registry: false #如果为false，则表示自己为注册中心
    service-url: # 监控页面地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```



### 主启动类：EurekaServer_7001

```java
@SpringBootApplication
@EnableEurekaServer //EnableEurekaServer表示服务端的启动类，可以接收别人注册进来
public class EurekaServer_7001 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigEurekaServer_7001.class, args);
    }
}
```



启动之后访问 http://localhost:7001/

![image-20210202201258376](https://s2.loli.net/2022/05/24/RbI6NDHJ5P1fT3U.png)



## Eureka的自我保护机制

![image-20210202204307917](https://s2.loli.net/2022/05/24/mQvUfCYSkVK8BEc.png)

![image-20210202204328410](https://s2.loli.net/2022/05/24/FVh7gBZlSXiY6mt.png)

## 7.4、8001服务注册与发现

springcloud-provider-dept-8001

首先肯定是要导入对应的依赖

### pom.xml

```xml
<!--Eureka-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
    <version>1.4.7.RELEASE</version>
</dependency>
```

然后在配置文件中添加对应的Eureka注册配置

### application.yml

```yml
#eureka 的配置，服务注册到哪里
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka/
```

最后在主启动类上添加注解

```java
@EnableEurekaClient  //在服务启动后自动注册到Eureka中
```

启动springcloud-config-eureka-7001，启动完毕后再启动下面的服务

启动springcloud-provider-dept-8001，等一会再次访问 http://localhost:7001/



![image-20210202210301741](https://s2.loli.net/2022/05/24/lkhUsD2ob69H8WO.png)



但是你点击服务状态信息会跳到一个页面

![image-20210202210434537](https://s2.loli.net/2022/05/24/jKmGCdo23pfQ4cq.png)

### actuator完善监控信息

所以这个时候我们应该是少了什么东西，然后我们继续在 8001 里面添加依赖

```xml
<!--actuator完善监控信息-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```



重启8001项目再次点击服务状态信息，跳到了一个页面，但是里面什么都没有，这个时候我们就要配置一些信息了，这个信息只是在团队开发的时候别人会通过这个信息来了解这个服务是谁写的

现在我们在8001项目的配置文件中添加一些配置

```yml
#eureka 的配置，服务注册到哪里
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka/
  instance:
    instance-id: springcloud-provider-dept8001  #修改Eureka上的默认的状态名字

#info配置（点击状态信息会返回的东西，可以百度）
info:
  app.name: wulei-springcloud    
  company.name: blog.wulei2921625957.com
```

然后你重启8001项目，再次点击项目状态信息会返回你在上面添加的信息



那如何通过代码来让别人发现自己呢？

### 服务发现

在8001项目的controller里面添加

```java
import org.springframework.cloud.client.discovery.DiscoveryClient;

//获取一些配置的信息，得到一些具体微服务
@Autowired
private DiscoveryClient client;

//注册进来的微服务~ ，获取一些信息
@GetMapping("/dept/discovery")
public Object discovery() {
    //获取微服务列表的清单
    List<String> services = client.getServices();
    System.out.println("discovery=>services:" + services);

    //得到一个具体的微服务信息,通过具体的微服务ID applicationName
    List<ServiceInstance> instances = client.getInstances("SPRINGCLOUD-PROVIDER-DEPT");
    for (ServiceInstance instance : instances) {
        System.out.println(
            instance.getHost() + "\t" +
            instance.getPort() + "\t" +
            instance.getUri() + "\t" +
            instance.getServiceId()
        );
    }
    return instances;
}
```

然后在8001项目主启动类上添加服务发现注解即可

这个注解我试了一下，不加也可以访问上面的接口返回信息

```java
@EnableDiscoveryClient //服务发现
```

重启8001项目并访问 http://localhost:8001/dept/discovery

![image-20210202211355622](https://s2.loli.net/2022/05/24/4LDbEQUcvIz8yoN.png)

# 8、Eureka集群的搭建 

![image-20210202212154668](https://s2.loli.net/2022/05/24/NTJ2CQuXrSjnEDe.png)





## 8.1、修改域名映射

为了体验集群搭载在不同的电脑上，我们进入`C:\Windows\System32\drivers\etc`里面修改hosts文件，在文件的末尾添加下面几行

```xml
127.0.0.1       eureka7001.com
127.0.0.1       eureka7002.com
127.0.0.1       eureka7003.com
```

## 8.2、修改7001配置文件

### application.yml

```yml
server:
  port: 7002
  servlet:
    context-path: /eureka

#Eureka配置
eureka:
  instance:
    #    hostname: localhost # Eureka服务端实例的名字
    hostname: eureka7001.com # Eureka服务端实例的名字
  client:
    register-with-eureka: false  # 表示是否向Eureka注册中心注册自己
    fetch-registry: false #如果为false，则表示自己为注册中心
    service-url: # 监控页面地址
      # 单机模式下配置自己一个就够了：defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      # 集群（关联）: 我们需要在7001里面去挂载7002和7003
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```





## 8.3 、springcloud-eureka-7002

创建Eureka注册中心7002项目（和7001一模一样）

### pom.xml

依赖和7001一样

### EurekaServer_7002

主启动类

```java
//启动之后访问 http://localhost:7002/
@SpringBootApplication
@EnableEurekaServer //EnableEurekaServer表示服务端的启动类，可以接收别人注册进来
public class EurekaServer_7002 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigEurekaServer_7002.class, args);
    }
}
```

### application.yml

```yml
server:
  port: 7003
  servlet:
    context-path: /eureka

#Eureka配置
eureka:
  instance:
    #    hostname: localhost # Eureka服务端实例的名字
    hostname: eureka7002.com # Eureka服务端实例的名字
  client:
    register-with-eureka: false  # 表示是否向Eureka注册中心注册自己
    fetch-registry: false #如果为false，则表示自己为注册中心
    service-url: # 监控页面地址
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7003.com:7003/eureka/
```





## 8.4 、springcloud-eureka-7003

创建Eureka注册中心7003项目（和7001一模一样）

### pom.xml

依赖和7001一样

### EurekaServer_7003

主启动类

```java
//启动之后访问 http://localhost:7003/
@SpringBootApplication
@EnableEurekaServer //EnableEurekaServer表示服务端的启动类，可以接收别人注册进来
public class EurekaServer_7003 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigEurekaServer_7003.class, args);
    }
}
```

### application.yml

```yml
server:
  port: 7003

#Eureka配置
eureka:
  instance:
    #    hostname: localhost # Eureka服务端实例的名字
    hostname: eureka7003.com # Eureka服务端实例的名字
  client:
    register-with-eureka: false  # 表示是否向Eureka注册中心注册自己
    fetch-registry: false #如果为false，则表示自己为注册中心
    service-url: # 监控页面地址
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
```



然后启动7001、7002、7003项目

然后访问：http://localhost:7001/  、http://localhost:7002/  、http://localhost:7003/ 



![image-20210202214950627](https://s2.loli.net/2022/05/24/dqR3n7rAp2YFXwV.png)



然后你可以停掉一个集群，然后又启动停掉的那个集群，发现它会自动注册上去



## 8.5、8001项目注册多个注册中心

要把8001项目注册到多个注册中心上去，其实很简单，只需要改动配置文件即可

### application.yml（8001）

```yml
# ...

#eureka 的配置，服务注册到哪里
eureka:
  client:
    service-url:
      # defaultZone: http://localhost:7001/eureka/
      # 然而现在服务发布要发布到3个注册中心上面去
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
# ...
```

然后启动8001项目，刷新http://localhost:7001/  、http://localhost:7002/  、http://localhost:7003/ 即可发现

![image-20210202215512999](https://s2.loli.net/2022/05/24/YfqSBZ6xjyGpa3U.png)





# 9、CAP原则及对比Zookeeper

![image-20210202215831461](https://s2.loli.net/2022/05/24/8tD6Oi4ACkspbQd.png)

![image-20210202215941127](https://s2.loli.net/2022/05/24/RG96qWc12JEKs8S.png)



## 作为服务注册中心，Eureka比Zookeeper好在那里？



![image-20210202220131502](https://s2.loli.net/2022/05/24/UwKzd8pyMDt7LTI.png)

![image-20210202220226349](https://s2.loli.net/2022/05/24/kRdiO2BxWzIsC4G.png)

![image-20210202220451883](\SpringCloud.assets\image-20210202220451883.png)

# 10、Ribbon负载均衡

## ribbon是什么？

![image-20210202221241417](https://s2.loli.net/2022/05/24/iASGEDHmrCVJfTh.png)

<img src="https://s2.loli.net/2022/05/24/mI4YNlkDRxsoMnT.png" alt="image-20210202221729868" style="zoom:50%;" />



## ribbon能干什么？

![image-20210202222340405](https://s2.loli.net/2022/05/24/1HoLQvxFGa8RTXt.png)

## 10.1、springcloud-consumer-dept-80使用Ribbon

首先80项目要添加两个依赖

### pom.xml

```xml
<!--Ribbon-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-ribbon</artifactId>
    <version>1.4.7.RELEASE</version>
</dependency>
<!--导入Ribbon的同时要导入erueka，因为它要发现服务从那里来-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
    <version>1.4.7.RELEASE</version>
</dependency>
```

由于我们消费者客户端是利用RestTemplate来进行服务的读取，所以我们让RestTemplate实现负载均衡，只需要加一个注解即可`@LoadBalanced`

### ConfigBean

```java
@Bean
@LoadBalanced  //Ribbon,只要加了这个注解，这个RestTemplate就变成了负载均衡
public RestTemplate getRestTemplate() {
    return new RestTemplate();
}
```

由于我们导入了Eureka，所以我们要配置Eureka

### application.yml

```yml
server:
  port: 80

#Eureka配置
eureka:
  client:
    register-with-eureka: false #不向eureka中注册自己
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/

```



### DeptConsumer_80

```java
//Ribbon和 Eureka整合以后，客户端可以直接调用，不用关心IP地址和端口号
@SpringBootApplication
//@EnableEurekaClient  //在服务启动后自动注册到Eureka中
public class DeptConsumer_80 {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer_80.class, args);
    }
}
```

最后还有一个问题，就是我们的RestTemplate实现了负载均衡，那么怎么体现它呢？我们现在就只是在它身上加了一个注解，那肯定是不行的，我们还要改变RestTemplate的请求路径，让其自动选择，而不是写死

### DeptConsumerController

```java
//private static final String REST_URL_PREFIX = "http://localhost:8001";
//用Ribbon做负载均衡的时候不应该写它，不应该写死,地址应该是一个变量,通过服务名来访问
private static final String REST_URL_PREFIX = "http://SPRINGCLOUD-PROVIDER-DEPT";
```

最后启动7001、7002、7003项目后再启动8001项目，等8001项目注册到它们3个中后启动80项目

然后访问 http://localhost/consumer/dept/list  可以看到正常返回结果，当然了，在这里也看不出负载均衡，所以下面会配置多个服务提供者和多个数据库，来测试负载均衡的效果。



## 10.2、使用Ribbon实现负载均衡

![image-20210203102021127](https://s2.loli.net/2022/05/24/7tNUkqr5Pjw3Cag.png)



### 创建另外两个数据库：db02、db03

![image-20210203102251057](https://s2.loli.net/2022/05/24/6xNZfzSA1ampBi4.png)



<img src="https://s2.loli.net/2022/05/24/vx9Az4eO1cI3sYK.png" alt="image-20210203102338828" style="zoom:67%;" /><img src="https://s2.loli.net/2022/05/24/DIEh1NZF4JWr7yS.png" alt="image-20210203102346731" style="zoom:67%;" />



### 创建另外两个服务提供者：8002、8003

直接新建两个子model，然后把8001的所有文件全部拷贝（提供相同的服务），一摸一样的，然后更改一下配置文件即可

- pom.xml依赖

- application.yml的端口号，对应的数据库，还有instance-id，例如：instance-id: springcloud-provider-dept8002

- 注意：下面的这个服务ID不要改

- ```yml
  spring:
    application:
      name: springcloud-provider-dept  # 3个服务名称一致是前提
  ```

  

现在的项目预览

![image-20210203105058120](https://s2.loli.net/2022/05/24/2xOgcXoPFahsUSl.png)



然后，启动

- springcloud-config-eureka-7001
- springcloud-config-eureka-7002
- springcloud-config-eureka-7003
- springcloud-provider-dept-8001
- springcloud-provider-dept-8002
- springcloud-provider-dept-8003
- springcloud-consumer-dept-80

![image-20210203120141132](https://s2.loli.net/2022/05/24/lXtEgi3fDPwSyd7.png)



然后访问http://localhost/consumer/dept/list ，多访问几次，查询的数据没变，但是数据库变了

![image-20210203120244511](https://s2.loli.net/2022/05/24/xePonw6LFZhORyE.png)



![image-20210203120251365](https://s2.loli.net/2022/05/24/tOLZ9JeQCW4rHzv.png)



![image-20210203120306639](https://s2.loli.net/2022/05/24/pULNKqICmvrDAzt.png)



你会发现是轮询，就是每个服务轮流来，这也Ribbon的默认算法

### Ribbon自定义均衡算法

里面有个接口非常重要：IRule，基本上全部的均衡算法都实现了这个接口

![image-20210203133008139](https://s2.loli.net/2022/05/24/rMLQpEU1ylKtwuk.png)

里面有这么多均衡算法，因为默认是轮询算法，也就是RoundRobinRule，那我们要怎么使用别的算法呢？

只需要在**80项目**的config类里面注册Bean即可

```java
//IRule
//RoundRobinRule:轮询
//RandomRule：随机
//AvailabilityFilteringRule：会先过滤掉跳闸、访问故障的服务~，对剩下的进行轮询
//RetryRule：会先按照轮询获取服务，如果服务获取失败，则会在指定的时间内进行重试
@Bean
public IRule myRule() {
    return new RandomRule(); //默认为轮询，现在我们使用随机的
}
```

然后启动80项目，访问http://localhost/consumer/dept/list，多访问几次，发现每次出现的数据库都没规律可循

我们要学会自定义负载均衡算法，为了体现我们使用了自定义的负载均衡算法，我们建包不建在主启动类的同级目录（官方建议）

![image-20210203133505705](https://s2.loli.net/2022/05/24/3ZsOlU1YPibzCoN.png)

把刚刚写在ConfigBean里面的Bean注释掉，我们来模仿它的算法写一个自己的算法

#### WuRandomRule

```java
public class WuRandomRule extends AbstractLoadBalancerRule {

    //每个机器，访问5次，换下一个服务（总共3个）
    //total = 0 默认=0，如果=5，我们指向下一个服务结点
    //index = 0 默认=0，如果total=5，那么index+1，

    private int total = 0; //被调用的次数
    private int currentIndex = 0; //当前是谁在提供服务

    public Server choose(ILoadBalancer lb, Object key) {
        if (lb == null) {
            return null;
        }
        Server server = null;

        while (server == null) {
            if (Thread.interrupted()) {
                return null;
            }
            List<Server> upList = lb.getReachableServers(); //获得还活着的服务
            List<Server> allList = lb.getAllServers();  //获得全部的服务

            int serverCount = allList.size();
            if (serverCount == 0) {
                return null;
            }

            //=============================================================

            if (total < 5) {
                total++;
            } else {
                total = 0;
                currentIndex++;
                if (currentIndex >= serverCount) {
                    currentIndex = 0;
                }
            }
            server = upList.get(currentIndex);
            //=============================================================
            if (server == null) {
                Thread.yield();
                continue;
            }

            if (server.isAlive()) {
                return (server);
            }
            server = null;
            Thread.yield();
        }

        return server;

    }

    protected int chooseRandomInt(int serverCount) {
        return ThreadLocalRandom.current().nextInt(serverCount);
    }

    @Override
    public Server choose(Object key) {
        return choose(getLoadBalancer(), key);
    }

    @Override
    public void initWithNiwsConfig(IClientConfig clientConfig) {

    }
}
```

#### WuRule

```java
@Configuration
public class WuRule {
    
    @Bean
    public IRule myRule() {
        return new WuRandomRule(); //默认为轮询，现在试使用自己自定义的
    }
}
```

最后还要在主启动类添加扫描注解，在微服务启动的时候就能去加载我们自定义的Ribbon类

#### DeptConsumer_80

```java
//Ribbon和 Eureka整合以后，客户端可以直接调用，不用关心IP地址和端口号
@SpringBootApplication
@EnableEurekaClient  //在服务启动后自动注册到Eureka中
//在微服务启动的时候就能去加载我们自定义的Ribbon类
@RibbonClient(name = "SPRINGCLOUD-PROVIDER-DEPT", configuration = WuRule.class)
public class DeptConsumer_80 {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer_80.class, args);
    }
}
```

然后重启80项目，访问http://localhost/consumer/dept/list，多访问几次，可以发现访问的服务每5次切换一下





# 11、Feign负载均衡

## 11.1、简介

![image-20210203144856775](\SpringCloud.assets\image-20210203144856775.png)

## 11.2、Feign能干什么？

![image-20210203145419179](https://s2.loli.net/2022/05/24/H3XbVW1Zhrl2nsP.png)

## 11.3、Feign集成了Ribbon

![image-20210203145526394](https://s2.loli.net/2022/05/24/xXo4mQbDPUSeO1H.png)

## 11.4 springcloud-consumer-dept-feign

![image-20210203163502138](https://s2.loli.net/2022/05/24/BNkJbC4HD7x9AM8.png)



创建一个springcloud-consumer-dept-feign空maven的空项目，这也是一个消费者，端口也是80，只是这个消费者使用Feign实现的负载均衡

![image-20210203161515728](https://s2.loli.net/2022/05/24/a9J6nQ2ONqTBoGA.png)



### pom.xml

```xml
<dependencies>
    <!--feign-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-feign</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <!--Ribbon-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-ribbon</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <!--导入Ribbon的同时要导入erueka，因为它要发现服务从那里来-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>com.wu</groupId>
        <artifactId>springcloud-api</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--热部署工具-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```





### application.yml

和springcloud-consumer-dept-80项目的一摸一样



### 修改springcloud-api

- 添加依赖

- ```xml
  <!--feign-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-feign</artifactId>
      <version>1.4.7.RELEASE</version>
  </dependency>
  ```

- 从前面了解到，Feign是通过注解接口，而Ribbon是通过微服务名字，那么下面给springcloud-api添加一个service包

![image-20210203162236532](https://s2.loli.net/2022/05/24/DBzSVGUra29OPxe.png)



- 并写上几个注解

- ```java
  @Component
  //Feign客户端的服务名
  @FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT")
  public interface DeptClientService {
  	
      @GetMapping("/dept/get/{id}")
      Dept queryById(@PathVariable("id") Long id);
  
      @PostMapping("/dept/add")
      boolean addDept(Dept dept);
  
      @RequestMapping("/dept/list")
      List<Dept> queryAll();
  
  }
  ```



然后在springcloud-consumer-dept-feign项目的controller也要做相应的修改

### DeptConsumerController

```java
@Controller
public class DeptConsumerController {
	
    //这里直接把springcloud-api的DeptClientService接口注入进来
    @Autowired
    private DeptClientService deptClientService = null;
	//然后通过接口来调用方法，调用方法后，它会调用上面类对应的方法，最后调用其服务名字的接口
    @RequestMapping("/consumer/dept/get/{id}")
    @ResponseBody
    public Dept queryById(@PathVariable("id") Long id) {
        return this.deptClientService.queryById(id);
    }

    @RequestMapping("/consumer/dept/add")
    @ResponseBody
    public boolean add(Dept dept) {
        return this.deptClientService.addDept(dept);
    }

    @RequestMapping("/consumer/dept/list")
    @ResponseBody
    public List<Dept> queryAll() {
        return this.deptClientService.queryAll();
    }

}
```

最后还要在启动类上添加FeignClient注解

### FeignDeptConsumer_80

```java
@SpringBootApplication
@EnableFeignClients(basePackages = {"com.wu.springcloud"})
public class FeignDeptConsumer_80 {
    public static void main(String[] args) {
        SpringApplication.run(FeignDeptConsumer_80.class, args);
    }
}
```

最后启动7001、...  、8001、...  、feign的80项目，测试





# 12、Hystrix服务熔断



## 分布式系统面临的问题



复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免的失败!



## 服务雪崩

![image-20210203163720422](https://s2.loli.net/2022/05/24/p9Bmo3KnsPSMJgL.png)







## 什么是Hystrix

![image-20210203163739837](https://s2.loli.net/2022/05/24/dNjgS1EOVBJwK7W.png)



![image-20210203163823198](https://s2.loli.net/2022/05/24/Q6uJy8Wo7MHImkG.png)



**官网资料**

https://github.com/Netflix/Hystrix/wiki



## 服务熔断

![image-20210203165129902](https://s2.loli.net/2022/05/24/iVKSM3d72r5mkGh.png)

### springcloud-provider-dept-hystrix-8001

创建一个带有服务熔断的服务提供者8001，把之前的8001项目原封不动的拷贝就行，改以下几个位置

#### pom.xml

添加依赖

```xml
<!--hystrix-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
    <version>1.4.7.RELEASE</version>
</dependency>
```



#### application.yml

```yml
  instance:
    instance-id: springcloud-provider-dept-hystrix-8001  #修改Eureka上的默认描述信息
    prefer-ip-address: true  # 为true可以显示服务的ip地址
```



#### DeptController

在方法上加入@HystrixCommand即可，这里做一个演示，就写一个方法做测试

```java
//提供Restfull服务!!
@RestController
public class DeptController {

    @Autowired
    private DeptServiceImpl deptService;


    @GetMapping("/dept/get/{id}")
    @HystrixCommand(fallbackMethod = "hystrixGetDept") //失败了就会调用下面的这个备选方案
    public Dept getDept(@PathVariable("id") Long id) {
        Dept dept = deptService.queryById(id);
        if (dept == null) {
            throw new RuntimeException("id-->" + id + "不存在该用户，或者信息无法找到");
        }
        return dept;
    }

    //备选方案
    public Dept hystrixGetDept(@PathVariable("id") Long id) {
        return new Dept()
                .setDeptno(id)
                .setDname("id=>" + id + "没有对应的信息，null---@hystrix")
                .setDb_source("not this database in MySQL");
    }

}
```

然后在主启动类上添加对熔断的支持  启用断路器



#### DeptProviderHystrix_8001

```java
//启动类
@SpringBootApplication
@EnableEurekaClient  //在服务启动后自动注册到Eureka中
@EnableCircuitBreaker //添加对熔断的支持  启用断路器
public class DeptProviderHystrix_8001 {
    public static void main(String[] args) {
        SpringApplication.run(DeptProviderHystrix_8001.class, args);
    }
}
```

启动集群，启动springcloud-provider-dept-hystrix-8001，启动80项目（普通的，不是feign的80项目）

- 访问正常请求http://localhost/consumer/dept/get/1
- ![image-20210203192738511](https://s2.loli.net/2022/05/24/hN2O9bWftXPBjru.png)

- 访问不正常请求，http://localhost/consumer/dept/get/10不会显示500错误，而是显示我们备选方案的返回结果
- ![image-20210203192838069](https://s2.loli.net/2022/05/24/gD1I8vO2rdphaTb.png)

## 服务降级



服务降级，当服务器压力剧增的情况下，根据当前业务情况及流量对一些服务和页面有策略的降级，以此释放服务器资源以保证核心任务的正常运行。比如电商平台，在针对618、双11等高峰情形下采用部分服务不出现或者延时出现的情形。

```xml
服务熔断：针对服务器的，某个服务连接超时或者异常的时候，引起熔断
服务降级：针对客户端，从整体网站请求负载考虑，当某个服务熔断或则关闭时，服务不在被调用，此时在客户端，我们可以准备一个FallbackFactory，返回一个默认的值，整体的服务水平下降了，好歹能用，比直接挂掉强
```

在springcloud-api项目的service包下建立DeptClientServiceFallbackFactory

### DeptClientServiceFallbackFactory

```java
//服务降级
@Component
//失败回调
public class DeptClientServiceFallbackFactory implements FallbackFactory {

    @Override
    public DeptClientService create(Throwable throwable) {
        return new DeptClientService() {
            @Override
            public Dept queryById(Long id) {
                return new Dept()
                        .setDeptno(id)
                        .setDname("id=>" + id + "，没有对应的信息，客户端提供了降级的信息，这个服务现在已经被关闭了")
                        .setDb_source("没有数据");
            }

            @Override
            public boolean addDept(Dept dept) {
                //...
                return false;
            }

            @Override
            public List<Dept> queryAll() {
                //...
                return null;
            }

        };
    }
}
```

然后怎么去体现它呢？还是一样的，在  springcloud-api 项目的DeptClientService接口上添加即可

### DeptClientService

失败回调工厂，参数就是上面配置的工厂类，只针对客户端

```java
@FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT", fallbackFactory = DeptClientServiceFallbackFactory.class)
```

启动7001项目、启动8001项目（正常的，不是Hystrix的那个）、然后再启动feign的80项目

- 正常访问 http://localhost/consumer/dept/get/1
- ![image-20210203201206830](https://s2.loli.net/2022/05/24/oulwMp5SXdb7vng.png)
- 访问一个不存在的数据
- ![image-20210203201222674](https://s2.loli.net/2022/05/24/tychlXWD6YbgTBw.png)

- 把8001服务提供者关闭，再次访问http://localhost/consumer/dept/get/1
- ![image-20210203201329756](https://s2.loli.net/2022/05/24/Ekqdh94eUOiS8GQ.png)

说明了什么？说明了服务熔断是被动的，服务降级是手动的，但是开启服务降级后，没有关闭服务，访问一个不存在的数据，也会返回一个客户端自定义的返回结果，当把服务关闭后，访问任何请求都是有客户端自定义的结果。





## Dashboard流监控



还是老规矩，新建一个项目

### springcloud-consumer-hystrix-dashborad	

![image-20210203212212785](https://s2.loli.net/2022/05/24/deOV6HExWmULruK.png)

#### pom.xml

```xml
<dependencies>
    <!--dashboard监控页面-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

#### application.yml

```yml
server:
  port: 9001
hystrix:
  dashboard:
    proxy-stream-allow-list: "*"
```



#### DeptConsumerDashboard_9001

```java
@SpringBootApplication
//服务端必须都要有监控依赖actuator
@EnableHystrixDashboard  //开启监控页面  http://localhost:9001/hystrix
public class DeptConsumerDashboard_9001 {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumerDashboard_9001.class, args);
    }
}
```

然后启动9001项目，直接访问http://localhost:9001/hystrix

![image-20210203212343244](https://s2.loli.net/2022/05/24/lkoL3EHrIOKFBJz.png)



现在监控页面搭载完成

我们要如何监控呢？监控什么呢？

我们监控的是实现了熔断支持的类（主启动类上加了@EnableCircuitBreaker注解），这里我们刚好有一个项目springcloud-provider-dept-hystrix-8001，还有一个前提，服务类必须添加actuator依赖

然后我们修改springcloud-provider-dept-hystrix-8001项目的主启动类，添加一个Servlet，为了配合监控使用



#### DeptProviderHystrix_8001

```java
//启动类
@SpringBootApplication
@EnableEurekaClient  //在服务启动后自动注册到Eureka中
@EnableCircuitBreaker //添加对熔断的支持  启用断路器
public class DeptProviderHystrix_8001 {
    public static void main(String[] args) {
        SpringApplication.run(DeptProviderHystrix_8001.class, args);
    }

    //为了配合监控使用
    //增加一个Servlet
    @Bean
    public ServletRegistrationBean hystrixMetricsStreamServlet() {
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(new HystrixMetricsStreamServlet());
        registrationBean.addUrlMappings("/actuator/hystrix.stream");
        return registrationBean;
    }
}
```



启动7001项目，启动9001项目，启动hystrix的8001项目，然后访问http://localhost:8001/dept/get/1，有返回数据即可

然后访问http://localhost:8001/actuator/hystrix.stream，会得到一些数据流

![image-20210203212928778](https://s2.loli.net/2022/05/24/n1Ne9yBFp5tkPhM.png)



然后http://localhost:9001/hystrix，填入以下信息即可

![image-20210203213133217](https://s2.loli.net/2022/05/24/C43Q1ipVNSwjl2G.png)



![image-20210203213146971](https://s2.loli.net/2022/05/24/54VbGo3DNnLlqZr.png)

![image-20210203213353062](https://s2.loli.net/2022/05/24/4AcMpDJoyO3ERkW.png)



![image-20210203211148860](https://s2.loli.net/2022/05/24/rXQiGmSEORZHBL2.png)



![image-20210203211216136](https://s2.loli.net/2022/05/24/iWX8cOMQYNIKTxe.png)

![image-20210203211249535](https://s2.loli.net/2022/05/24/Eztjx6QlsnuJb5d.png)







![image-20210203211325136](https://s2.loli.net/2022/05/24/3pC8Y1rgJIMcTea.png)



![image-20210203211459377](https://s2.loli.net/2022/05/24/wFLijP5uR7pY9d4.png)



# 13、Zuul 路由网关

## 概述

### 什么是Zuul

![image-20210203213518093](https://s2.loli.net/2022/05/24/3GJY6kewiRUo8r2.png)



### Zuul能干吗

- 路由
- 过滤



**官网** ： https://github.com/netflix/zuul



直接搭建项目：springcloud-zuul-9527

## springcloud-zuul-9527

先在自己的hosts文件添加域名映射来模仿网站

C:\Windows\System32\drivers\etc\hosts

![image-20210203221937360](https://s2.loli.net/2022/05/24/SGNE7KQ3AYDm5cf.png)

![image-20210203221949006](https://s2.loli.net/2022/05/24/C4B7ZtfsKIeSgPi.png)



### pom.xml

```xml
<dependencies>
    <!--zuul-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zuul</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <!--erueka-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <!--actuator完善监控信息-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```



然后第二步肯定是配置文件

### application.yml

```yml
server:
  port: 9527
spring:
  application:
    name: springcloud-zuul
# eureka 配置
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
  instance:
    instance-id: zuul9527.com  #修改Eureka上的默认描述信息
    prefer-ip-address: true  # 为true可以显示服务的ip地址
info:
  app.name: wu-springcloud
  company.name: blog.wulei2921625957.com

#zuul配置
zuul:
  routes:
    mydept.serviceId: springcloud-provider-dept  # 原来的id 
    mydept.path: /mydept/**  # serviceId 和 path 是配套使用的，前面的mydept可以随便
  ignored-services: 
    - springcloud-provider-dept  #不能再使用这个路径访问了  这是yml的数组表示方式
    # 没有加上面的忽略配置可以直接通过http://www.wu.com:9527/springcloud-provider-dept/dept/get/1访问
  prefix: /wu     # 这个是前缀  比如： http://www.wu.com:9527/wu/mydept/dept/get/1
```



最后是主启动类

### ZuulApplication_9527

```java
@SpringBootApplication
@EnableZuulProxy  //加上zuul代理注解即可
public class ZuulApplication_9527 {
    public static void main(String[] args) {
        SpringApplication.run(ZuulApplication_9527.class, args);
    }
}
```

总共就是这3步

然后启动项目 7001、8001、9527

访问http://www.wu.com:9527/wu/mydept/dept/get/1 即可得到结果



# 14、SpringCloud config 分布式配置



## 14.1、概述

### 分布式系统面临的-配置文件的问题

![image-20210203223410133](\SpringCloud.assets\image-20210203223410133.png)

### 什么是SpringCloud config分布式配置中心

![image-20210203223457210](https://s2.loli.net/2022/05/24/xfcPerpBlhgI381.png)

![image-20210203225513223](https://s2.loli.net/2022/05/24/s8Bc1XWb92RayDk.png)



## 14.2、环境搭建

我这里使用的码云：https://gitee.com/，在国内访问速度快一点，Githup有点慢就没用它



然后还是为了方便理解，重新建立项目



先建立连接码云长仓库的server端

### springcloud-config-server-3344

![image-20210204134502863](https://s2.loli.net/2022/05/24/dEwfDTmgerNIMZa.png)

#### pom.xml

```xml
<!--config-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
    <version>2.2.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!--actuator完善监控信息-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

#### application.yml

```yml
server:
  port: 3344


spring:
  application:
    name: springcloud-config-server
  # 连接远程仓库，先把连接远程配置写在这里
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/wuleiaizb/springcloud-config.git  #https的
```



#### Config_Server_3344

```java
@SpringBootApplication
@EnableConfigServer  //开启注解
public class Config_Server_3344 {
    public static void main(String[] args) {
        SpringApplication.run(Config_Server_3344.class, args);
    }
}
```

然后你码云或者Githup里面要有一个 **application.yml** 文件

```yml
# 这个3344项目只是为了读取配置，不干别的事，我这里配了 2 套环境为了测试，

spring:
  profiles: dev
  application:
    name: springcloud-config-dev
    
---
spring:
  profiles: test
  application:
    name: springcloud-config-test
```

然后

![image-20210204135650578](https://s2.loli.net/2022/05/24/tTNkeWdP18ErowZ.png)

所以上面项目里面的配置文件的uri就是这个仓库的https链接

启动3344项目，访问

- http://localhost:3344/application-dev.yml
- http://localhost:3344/application-test.yml
- http://localhost:3344/application/dev/master
- http://localhost:3344/master/application-dev.yml
- ![image-20210204135951533](https://s2.loli.net/2022/05/24/JsM26TmdhREOz5i.png)

都可以获取到，说明配置成功，那么可以进入下一步

### springcloud-config-eureka-7001

先在码云或者Githup上新建一个文件**config-eureka.yml**

```yml
spring:
  profiles:
    active: dev


---

server:
  port: 7001
spring:
  profiles: dev
  application:
    name: springcloud-config-eureka-dev

# C:\Windows\System32\drivers\etc\hosts  更改

#Eureka配置
eureka:
  instance:
    hostname: eureka7001.com #eureska服务端的实例名称    ，如果这个名字一样，他会当作是同一个集群，导致集群失败，所以我还是改回来了，知道即可
  client:
    register-with-eureka: false # 表示是否向eureka注册中心注册自己
    fetch-registry: false #表示如果为false则表示自己为注册中心 "defaultZone", "http://localhost:8761/eureka/"
    service-url:  # 监控页面
      #单机：
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      #集群（关联）
      #defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
      
 
---
server:
  port: 7001

spring:
  profiles: test
  application:
    name: springcloud-config-eureka-test

# C:\Windows\System32\drivers\etc\host  更改

#Eureka配置
eureka:
  instance:
    hostname: eureka7001.com #eureka服务端的实例名称    ，如果这个名字一样，他会当作是同一个集群，导致集群失败，所以我还是改回来了，知道即可
  client:
    register-with-eureka: false # 表示是否向eureka注册中心注册自己
    fetch-registry: false #表示如果为false则表示自己为注册中心 "defaultZone", "http://localhost:8761/eureka/"
    service-url:  # 监控页面
      #单机： 
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      #集群（关联）
      #defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```

然后创建项目 springcloud-config-eureka-7001

![image-20210204140713943](https://s2.loli.net/2022/05/24/7f8Bs5wqzPjpL6M.png)



#### pom.xml

```xml
<dependencies>
    <!--config-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
        <version>2.2.6.RELEASE</version>
    </dependency>
    <!--erueka  server-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka-server</artifactId>
        <version>1.4.7.RELEASE</version>
    </dependency>
    <!--热部署工具-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```



#### ConfigEurekaServer_7001

```java
//启动之后访问 http://localhost:7001/
@SpringBootApplication
@EnableEurekaServer //EnableEurekaServer表示服务端的启动类，可以接收别人注册进来
public class ConfigEurekaServer_7001 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigEurekaServer_7001.class, args);
    }
}
```



接下来要接触一个新的配置文件 **bootstrap.yml** ，这个是系统级别的，它的配置可以覆盖 **application.yml**

#### bootstrap.yml

```yml
# 系统级别
spring:
  cloud:
    config:
      uri: http://localhost:3344  #直接使用上一个项目来获取配置文件即可
      name: config-eureka # 需要从git上读取的资源名称，不要要后缀
      profile: dev  # 使用的开发环境
      label: master  # 使用分支，默认为主分支
```

#### application.yml

```yml
#用户级别
spring:
  application:
    name: springcloud-config-eureka-7001
```

然后在3344项目启动的情况下启动这个项目

访问 http://localhost:7001/ 显示正常页面即可

然后我们看它读取的配置 http://localhost:3344/master/config-eureka-dev.yml

![image-20210204141719967](https://s2.loli.net/2022/05/24/W9yDakjJGd3zSeZ.png)

最后我们来个服务提供者，也是新建一个项目

### springcloud-config-dept-8001

第一步也是先在码云或者Githup上新建文件**config-dept.yml**

```yml
# 两套环境的数据库不一样
spting:
  profiles:
    active: dev
    
---

server:
  port: 8001

#mybatis配置
mybatis:
  type-aliases-package: com.wu.springcloud.pojo
  config-location: classpath:mybatis/mybatis-config.xml
  mapper-locations: classpath:mybatis/mapper/*.xml

#spring的配置
spring:
  profiles: dev
  application:
    name: springcloud-provider-config-dept  # 3个服务名称一致是前提
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  #数据源
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/db01?useUnicode=true&characterEncoding=UTF-8&useSSL=true&serverTimezone=UTC
    username: root
    password: Wl123456

#eureka 的配置，服务注册到哪里
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka/
  instance:
    instance-id: springcloud-provider-dept8001  #修改Eureka上的默认描述信息
    prefer-ip-address: true  # 为true可以显示服务的ip地址

#info配置
info:
  app.name: wulei-springcloud
  company.name: blog.wulei2921625957.com
  
  
---

server:
  port: 8001

#mybatis配置
mybatis:
  type-aliases-package: com.wu.springcloud.pojo
  config-location: classpath:mybatis/mybatis-config.xml
  mapper-locations: classpath:mybatis/mapper/*.xml

#spring的配置
spring:
  profiles: test
  application:
    name: springcloud-provider-config-dept  # 3个服务名称一致是前提
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  #数据源
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/db02?useUnicode=true&characterEncoding=UTF-8&useSSL=true&serverTimezone=UTC
    username: root
    password: Wl123456

#eureka 的配置，服务注册到哪里
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka/
  instance:
    instance-id: springcloud-provider-dept8001  #修改Eureka上的默认描述信息
    prefer-ip-address: true  # 为true可以显示服务的ip地址

#info配置
info:
  app.name: wulei-springcloud
  company.name: blog.wulei2921625957.com
```

然后把原来普通的8001项目的文件原封不动的拷贝到这个项目，改以下几个位置即可

#### pom.xml

```xml
<!--添加依赖-->
<!--config-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
    <version>2.2.6.RELEASE</version>
</dependency>
```

#### bootstrap.yml

```yml
spring:
  cloud:
    config:
      uri: http://localhost:3344
      name: config-dept # 需要从git上读取的资源名称，不要要后缀
      label: master
      profile: dev
```

#### application.yml

```yml
spring:
  application:
    name: springcloud-config-dept-8001
```

然后在前两个项目启动的情况下启动此项目

访问正常的业务还是可以访问

![image-20210204142520033](https://s2.loli.net/2022/05/24/FzBWDAdKg3YQtLx.png)



### 小结

- Spring Cloud Config 说白了就是把配置文件放到云端托管，而且有利于多人合作开发
- 如果在项目启动后更改了云端的配置文件，要重启项目







# 项目全部结构

![image-20210204142827790](https://s2.loli.net/2022/05/24/3PaGeNpE2ZnoTmi.png)















