## Mybatis-01(模糊查询的四种实现方式)

在 MySQL 中，LIKE 关键字主要用于搜索匹配字段中的指定内容。其语法格式如下：

[NOT] LIKE '字符串'

其中：

- NOT ：可选参数，字段中的内容与指定的字符串不匹配时满足条件。
- 字符串：指定用来匹配的字符串。“字符串”可以是一个很完整的字符串，也可以包含通配符。


LIKE 关键字支持百分号“%”和下划线“_”通配符。

> 通配符是一种特殊语句，主要用来模糊查询。当不知道真正字符或者懒得输入完整名称时，可以使用通配符来代替一个或多个真正的字符。 

### 带有“%”通配符的查询

“%”是 MySQL 中最常用的通配符，它能代表任何长度的字符串，字符串的长度可以为 0。例如，`a%b`表示以字母 a 开头，以字母 b 结尾的任意长度的字符串。该字符串可以代表 ab、acb、accb、accrb 等字符串。

### Mybatis 模糊查询方式一：

> 手动添加 "%" 通配符

* xml配置

    ```xml
    <select id="queryByfuzzy" resultType="com.bin.pojo.Book">
        select * from mybatis.book where bookName like #{info};
    </select>
    ```

* 测试

    ```java
    //service实现类
    public List<Article> queryBlogByTitleAndFuzzy(String title) {
    		title = "%"+title+"%";
    		return articleMapper.queryBlogByTitleAndFuzzy(title);
    }
    //测试类
    @Test
    public void queryBlogByTitleAndFuzzy(){
    		ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    		articleServiceImpl articleServiceImpl = applicationContext.getBean("articleServiceImpl", articleServiceImpl.class);
    		System.out.println(articleServiceImpl.queryBlogByTitleAndFuzzy("spring"));
    }
    ```

**说明：需要手动添加"%"通配符，显然这种方式很麻烦，并且如果忘记添加通配符的话就会变成普通的查询语句，匹配全部字符查询。**

### Mybatis 模糊查询方式二：

> 在xml配置文件中添加"%"通配符，拼接字符串形式

```xml
<select id="fuzzyQuery" resultType="com.bin.pojo.Book">
    select * from mybatis.book where bookName like '%${info}%';
</select>
```

**说明：在mapper.xml配置文件中添加"%"通配符，但是需要用单引号将其包裹住，但是用单引号裹住之后#{}就无法被识别，要改成${}这种拼接字符串的形式。虽然通过方式二优化了方式一的缺点，但同时也造成了SQL安全性的问题，也就是用户可以进行SQL注入。**

### Mybatis 模糊查询方式三：

> 在xml配置文件中添加"%"通配符，借助mysql函数

```xml
<select id="fuzzyQuery" resultType="com.bin.pojo.Book">
    select * from mybatis.book where bookName like 
    concat('%',#{info},'%');
</select>
```

**说明：解决了SQL注入且能在配置文件中写"%"通配符的问题，完美实现了模糊查询**

### Mybatis 模糊查询方式四：

> 使用mysql函数，但使的用是${}形式，不过需要用单引号包裹住

```xml
<select id="fuzzyQuery" resultType="com.bin.pojo.Book">
    select * from mybatis.book where bookName like 
    concat('%','${info}','%');
</select>
```

#### 总结：

- \#{}是预编译处理，mybatis在处理#{}时，会将其替换成"?"，再调用PreparedStatement的set方法来赋值。
- ${}是拼接字符串，将接收到的参数的内容不加任何修饰的拼接在SQL语句中，会引发SQL注入问题。