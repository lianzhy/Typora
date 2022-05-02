#### JSTL标签库(JSP Standard Tag Library)

标签库则是为了替代代码脚本。

JSTL有五组不同功能的标签库组成：

|                  功能范围                  |                URI                 | 前缀 |
| :----------------------------------------: | :--------------------------------: | :--: |
| <span style="color:red;">核心标签库</span> |    http://java.sun.com/jsp/core    |  c   |
|                   格式化                   |    http://java.sun.com/jstl/fmt    | fmt  |
|                    函数                    | http://java.sun.com/jstl/functions |  fn  |
|               数据库(不使用)               |    http://java.sun.com/jstl/sql    | sql  |
|                XML(不使用)                 |    http://java.sun.com/jstl/xml    |  x   |

在jsp标签库中使用taglib指令引入标签库

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/core"%>
```



