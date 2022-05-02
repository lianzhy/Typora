#### EL表达式（Express Language，是表达式语言）

作用：代替jsp页面中的表达式脚本在jsp页面中进行数据的输出。

```jsp
<body>
  <%
    request.setAttribute("key","value");
  %>
    表达式脚本输出：<%=request.getAttribute("key1")==null?"":request.getAttribute("key1")%><br/>
    EL：${key1}

</body>
```

EL表达式的格式是：${表达式} 

EL表达式在输出null值的时候，输出的是空串。jsp表达式脚本输出null值的时候，输出的是null字符串。

##### EL表达式搜索域对象中的数据的顺序

当四个域中都有相同的key的数据的时候，

pageContext > request > session(浏览器关闭才消失) > application 

EL表达式 ${p.age}调用getAge()方法无需age变量。

###### EL表达式----运算

关系运算：

| 关系运算符 | 说    明 |         范       例          | 结果  |
| :--------: | :------: | :--------------------------: | :---: |
|  == 或 eq  |   等于   | \${ 5 == 5 } 或 ${ 5 eq 5 }  | true  |
|  != 或 ne  |  不等于  | \${ 5 != 5 } 或 ${ 5 nq 5 }  | false |
|  < 或 lt   |   小于   |  \${ 3 < 5 } 或 ${ 3 lt 5}   | true  |
|  > 或 gt   |   大于   | \${ 2 > 10 } 或 ${ 2 gt 10}  | false |
|  <= 或 le  | 小于等于 | \${ 5 <= 12 } 或 ${ 5 le 12} | true  |
|  >= 或 ge  | 大于等于 |  \${ 3 >= 5 } 或 ${ 3 ge 5}  | false |

逻辑运算：

| 逻辑运算符 | 说    明 |                      范      例                      | 结果  |
| :--------: | :------: | :--------------------------------------------------: | :---: |
| && 或 and  |  与运算  | \${ 12 == 12 && 12 < 11} 或 ${12 == 12 and 12 < 11}  | false |
| \|\| 或 or |  或运算  | \${ 12 == 12 \|\| 12 < 11} 或 ${12 == 12 or 12 < 11} | true  |
|  ! 或 not  | 取反运算 |             \${ !true } 或 ${ not true }             | false |

empty运算(可以判断一个数据是否为空，如果为空，则输出true，不为空，输出false。)

​	空：①值为null值的时候，为空。②值为空串的时候，为空。③值是Object类型数组，长度为零的时候。④list集合，元素个数为零。⑤map集合，元素个数为零。

三元运算    表达式1 ? 表达式2 : 表达式3  表达式1为true，则返回表达式2的值，否者，返回表达式3的值。

"."点运算和"[]"中括号运算符

​	.点运算，可以输出Bean对象中某个属性的值。

​	[]中括号运算，可以输出有序集合中某个元素的值。

​	并且[]中括号运算，还可以输出map集合key集合里含有特殊字符的key的值。

```jsp
<%
	Map<String,Object> map = new HashMap<String,Object>();
	map.put("a.a.a",aaaValue);
	map.put("b+b+b",bbbValue);
	map.put("c-c-c",cccValue);

	request.setAttribute("map",map);
%>
${ map['a.a.a'] } <br/>
${ map["b+b+b"] } <br/>
${ map['c-c-c'] } <br/>
```



##### EL表达式中的11个隐含对象(EL表达式中自己定义的，可以直接使用)

|     变    量     |      类     型       |                   作   用                    |
| :--------------: | :------------------: | :------------------------------------------: |
|   pageContext    |   PageContextImpl    |           获取jsp中的九大内置对象            |
|    pageScope     |  Map<String,Object>  |          获取pageContext域中的数据           |
|   requestScope   |  Map<String,Object>  |            获取Request域中的数据             |
|   sessionScope   |  Map<String,Object>  |            获取Session域中的数据             |
| applicationScope |  Map<String,Object>  |          获取ServletContext中的数据          |
|      param       |  Map<String,String>  |               获取请求参数的值               |
|   paramValues    | Map<String,String[]> |    获取请求参数的值，获取多个值的时候使用    |
|      header      |  Map<String,String>  |               获取请求头的信息               |
|   headerValues   | Map<String,String[]> |      获取请求头的信息，获取多个值的情况      |
|     cookies      | Map<String,Cookies>  |          获取当前请求的Cookies信息           |
|    initParam     |  Map<String,String>  | 获取web.xml中配置的<content-param>上下文参数 |

四个特定域中的属性

pageScope ==>>>>   pageContext域                       requestScope ==>>>> Request域

sessionScope ==>>>> Session域                      applicationScope ==>>>>ServletContext域

