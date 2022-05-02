#### JSON

##### 1、什么是JSON?

​	__JSON__(JavaScript Object Notation)是一种轻量级的数据交换格式。采用完全独立于语言的文本格式。

json是一种轻量级的数据交换格式。

* 轻量级是指跟xml作比较。
* 数据交换指的是客户端和服务器之间业务数据的传递格式。

##### 1.1 JSON在JavaScript中的使用

###### 1.1.1 json的定义

​		json是由键值对组成，并且由花括号(大括号)包围。每个键由引号引起来，键和值之间使用冒号进行分隔，多组键值对之间使用逗号分隔。

```json
var jsonobj = {
    "key1":12,
    "key2":"abc",
    "key3":true,
    "key4":[11,"arr",false],
    "key5":{
        "key5_1":551,
        "key5_2":"key5_2_value"
    },
    "key6":[{
        "key6_1_1":6611,
        "key6_1_2":"key6_1_2_value"
    },{
        "key6_2_1":6611,
        "key6_2_2":"key6_2_2_value"
    }]
};
```

###### 1.1.2、json的访问

​		json本身是一个对象。

​		json中的key我们可以理解为是对象中的属性。

​		json中的key访问就跟访问对象的属性一样，json对象.key

```js
// json的访问
alert(jsonobj.key1); //12
alert(jsonobj.key2); //abc
alert(jsonobj.key2); //true

var arr = [11,"arr",false];
alert(jsonobj.key4); //得到数组[11,"arr",false]
//json中数组值的遍历
for(var i=0;i<jsonobj.key4.length;i++){
	alert(jsonobj.key4[i]);
}

alert(jsonobj.key5.key5_1) //551
alert(jsonobj.key5.key5_2) //key5_2_value

//得到json数组
alert(jsonobj.key6);

//取出来每个都是json对象
var jsonItem = jsonobj.key6[0];
alert(jsonItem.key6_1_1); //6611
```

###### 1.1.3、json的两个常用方法

​		json的存在有两种形式。(对象(json对象)和字符串(json字符串))。

​		一般我们要操作json中的数据的时候，需要json对象的格式。

​		一般我们要在客户端和服务器之间进行数据交换的时候，使用json字符串。

​	JSON.stringify()             把json对象转换成为json字符串

​	JSON.parse()    			 把json字符串转换成为json对象

```js
//alert(jsonObj);

//把json对象转换为json字符串
var jsonObjString = JSON.stringify(jsonObj);
alert(jsonObjString)
// json字符串转json对象
var jsonObj2 = JSON.parse(jsonObjString);
alert(jsonObj2.key1); //12
alert(jsonObj2.key2); //abc
```

##### 1.2、JSON在java中的使用

###### 1.2.1、javaBean 和 json 的互转

```java
Person person = new Person(1,"lian");
//创建Gson对象实例
Gson gson = new Gson();
//toJson方法可以将java对象转换为json字符串
String s = gson.toJson(person);
System.out.println(s);
//fromJson把json字符串转换为java对象
//第一个参数是json字符串
//第二个参数是转换回去的java对象类型
Person person1 = gson.fromJson(s, Person.class);
System.out.println(person1);
```

###### 1.2.2、List 和 json 的互转

```java
public class PersonListType extends TypeToken<ArrayList<Person>> {
}
```

```java
List<Person> personList = new ArrayList<>();
personList.add(new Person(1,"abc"));
personList.add(new Person(2,"def"));
personList.add(new Person(3,"ghi"));

//创建Gson对象实例
Gson gson = new Gson();
//把list集合转换为json字符串
String s = gson.toJson(personList);
System.out.println(s);

List<Person> list = gson.fromJson(s, new PersonListType().getType());
System.out.println(list);
Person person = list.get(0);
System.out.println(person);
```

###### 1.2.3、map 和 json 的互转

```java
public class PersonMapType extends TypeToken<HashMap<Integer, Person>> {
}
```

```java
Map<Integer,Person> personMap = new HashMap<>();

personMap.put(1, new Person(1,"lian"));
personMap.put(2, new Person(1,"abc"));

Gson gson = new Gson();
//把Map集合转换为json字符串
String s = gson.toJson(personMap);
System.out.println(s);

Map<Integer,Person> personMap2 = gson.fromJson(s, new PersonMapType().getType());
System.out.println(personMap2);
Person person = personMap2.get(1);
System.out.println(person);
```

使用匿名内部类解决需创建类来继承TypeToken实现list和Map从字符串转对象出现的map无法转换为person对象的问题。

```java
Map<Integer,Person> personMap2 = gson.fromJson(s, new TypeToken<HashMap<Integer,Person>>(){}.getType());
```

