#### Reflection
反射是指程序在运行期可以获得到一个对象的所有信息。  

反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。  

由于JVM为每个加载的class创建了对应的Class实例，并在实例中保存了该class的所有信息，包括类名、包名、父类、实现的接口、所有方法、字段等，因此，如果获取了某个Class实例，我们就可以通过这个Class实例获取到该实例对应的class的所有信息。这种通过Class实例获取class信息的方法称为反射（Reflection）。  

__如何获取一个class的Class实例？有三个方法：__  
方法一：直接通过一个class的静态变量class获取：
```
    Class cls = String.class;
```
方法二：如果我们有一个实例变量，可以通过该实例变量提供的getClass()方法获取：
```
    String s = "Hello";
    Class cls = s.getClass();
```
方法三：如果知道一个class的完整类名，可以通过静态方法Class.forName()获取：
```
    Class cls = Class.forName("java.lang.String");
```
__因为Class实例在JVM中是唯一的，所以，上述方法获取的Class实例是同一个实例。可以用==比较两个Class实例：__
```
    Class cls1 = String.class;

    String s = "Hello";
    Class cls2 = s.getClass();

    boolean sameClass = cls1 == cls2; // true
```
__注意一下Class实例比较和instanceof的差别：__
```
Integer n = new Integer(123);

boolean b1 = n instanceof Integer; // true，因为n是Integer类型
boolean b2 = n instanceof Number; // true，因为n是Number类型的子类

boolean b3 = n.getClass() == Integer.class; // true，因为n.getClass()返回Integer.class
boolean b4 = n.getClass() == Number.class; // false，因为Integer.class!=Number.class
```
用 *instanceof* 不但匹配指定类型，还匹配指定类型的子类。而用 __==__ 判断class实例可以精确地判断数据类型，但不能作子类型比较。

通常情况下，我们应该用 *instanceof* 判断数据类型，因为面向抽象编程的时候，我们不关心具体的子类型。只有在需要精确判断一个类型是不是某个class的时候，我们才使用==判断class实例。  

如果获取到了一个Class实例，我们就可以通过该Class实例来创建对应类型的实例：
```
// 获取String的Class实例:
Class cls = String.class;
// 创建一个String实例:
String s = (String) cls.newInstance();
```
上述代码相当于new String()。__通过Class.newInstance()可以创建类实例，它的局限是：只能调用public的无参数构造方法。带参数的构造方法，或者非public的构造方法都无法通过Class.newInstance()被调用。__