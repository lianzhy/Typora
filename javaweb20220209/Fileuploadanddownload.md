#### File upload

​	1、要有一个from标签，method=post请求。

​	2、form标签的encType属性值必须为multipart/form-data(表示提交的数据以多段(每一个表单项一个数据段)的形式进行拼接，然后以二进制流的形式发送给服务器)值。

​	3、在form标签中使用input type=file添加上传的文件。

​	4、编写服务器代码接收(servlet程序)，处理上传的数据。

![image-20220122155427706](https://s2.loli.net/2022/01/22/aZ5ebEiCd97Imon.png)

处理客户端浏览器发送的流数据(commons-upload.jar，commons-io.jar)

#### File Download

![image-20220122163845121](https://s2.loli.net/2022/01/22/Tori8Zz9GkfDNSn.png)