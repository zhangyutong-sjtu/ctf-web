# sql注入的初级知识

本章节主要介绍有关sql注入的一些数据库查询语句和PHP函数。

## 与MySQL注入相关的知识点

### MySQL数据库中的重要表名

在MySQL 5.0版本后，MySQL默认在数据库中存放一个“information_schema”的数据库，在该库中有3个重要的表名，分别为：

（1）SCHEMATA 

存储该用户创建的所有数据库的库名，其中该表中记录数据库库名的字段名为 SCHEMA_NAME。

（2）TABLES

存储该用户创建的所有数据库的库名和表名，其中记录数据库库名的字段名为 TABLE_SCHEMA， 记录表名的字段为 TABLE_NAME。

（3）COLUMNS

存储该用户创建的所有数据库的库名、表名和字段名。其中记录数据库库名的字段为 TABLE_SCHEMA，记录表名的字段名为 TABLE_NAME，记录字段名的字段名为 COLUMN_NAME。

### MySQL数据库查询语句

不知任何条件

```markdown
SELECT 要查训的表名 FROM 库名.表名;

```

已知一条条件

```markdown
SELECT 要查询的字段名1，字段名2... FROM 库名.表名 WHERE 已知条件的字段名 = '已知条件的值';

```

已知两条条件

```markdown
SELECT 要查询的字段名 FROM 库名.表名 WHERE 已知条件1的字段名 = '已知条件1的值' AND 已知条件2的字段名 = '已知条件2的值;

```

### 操作符及函数

（1） UNION操作符

用于合并两个或多个SELECT语句的结果集（UNION内部的SELECT语句必须拥有相同数量的列，列必须拥有相似的数据类型，每条SELECT语句中的列的顺序必须相同）。

```markdown
SELECT column_name1 FROM table1

UNION

SELECT column_name1 FROM table2;

```

默认UNION操作符选取不同值，如允许重复，使用`UNION ALL`。

（2）limit

limit的使用格式为limit m,n ，其中m为记录的开始（0是第一条），n为取几条记录，如

```markdown
SELECT * FROM users limit 0,2;
```

返回的就是符合条件的第1、2条，共两条

（3）concat()

CONCAT函数用于将两个或多个字符串连接为一个字符串

语法：CONCAT(str1,str2,...)

返回结果为连接参数产生的字符串，如有任何有一个参数为NULL，则返回值为NULL，例如：

```markdown
SELECT CONCAT(id, ‘，’, name) AS con FROM info LIMIT 1;

```

（4）concat_ws()

CONCAT_WS() 代表 CONCAT With Separator ，是CONCAT()的特殊形式。

语法：CONCAT(separator,str1,str2,...)

separator：其他参数的分隔符，位置在要连接的两个字符之间，可以为字符串、其他参数（若分隔符为NULL，结果为NULL）。函数会忽略任何分隔符参数后NULL值，但不会忽略任何空字符串。例如：

```markdown
SELECT CONCAT_WS('_',id,name)AS con_ws FROM info LIMIT 1;

```

（5）group_concat()

返回由分组中的值连接组成的字符串。

语法：GROUP_CONCAT([DISTINCT] expr [,expr ...]  [ORDER BY {unsigned_integer | col_name | formula} [ASC | DESC] [,col ...]] [SEPARATOR str_val])  通过使用DISTINCT可以排除重复值，用ORDER BY 子句可以进行排序（后文会提到），SEPARATOR 为一个字符串值，被用于插入结果值中，缺省为“，”，可通过“”完全一处这个分隔符。可通过变量group_concat_max_len设置一个最大长度，执行`SET[SESSION|GLOBAL] group_concat_max_len = unsigned_integer;` ，如果最大长度被设置，结果值被剪切到这个最大长度。例如：

```markdown
SELECT locus,GROUP_CONCAT(id) FROM info WHERE locus IN('AB086827','AF040764') GROUP BY locus;

SELECT locus,GROUP_CONCAT(distinct id ORDER BY id DESC SEPARATOR '_') FROM info WHERE locus IN('AB086827','AF040764') GROUP BY locus;
```

（6）order by

ORDER BY用于根据指定列对结果集排序，默认升序，DESC为降序关键字

```markdown
SELECT A,B FROM Orders ORDER BY A (DESC);

```

（7）group by

GROUP BY用于结合合计函数，根据一个或多个列对结果集分组，例如：

```markdown
SELECT column_name, aggregate_function(column_name) /*aggregate_function()为聚合函数，常与GROUP BY 搭配使用*/

FROM table_name

WHERE column_name operator value

GROUP BY column_name;

```

（8）if()

语法：if(expr1,expr2,expr3) 其中，expr1是判断条件，expr2和expr3是符合expr1的自定义返回结果。

```markdown
SELECT IF( A.id = 'id1' , 'a' , 'b') as id from ASDF as A;

```

（9）sleep()

执行`select sleep(N)` 可以让此语句运行N秒种

（10）left()

语法：LEFT(str,len) 返回str最左边的len个字符，如果任何参数为NULL返回NULL。

```markdown
SELECT LEFT('ASDFGH',5);

```

(11)count()

语法： COUNT(column_name) 函数返回指定列的值的数目（NULL不计入）; COUNT(*)函数返回表中的记录数；COUNT(DISTINCT column_name)函数返回指定列的不同值数目

```markdown
SELECT COUNT(column_name) FROM table_name;

SELECT COUNT(*) FROM table_name；

SELECT COUNT(DISTINCT column_name) FROM table_name；

```
