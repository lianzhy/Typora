#### Session会话

​	Session是一个接口(HttpSession)。

​	Session就是会话。她是用来维护一个客户端和服务器之间关联的一种技术。

​	每个客户端都有一个自己的Session会话。

​	Session会话中，我们经常用来保存登录之后的信息(保存于服务端)

###### Seesion的创建和获取

​	request.getSession()

​			第一次调用：是创建Session会话，之后调用都是：获取前面创建好的Session会话对象。

​	boolean isNew()  判断是否为刚创建的Session会话。

​				true  表示刚创建   false   表示获取之前创建

​	getId()  得到Session的会话id值。每个会话都有一个会话号码。也就是ID值，而且这个ID值是唯一的。  

###### Session生命周期控制

public void setMaxInactiveInterval(int interval)  设置Session的超时时间，超过指定的时长，Session就会被销毁。(<span style="color:red;">修改个别Session的超时时长</span>)

```java
//单独设置超时时长
session.setMaxInactiveInterval(int interval);
```

​		值为正数的时候，设定Session的超时时长。

​		负数表示永不超时（极少使用）。

​		public void invalidate()  使此会话无效，然后取消对任何绑定它的对象的绑定。(让当前Session会话马上超时无效)

public int getMaxInactiveInterval()  获取Session的超时时长。

​	Session 默认的超时时长  30min

​	可以在web.xml中配置，修改web工程所有的默认超时时长：

```xml
<session-config>
    <session-timeout>30</session-timeout>
</session-config>
```

![image-20220127215312378](https://s2.loli.net/2022/01/27/vaqieAf9QmI3NPz.png)

示例代码：

```java
protected void life3(HttpServletRequest req, HttpServletResponse resp) throws IOException {
		//获取了Session的默认超时时长
		HttpSession session = req.getSession();
		//设置当前session3秒后超时
		session.setMaxInactiveInterval(3);
		resp.getWriter().write("Session的默认超时时长已修改3秒后超时");
	}
```

##### 浏览器和Session之间的技术内幕

Session技术，底层是基于Cookie技术实现的。

![image-20220127225436212](https://s2.loli.net/2022/01/27/vbpxEw6iP5I4yMQ.png)