#### MySQL UNION操作符

**描述：**MySQL UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中。多个 SELECT 语句会删除重复的数据。

**语法**

```sql
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions]
UNION [ALL | DISTINCT]
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions];
```

**参数**

* **expression1, expression2, ... expression_n**: 要检索的列。
* **tables:** 要检索的数据表。
* **WHERE conditions:** 可选， 检索条件。
* **DISTINCT:** 可选，删除结果集中重复的数据。默认情况下 UNION 操作符已经删除了重复数据，所以 DISTINCT 修饰符对结果没啥影响。
* **ALL:** 可选，返回所有结果集，包含重复数据。

#### MySQL IF函数：判断

[MySQL](http://c.biancheng.net/mysql/) IF 语句允许您根据表达式的某个条件或值结果来执行一组 SQL 语句。

要在 MySQL 中形成一个表达式，可以结合文字，变量，运算符，甚至函数来组合。表达式可以返回 TRUE,FALSE 或 NULL，这三个值之一。

**语法**

```sql
IF(expr,v1,v2)
```

其中：表达式 expr 得到不同的结果，当 expr 为真是返回 v1 的值，否则返回 v2.

#### **case**

case用法： case a when cond1 then exp1 else cond2 then exp2 else exp3

当a满足条件cond1时， 返回exp1 当a满足条件cond2时， 返回exp2 否则 返回exp3