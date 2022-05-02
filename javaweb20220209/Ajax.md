#### AJAX请求

##### 1.1、什么是AJAX请求

​	AJAX即 " _Asynchronous_  _JavaScript_ _And_ _XML_"(异步JavaScript 和 XML)，是指一种创建交互式网页应用的网页开发技术。

​	<span style="color:red;">ajax是一种浏览器通过js异步发起请求，局部更新页面的技术。</span>



​	<span style="color:red;">Ajax请求的局部更新：浏览器地址栏不会发生变化    局部更新不会舍弃原来页面的内容</span>

##### 1.2、原生AJAX请求的示例

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="pragma" content="no-cache" />
        <meta http-equiv="cache-control" content="no-cache" />
        <meta http-equiv="Expires" content="0" />
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Insert title here</title>
        <script type="text/javascript">
        //在这里是用javaScript语言发起Ajax请求，访问服务器AjaxServlet中的javaScriptAjax
        function ajaxRequest() {
// 				1、我们首先要创建XMLHttpRequest 
        	var xmlhttprequest = new XMLHttpRequest();
// 				2、调用open方法设置请求参数
            xmlhttprequest.open("GET","http://localhost:8080/json_ajax_i18n/ajaxServlet?action=javaScriptAjax",true);
// 				4、在send方法前绑定onreadystatechange事件，处理请求完成后的操作。
            xmlhttprequest.onreadystatechange = function () {
            	if(xmlhttprequest.readyState == 4 && xmlhttprequest.status ==200){
            		var jsonObj = JSON.parse(xmlhttprequest.responseText);
            		//把响应的数据放入页面
            		document.getElementById("div01").innerHTML = "编号" + jsonObj.id + "姓名" + jsonObj.name;
                }
            }
// 				3、调用send方法发送请求
                xmlhttprequest.send()
        }
        </script>
    </head>
    <body>	
        <button onclick="ajaxRequest()">ajax request</button>
        <div id="div01">
        </div>
    </body>
</html>
```

##### 1.3、jQuery中的 AJAX 请求

​	$.aiax 方法

​			url									表示请求的地址

​			type								 表示请求的类型GET或POST请求

​			data								 表示发送给服务器的数据

​							格式有两种：&name=value&name1=value2        {key:value} 

​			success						   请求响应，响应的回调函数

​			dataType						响应的数据类型 

​							常用的数据类型有：text  表示纯文本   XML 表示XML 数据   json   表示json对象

```js
$.ajax({
	url: "http://localhost:8080/json_ajax_i18n/ajaxServlet",
	//data: "action=jQueryAjax",
	data:{
		action:"jQueryAjax"
	},
	type: "GET",
	success: function (data) {
		//alert("服务器返回的数据是："+data);
		//var jsonObj = JSON.parse(data);
		$("#msg").html("编号："+ data.id + "姓名：" + data.name);
	},
	dataType: "json"
});
```

​	$.get 方法和  \$.post 方法

​			url									  表示请求的地址

​			data								   表示发送给服务器的数据

​			callback							 载入成功的回调函数

​			type								   返回的数据类型

```js
$.get("http://localhost:8080/json_ajax_i18n/ajaxServlet","action=jQueryGet",function (data){
	$("#msg").html("get 编号："+ data.id + "姓名：" + data.name);
},"json");
$.get("http://localhost:8080/json_ajax_i18n/ajaxServlet","action=jQueryPost",function (data){
	$("#msg").html("post 编号："+ data.id + "姓名：" + data.name);
},"json");
```

​	$.getJSON 方法

​			url										表示请求的地址

​			data									 表示发送给服务器的数据

​			callback     						 载入成功的回调函数

​	表单序列化 serialize()

​	serialize()可以把表单中的所有表单项的 内容都获取到，并以name=value&name=value的形式进行拼接。