#### ThreadLocal

​	jdk提供的用于解决线程安全的工具类

​	作用：解决多线程的数据安全问题。

​	ThreadLocal它可以给当前线程关联一个数据（可以是普通变量，可以是对象，也可以是数组，集合）

​	ThreadLocal特点：

​			1、ThreadLocal可以为当前线程关联一个数据。（它可以像Map一样存取数据，key为当前线程）

​			2、每一个ThreadLocal对象只能为当前线程关联一个数据，如果要为当前线程关联多个数据，就需要使用多个ThreadLocal对象实例。

​			3、每个ThreadLocal对象实例定义的时候一般都是static类型。

​			4、ThreadLocal中保存的数据，在线程销毁后会由JVM虚拟机自动释放。