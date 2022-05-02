## Maven

## 1. 主要内容

------

![Maven](https://s2.loli.net/2022/02/14/MjYQnCILkc7Eo5a.png)

## 2. Maven的简介

------

#### 2.1.简介

​	Maven 主要服务于基于 Java 平台的项目构建，依赖管理和项目信息管理。

​	无论是小型的开源类库项目，还是大型的企业级应用；无论是传统的瀑布式开发，还是流行的敏捷开发，Maven 都能大显身手。

#### 2.2.项目构建

#### 2.3.项目构建工具

​		__Ant构建__

​	最早的构建工具，基于IDE，大概是2000年的，当时流行的 java 构建工具，不过它的XML脚本编写格式让XML文件特别大。

​		__Maven【JAVA】__

​	项目对象模型，通过其描述信息来管理项目的构建，报告和文档的软件项目管理工具。它填补了Ant缺点，Maven第一次支持从网络上下载的功能，仍然采用xml作为配置文件格式。Maven专注的是依赖管理，使用 Java 编写。

​		**Gradle**

​	属于结合以上两个的优点，它继承了Ant的灵活和Maven的生命周期管理，它最后被google作为了Android御用管理工具。它最大的区别是不用XML作为配置文件格式，采用DSL格式，是的脚本更加简洁。

#### 2.4. Maven的四大特性

##### 2.4.1. 依赖管理系统

​	Maven 为 Java 世界引入了一个新的依赖管理系统 jar 升级时修改配置文件即可。在java世界中，可以用groupId、artifactId、version组成的Coordination（坐标）唯一标识一个依赖。

​	任何基于Maven构建的项目自身也必须定义这三项属性，生成的包可以是jar包，也可以是war或者jar包。一个典型的依赖引用如下：

```xml
<dependency>
	<groudId>javax.servlet</groudId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
</dependency>
```

**坐标属性的理解**

​	Maven坐标为各种组件引入了秩序，任何一个组件都必须明确定义自己的坐标。

**groupId**

​	定义当前Maven项目隶属的实际项目-公司名称。（jar包所在的仓库路径）由于Maven中模块的概念，因此一个实际的项目往往会被划分为很多模块。比如spring是一个实际项目，其对应的Maven模块会有很多，如spring-core，spring-webmvc等。

**artifactId**

​	该元素定义实际项目中的一个Maven模块-项目名，推荐的做法是使用项目名称作为artifactId的前缀。比如：spring-webmvc等。

**version**

​	该元素定义Maven项目当前所处的版本。

##### 2.4.2. 多模块构建

​	在Maven中需定义一个parent POM作为一组moudle的聚合POM。在该POM中可以使用<modules> 标签来定义一组子模块。parent POM不会有什么实际构建产出。而parent POM中的build配置以及依赖配置都会自动继承给module。

##### 2.4.3. 一致的项目结构

​	Maven的设计理念就是Conversion over configuration（约定大于配置）。其制定了一套项目目录结构作为标准的Java项目结构，解决不同 ide 带来的文件目录不一致问题。

##### 2.4.4. 一致的构建模型和插件机制

```xml
<plugin>
	<groupId>org.mortbay.jetty</groupId>
    <artifactId>maven-jetty-plugin</artifactId>
    <version>6.1.25</version>
    <configuration>
    	<scanIntervalSeconds>10</scanIntervalSeconds>
        <contextPath>/test</contextPath>
    </configuration>
</plugin>
```

## 3. Maven的安装配置和目录结构

------

#### 3.1. Maven的安装配置

##### 3.1.1. 检查JDK版本

​	JDK版本1.7及以上版本

##### 3.1.2. 下载Maven

​	下载地址：http://maven.apache.org/download.html

##### 3.1.1. 配置Maven环境变量

​	解压后把Maven的根目录配置到系统环境变量中MAVEN_HOME，将bin目录配置到path变量中。

##### 3.1.4. 检查Maven是否安装成功

​	打开dos，输入mvn -v

#### 3.2. 认识Maven目录结构

​	Maven项目目录结构

| 目录                           | 作用                             |
| ------------------------------ | -------------------------------- |
| ${base dir}                    | 存放 pom.xml和所有的子目录       |
| ${base dir}/src/main/java      | 项目的 java 源代码               |
| ${base dir}/src/main/resources | 项目的资源，比如说 property 文件 |
| ${base dir}/src/test/java      | 项目的测试类，比如说 Junit 代码  |
| ${base dir}/src/test/resources | 测试使用的资源                   |

## 4. Maven命令

​	_Maven的命令格式如下：_

```shell
mvn [plugin-name]:[goal-name]
```

命令代表的含义：执行` plugin-name`插件的` goal-name`目标
