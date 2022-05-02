#### Cookie

​	Cookie是服务器通知客户端保存键值对的一种技术。

​	客户端有了Cookie后，每次请求都发送给服务器。

​	每个Cookie的大小不能超过4kb。

###### 如何创建Cookie

```java
//创建Cookie对象
Cookie cookie = new cookie("key1","value1");
//通知客户端保存Cookie
resp.addCookie(cookie)
```



![image-20220126213217806](https://s2.loli.net/2022/01/26/SrcE2KHiVTZO53a.png)

###### 服务器获取客户端的Cookie

```java
protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws IOException {
    Cookie[] cookies = req.getCookies();
    for(Cookie cookie : cookies){
        //getName方法返回Cookie的key（名）
        //getValue方法返回Cookie的value（值）
        resp.getWriter().write("Cookie["+ cookie.getName() + "="+ 						cookie.getValue() +"]<br/>");
    }
}
```

![image-20220126214828764](https://s2.loli.net/2022/01/26/KQvaS9FAbifg4ew.png)

获取某个cookie：

```java
Cookie iWantCookie = null;

for(Cookie cookie :  cookie){
	if("key2".equals(cookie.getName())){
        iWantCookie = cookie;
        break;
    }
}
//如果不等于null，说明赋过值，也就是找到了需要的Cookie
if(iWantCookie != null){
    resp.getWriter.write("找到了需要的Cookie");
}
```

###### Cookie值的修改

方案①：

​	1、先创建一个要修改的同名的Cookie对象。

​	2、在构造器，同时赋予新的Cookie值。

​	3、调用response.addCookie(Cookie);。

```java
protected void updateCookie(HttpServletRequest req, HttpServletResponse resp) throws IOException {
		//1、先创建一个要修改的同名的Cookie对象。
		//2、在构造器，同时赋予新的Cookie值。
		Cookie cookie = new Cookie("key1", "newvalue1");
		//3、调用response.addCookie(Cookie);。 通知客户端保存修改
		resp.addCookie(cookie);

		resp.getWriter().write("key1的值已经修改！");
	}
```



方案②：

​	1、先查找到需要修改的Cookie对象。

​	2、调用serValue()方法赋予新的Cookie值。

​	3、调用response.addCookie()通知客户端保存修改。

​	__对于Cookie的值不因包含<span style="color:red;">汉字</span>，<span style="color:red;">空格</span>、<span style="color:red;">方括号</span>、<span style="color:red;">圆括号</span>、<span style="color:red;">等号</span>、<span style="color:red;">逗号</span>、<span style="color:red;">双引号</span>、<span style="color:red;">斜杠</span>、<span style="color:red;">问号</span>、<span style="color:red;">at符号</span>、<span style="color:red;">冒号</span>和<span style="color:red;">分号</span>，<span style="color:red;">空值</span>在浏览器上的行为不一定相同。(如需使用，因该<span style="color:red;">先用BASE64编码</span>后在使用)__

###### Cookie生命控制

Cookie的生命控制指的是如何管理Cookie什么时候被销毁（删除）

serMaxAge(int expiry)  设置cookie的最大生存时间，以秒为单位。

​	正值表示cookie将在经过改值表示的秒数后过期。注意，该值是Cookis过期的最大生存时间，不是cookie的当前生存时间。负值意味着cookie不会被持久存储，将在Web浏览器退出时删除(默认-1)。0值会导致删除cookis。

###### Cookie有效路径Path的设置

Cookie的path属性可以有效的过滤那些Cookie发给服务器。

path属性是通过请求的地址来进行有效的过滤。

CookieA    path=/工程路径

CookieB    path=/工程路径/abc

请求地址如下：

​		http://ip:port/工程路径/a.html     CookieA   发送      CookieB  不发送

​		http://ip:port/工程路径/abc/a.html     CookieA   发送      CookieB  发送

###### Cookie面输入用户名登录

![image-20220127201624853](https://s2.loli.net/2022/01/27/Tb57CgojpvkcNYm.png)