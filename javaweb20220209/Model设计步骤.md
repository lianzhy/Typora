#### Model编码步骤

1、编写xx模块的数据库表

2、编写xx模块的JavaBean

3、编写xx模块的Dao和测试Dao

4、编写xx模块的Service和Service

5、编写xx模块的Web层，和页面联调测试

eg：__图书模块__

​	1、编写图书模块的数据库表

```sql
USE book;

CREATE TABLE t_book(
	id INT PRIMARY KEY AUTO_INCREMENT,
	`name` VARCHAR(100),
	price DECIMAL(11.2),
	author VARCHAR(100),
	sales INT,
	stock INT,
	img_path VARCHAR(200)
);
ALTER TABLE t_book CHANGE NAME NAME VARCHAR(255) CHARACTER SET utf8;

ALTER TABLE t_book CHANGE author author VARCHAR(255) CHARACTER SET utf8;

INSERT INTO t_book(`id`,`name`,`author`,`price`,`sales`,`stock`,`img_path`)
VALUES(NULL,'Vue框架','赵六', 52 , 347 , 256 , 'static/img/default.jpg');

INSERT INTO t_book(`id`,`name`,`author`,`price`,`sales`,`stock`,`img_path`)
VALUES(NULL,'React','李四', 67 , 234 , 123 , 'static/img/default.jpg');

INSERT INTO t_book(`id`,`name`,`author`,`price`,`sales`,`stock`,`img_path`)
VALUES(NULL,'SpringBoot框架','王五', 62 , 210 , 106 , 'static/img/default.jpg');

SELECT id,`name`,author,price,sales,stock,img_path FROM t_book;
```

​	2、编写模块的JavaBean

```java
	private Integer id;
	private String name;
	private String author;
	private BigDecimal price;
	private Integer sales;
	private Integer stock;
	private String imgpath = "static/img/default.jpg";

```

​	3、编写图书模块的Dao和测试Dao

​		<span style="color:red;">接口中全是public。</span>

​		<span style="color:red;">如果数据库表的列名和pojo/domain/entity中的Bean的属性不一致的情况，需要使用别名进行兼容才能填充Bean，因此这种情况不能直接select *</span>

```java
package com.junit;

import com.dao.BookDao;
import com.dao.impl.BookDaoImpl;
import com.domain.Book;
import org.junit.Test;

import java.math.BigDecimal;

import static org.junit.Assert.*;

public class BookDaoTest {

	private BookDao bookDao = new BookDaoImpl();

	@Test
	public void addBook() {
		bookDao.addBook(new Book(null,"book1","lianzhy",new BigDecimal(999),11000,0,null));
	}

	@Test
	public void deleteBook() {
	}

	@Test
	public void updateBook() {
		bookDao.updateBook(new Book(21,"软件工程","张无",new BigDecimal(999),11000,0,null));

	}

	@Test
	public void queryBookById() {
		System.out.println(bookDao.queryBookById(21));
	}

	@Test
	public void queryBooks() {
		for (Book book : bookDao.queryBooks()) {
			System.out.println(book);
		}
	}
}
```

​	4、编写图书模块的Service和测试Service

​	5、编写图书模块的Web层，和页面联调测试

图书列表功能流程：

