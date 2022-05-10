## ssm整合Thymeleaf

1.thymelef+spring官方文档:https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html

### 准备：

导入Thymeleaf 包：

```xml
<dependency>
  <groupId>org.thymeleaf</groupId>
  <artifactId>thymeleaf</artifactId>
  <version>3.0.11.RELEASE</version>
</dependency>
```

导入spring5-Thymeleaf整合包:

```xml
<dependency>
  <groupId>org.thymeleaf</groupId>
  <artifactId>thymeleaf-spring5</artifactId>
  <version>3.0.11.RELEASE</version>
</dependency>
```

配置spring-mvc.xml

```xml
<!--配置thymeleaf-->
<bean id="springResourceTemplateResolver" class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
  <property name="prefix" value="/templates/"/>
  <property name="suffix" value=".html"/>
  <!--解决页面的中文乱码-->
  <property name="characterEncoding" value="UTF-8"/>
  <property name="order" value="1"/>
  <property name="templateMode" value="HTML5"/>
  <property name="cacheable" value="false"/>
</bean>
<!--注入thymeleaf bean-->
<bean id="springTemplateEngine" class="org.thymeleaf.spring5.SpringTemplateEngine">
  <property name="templateResolver" ref="springResourceTemplateResolver"/>
</bean>
<!--视图解析器-->
<bean class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
  <property name="templateEngine" ref="springTemplateEngine" />
  <!-- NOTE 'order' and 'viewNames' are optional -->
  <property name="order" value="1" />
  <property name="characterEncoding" value="UTF-8"/>
</bean>
```