![image-20220124172108097](https://s2.loli.net/2022/01/24/hUVkePmsOTD9WFA.png)

图书列表管理流程：

![image-20220124190431071](https://s2.loli.net/2022/01/24/mCTyQhcOKn2fsXq.png)

表单重复提交：

​		当用户提交完请求后，浏览器会记录下最后一次请求的全部信息。当用户按下功能键F5，就会转发浏览器记录的最后一次请求。

页面无法直接跳转(需携带数据，应当经过servlet转发或重定向将数据保存到request域中)

删除图书功能流程：

![image-20220124203615133](https://s2.loli.net/2022/01/24/CaLrkIMu2Ueopl1.png)

修改图书功能流程：

1、把修改的图书信息回显到表单项中

![image-20220124214237517](https://s2.loli.net/2022/01/24/LptwK1JQ5WHXPNk.png)

2、提交修改后的数据给服务器保存修改

![image-20220124221821315](https://s2.loli.net/2022/01/24/dCHLDBAlUYKRXwq.png)

图书管理分页功能实现流程：

![image-20220125130920332](https://s2.loli.net/2022/01/25/Q6bKM7A2PshZTOE.png)

分页模块中，页码1，2，【3】，4，5的显示，要显示五个连续的页码，并且页码可以点击跳转。

需求：显示5个连续的页码，而且当前页码在中间。除了当前页码之外，每个页码都可以点击跳到指定页。

__情况1：如果总页码小于等于5的情况，页码的范围是：1到总页码__

1页     1

2页     1，2

3页	 1，2，3

4页	 1，2，3，4

5页	 1，2，3，4，5

__情况2：总页码大于5的情况。假设一共10页__

①当前页码为前面3个：1，2，3的情况

​	【1】，2，3，4，5        

​	  1，【2】，3，4，5                                 

​	  1，2，【3】，4，5

②当前页码为最后3个，8，9，10 页码范围是：总页码减4 ---总页码

6，7，【8】，9，10

6，7，8，【9】，10

6，7，8，9，【10】

③4，5，6，7  页码范围是：当前页吗减2---当前页码加2

2，3，4，5，6

3，4，5，6，7

5，6，7，8，9

```jsp
<%--大于首页，才显示--%>
<c:if test="${ requestScope.page.pageNo > 1 }">
	<a href="manager/bookServlet?action=page&pageNo=1">首页</a>
    <a href="manager/bookServlet?action=page&pageNo=${ requestScope.page.pageNo-1 }">上一页</a>
    </c:if>
    <%--页码输出的开始--%>
    <%--<c:if test="${ requestScope.page.pageNo > 1 }">
    <a href="manager/bookServlet?action=page&pageNo=${ requestScope.page.pageNo-1}">${ requestScope.page.pageNo-1 }</a>
    </c:if>
    【${ requestScope.page.pageNo}】
    <c:if test="${ requestScope.page.pageNo < requestScope.page.pageTotal }">
    <a href="manager/bookServlet?action=page&pageNo=${ requestScope.page.pageNo+1}">${ requestScope.page.pageNo+1 }</a>
     </c:if>--%>
<c:choose>
     <%--情况1：如果总页码小于等于5的情况，页码的范围是：1到总页码--%>
     <c:when test="${ requestScope.page.pageTotal <= 5 }">
     	<c:set var="begin" value="1"/>
        <c:set var="end" value="${requestScope.page.pageTotal}"/>
     </c:when>
     <%--情况2：总页码大于5的情况。假设一共10页--%>
     <c:when test="${ requestScope.page.pageTotal > 5}">
        <c:choose>
            <%--①当前页码为前面3个：1，2，3的情况--%>
            <c:when test="${ requestScope.page.pageNo <= 3}">
                <c:set var="begin" value="1"/>
                <c:set var="end" value="5"/>
            </c:when>
            <%--②当前页码为最后3个，8，9，10 页码范围是：总页码减4 ---总页码--%>
            <c:when test="${ requestScope.page.pageNo > requestScope.page.pageTotal - 3 }">
                <c:set var="begin" value="${ requestScope.page.pageTotal - 4 }"/>
                <c:set var="end" value="${requestScope.page.pageTotal}"/>
            </c:when>
            <%--③4，5，6，7  页码范围是：当前页吗减2---当前页码加2--%>
            <c:otherwise>
                <c:set var="begin" value="${ requestScope.page.pageNo - 2 }"/>
                <c:set var="end" value="${ requestScope.page.pageNo + 2 }"/>
            </c:otherwise>
        </c:choose>
    </c:when>
</c:choose>
<c:forEach begin="${begin}" end="${ end }" var="i">
    <c:if test="${ i == requestScope.page.pageNo }">
        【${ i }】
    </c:if>
    <c:if test="${ i != requestScope.page.pageNo }">
        <a href="manager/bookServlet?action=page&pageNo=${i}">${ i }</a>
    </c:if>
</c:forEach>
<%--页码输出的结束--%>

<%--如果已经是最后一页，则不在显示下一页，末页--%>
<c:if test="${ requestScope.page.pageNo < requestScope.page.pageTotal }">
    <a href="manager/bookServlet?action=page&pageNo=${ requestScope.page.pageNo+1 }">下一页</a>
    <a href="manager/bookServlet?action=page&pageNo=${ requestScope.page.pageTotal }">末页</a>
</c:if>
共${ requestScope.page.pageTotal}页，${ requestScope.page.pageTotalCount}条记录 到第
<input  value="${param.pageNo}" name="pn" id="pn_input"/>页
<input id="searchPageBtn" type="button" value="确定">
```

前台价格区间功能实现流程：

![image-20220126155250256](https://s2.loli.net/2022/01/26/qT91d7Xv4weJf3s.png)

###### 表单重复提交-----验证码

​		一：提交完表单。服务器使用请求转发来进行页面跳转。这个时候，用户按下功能键F5，就会发起最后一次的请求。造成表单重复提交问题。<span style="color:red">解决办法：使用重定向来进行跳转</span>

​		二：用户正常提交服务器，但是由于网络延迟等原因，迟迟未收到服务器的响应，这个时候，用户以为提交失败，就会多次提交，也会造成表单重复提交。

​		三：用户正常提交服务器。服务器页没有延迟，但是提交完成后，用户回退浏览器。重新提交。也会造成表单重复提交。

![image-20220128185617092](https://s2.loli.net/2022/01/28/9cVNuCDZQ6hanAs.png)

谷歌Kaptcha图片验证码的使用

​	1、导入谷歌验证码的jar包

​				kaotch-2.3.2,jar

​	2、在web.xml中去配置用于生成验证码的Servlet程序

```xml
<servlet>
    <servlet-name>KaptchaServlet</servlet-name>
    <servlet-class>com.google.code.kaptcha.servlet.KaptchaServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>KaptchaServlet</servlet-name>
    <url-pattern>/kaptcha.jpg</url-pattern>
</servlet-mapping>
```

​	3、在表单里使用img标签去显示验证码图片并使用它

```html
<form action="http://localhost:8080/tmp/registServlet" method="get">
  用户名：<input type="text" name="username" > <br>
  验证码：<input type="text" style="width: 60px;" name="code">
  <img src="http://localhost:8080/tmp/kaptcha.jpg" alt="" style="width: 100px; height: 28px;"> <br>
  <input type="submit" value="登录">
</form>
```

​	4、在服务器获取谷歌生成的验证码和客户端发送过来的验证码比较

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 获取Session中的验证码
    String token = (String) req.getSession().getAttribute(KAPTCHA_SESSION_KEY);
    // 删除 Session中的验证码
    req.getSession().removeAttribute(KAPTCHA_SESSION_KEY);


    String code = req.getParameter("code");
    // 获取用户名
    String username = req.getParameter("username");

    if (token != null && token.equalsIgnoreCase(code)) {
        System.out.println("保存到数据库：" + username);
        resp.sendRedirect(req.getContextPath() + "/ok.jsp");
    } else {
        System.out.println("请不要重复提交表单");
    }
}
```

###### 购物车模块

![image-20220128201731293](https://s2.loli.net/2022/01/28/nCWm2UhI8dvY1wt.png)

###### 加入购物车重定向返回当前页

```java
resp.sendRedirect(req.getHeader("Referer"));
```

###### 修改购物车商品数量

![image-20220129204933848](https://s2.loli.net/2022/01/29/EWMCPte6Tilg8h5.png)

购物车数据回显到主页显示：

​			方案①：在Cart对象中定义变量来实现最后添加物品的名字。

​			方案②：在session域中保存最后添加的信息。(重定向不支持request域数据共享)

###### 订单模块

![image-20220129220618291](https://s2.loli.net/2022/01/29/ZQPusrEie8OjpRl.png)

1、数据库表的创建：

```sql
CREATE TABLE t_order( 
    `order_id` VARCHAR(50) PRIMARY KEY, 
    `create_time` DATETIME, 
    `price` DECIMAL(11,2), 
    `status` INT, 
    `user_id` INT, 
    FOREIGN KEY(`user_id`) REFERENCES t_user(`id`) 
); 
CREATE TABLE t_order_item( 
    `id` INT PRIMARY KEY AUTO_INCREMENT, 
    `name` VARCHAR(100), 
    `count` INT, 
    `price` DECIMAL(11,2), 
    `total_price` DECIMAL(11,2), 
    `order_id` VARCHAR(50), 
    FOREIGN KEY(`order_id`) REFERENCES t_order(`order_id`) 
); 
```

2、创建订单模块的数据模型

```java
public class Order {
	//订单号(唯一)
	private String orderId;
	//下单时间
	private Date createTime;
	//金额
	private BigDecimal price;
	//订单状态 0未发货 1已发货 2已签收
	private final Integer status = 0;
	//用户编号
	private Integer userId;
	......
}
public class OrderItem {
	//主键编号
	private Integer id;
	//商品名称
	private String name;
	//数量
	private Integer count;
	//单价
	private BigDecimal price;
	//总价
	private BigDecimal totalPrice;
	//订单号
	private String orderId;

}
```

3、编写订单模块的dao程序和测试

4、编写订单模块的Service和测试

#### 使用AJAX验证用户名是否可用

![image-20220209162126457](https://s2.loli.net/2022/02/09/V95f8SLlYBt6TmX.png)

#### 使用AJAX将商品加入购物车

![image-20220209170626642](https://s2.loli.net/2022/02/09/eDEbFuHprxtP8VC.png)
