# 了解SQL

## 数据库

**数据库** 保存有阻值的数据的容器（通常时一个文件或一组文件） 

数据库软件称为**DBMS（数据库管理系统）**。数据库是通过DBMS创建和操纵的容器。

## 表

**表** 某种特定类型数据的结构化清单

表是一种结构化文件，可用来储存某种特定类型的数据。

**模式（schema）** 关于数据库和表的布局及特性的信息

## 列

**列** 表中的一个字段。所有表都由一个或多个列组成的

数据库中每个列都有相应的数据类型

**数据类型** 所容许的数据的类型

每个表列都有相应的数据类型，它限制该列中存储的数据。数据类型还帮助正确地排列数据，并在优化磁盘使用方面起重要的作用。因此，在创建表时必须对数据类型给予特别地关注。

## 行

表中的一个记录

## 主键

**主键** 一列（或一组列），其值能够唯一区分表中的每个行。

**唯一**标识表中每行的这个列（或这组列）称为主键。每个表中都应该具有一个主键，以便于以后的数据操纵和管理。

表中的任何列都可以作为主键，需要满足如下**条件**：

- 任意两行都不具有相同的主键值
- 每个行都必须具有一个主键值（主键列不允许NULL值）

**主键的最好习惯**

- 不更新主键列中的值
- 不重用主键列的值
- 不在主键列中使用可能会改变的值（如果使用一个名字作为主键以标识某个供应商，当该供应商合并更改其名字时，必须更改这个主键）

# 4.检索数据

## 检索列

### 检索单个列：

```mysql
SELECT prod_name FROM products;
```

### 检索多个列：

```mysql
SELECT prod_id, prod_name, prod_price
FROM products;
```

在选择多个列时，一定要在列名之间加上逗号

### 检索所有列（*）

```mysql
SELECT * FROM products;
```

如果给定一个**通配符(*)**，则返回表中的所有列。列的顺序一般是列在表定义中出现的顺序。

### 检索不同的行（DISTINCT）

```mysql
SELECT DISTINCT vend_id FROM products;
```

如果使用DINSTINCT关键字，它必须直接放在列名的前面。

**不能部分使用DISTINCT**： DISTINCT关键字应用于所有的列而不仅是前置它的列。

如果给出 `SELECT DISTINCT vend_id, prod_price` （在我的测试下，distinct貌似只应用在了prod_price）

## 限制结果（LIMIT）

```mysql
SELECT prod_name FROM products LIMIT 5; 
SELECT prod_name FROM products LIMIT 5,5;
```

LIMIT 5 指示MySQL返回不多于5行；LIMIT 5，5指示MySQL返回从行5开始的5行。第一个数为开始位置，第二个数为要检索的行数。

**所以，带一个值的LIMIT总是第一行开始，给出的数为返回的行数。带两个值的LIMIT可以指定从行号为第一个值的位置开始。** 

**行0** ：检索出来的第一行为行0而不是行1。因此，LIMIT1，1将检索出第二行而不是第一行。

在行数不够时，MySQL只能返回它能返回的那么多行。

## 完全限定的表名

```mysql
SELECT products.prod_name FROM crashcourse.products;
```

# 5.排序检索数据

## 排序数据

为了明确地排序用SELECT语句检索出的数据，可使用ORDER BY子句。 ORDER BY子句取一个或多个列的名字，据此对输出进行排序。

```mysql
SELECT prod_name FROM products ORDER BY prod_name;
```

通常，ORDER BY子句中使用的列是所选择显示的列。

## 按多个列排序

```mysql
SELECT prod_id, prod_price, prod_name
FROM products 
ORDER BY prod_price, prod_name;
```

**在按多个列排序时，排序完全所规定的顺序进行**。上述例子中的输出，就是先对prod_price 排序。

## 排序方向

```mysql
SELECT prod_id, prod_price, prod_name
FROM products 
ORDER BY prod_price DESC, prod_name;
```

DESC关键字只应用直接位于其前面的列名。

**如果想在多个列上进行降序排序，必须对每个列指定DESC关键字**

使用**ORDER BY**和**LIMIT**的组合，能够找到列中最高或最低的值。

```mysql
SELECT prod_price
FROM products
ORDER BY prod_price DESC
LIMIT 1;
```

**在给出ORDER BY 子句时，应该保证它位于FROM子句之后。如果使用LIMIT，它必须位于ORDER BY 之后。使用子句的次序不对将会产生错位消息。**

# 6.7.过滤数据

## WHERE子句

|  操作符  |        说明        |
| :------: | :----------------: |
|    =     |        等于        |
| <> (! =) |       不等于       |
|  < (=)   |     小于(等于)     |
|  > (=)   |     大于(等于)     |
| BETWEEN  | 在指定的两个值之间 |

**单引号用来限定字符串**，如果将值与串类型的列进行比较，则需要限定引号。

## 空值检查

在创建表时，表设计人员可以指定其中的列是否可以不包含值。在一个列不包含值时，称其为包含空值**NULL**。

SELECT语句有个特殊的WHERE子句，可用来检查具有NULL值的列。这个WHERE子句就是IS NULL 子句。

```mysql
SELECT prod_name FROM products WHERE prod_price IS NULL;
```

## AND && OR 操作符

**操作符** 用来连结或改变WHERE子句中的子句的关键字。也称为**逻辑操作符**。

例子：

```mysql
SELECT prod_id, prod_price, prod_name
FROM products
WHERE vend_id = 1003 AND prod_price <= 10;
```

```mysql
SELECT prod_name, prod_price
FROM products
WHERE vend_id = 1002 OR vend_id = 1003;
```

**在处理OR操作符之前，优先处理AND操作符**

``` 
在WHERE子句中使用圆括号：任何时候使用具有AND和OR操作符的WHERE子句，都应该使用圆括号明确地分组操作符
```

## IN操作符

**IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。**

```mysql
SELECT prod_name, prod_price FROM products
WHERE vend_id IN (1002,1003) ORDER BY prod_name;
```

**IN操作符的优点**

- 在使用长的合法选项清单时，IN操作符的语法更清楚且更直观
- 在使用IN时，计算的次序更容易管理
- IN操作符一般比OR操作符清单执行更快
- IN的最大优点时可以包含其他SELECT语句，使得能够更动态地建立WHERE子句

## NOT操作符

```mysql
SELECT prod_name, prod_price FROM products
WHERE vend_id NOT IN (1002,1003) ORDER BY prod_name;
```

**MySQL支持使用NOT对IN、BETWEEN和EXISTS子句取反**

# 8.用通配符进行过滤

## LIKE操作符

**通配符(wildcard)     用来匹配值地一部分地特殊字符**

**搜索模式(search pattern)     由字面值、通配符或两者组合构成的搜索条件**

### 百分号(%) 通配符

**在搜索串中，%表示任何字符出现任意字符**

例如，为了找出所有以词 **jet** 起头的产品

```mysql
SELECT prod_id, prod_name FROM products
WHERE prod_name LIKE 'jet%';
```

例如， 为了找出以词 **anvil** 结尾的产品

```mysql
SELECT prod_id, prod_name FROM products
WHERE prod_name LIKE '%anvil';
```

例如，为了找出包含文本 **anvil** 的产品

```mysql
SELECT prod_id, prod_name FROM products
WHERE prod_name LIKE '%anvil%';
```

**搜索模式 '%anvil%' 表示匹配任何位置包含文本 anvil 的值， 不论它之前或之后出现什么字符**

通配符也可以出现在搜索模式的中间，例如找出s起头以e结尾的所有产品

```mysql
SELECT prod_name FROM products 
WHERE prod_name LIKE 's%e';
```

### 下划线(_) 通配符

**下划线(_) 匹配单个字符，不能多也不能少**

```mysql
SELECT prod_id, prod_name FROM products
WHERE prod_name LIKE '_ ton anvil';
```

# 9.用正则表达式进行搜索

## 基本字符匹配

与文字正文1000匹配的一个正则表达式

```mysql
SELECT prod_name FROM products 
WHERE prod_name REGEXP '1000' ORDER BY prod_name;
```

**. 是正则表达式语言中一个特殊的字符，它表示匹配任意一个字符**

```mysql
SELECT prod_name FROM products 
WHERE prod_name REGEXP '.000' ORDER BY prod_name;
```

## 进行OR匹配

```mysql
SELECT prod_name FROM products 
WHERE prod_name REGEXP '1000|2000' ORDER BY prod_name;
```

**| 为正则表达式的OR操作符，它表示匹配其中之一**

## 匹配几个字符之一（^,  [ ] )

```mysql
SELECT prod_name FROM products 
WHERE prod_name REGEXP '[123] ton' ORDER BY prod_name;
```

**[123]定义一组字符，他的意思是匹配1或2或3**

[]是另一种形式的OR语句。事实上，正则表达式[123] ton 为[1|2|3] ton 的缩写

字符集合也可以被否定，它们将匹配除指定字符外的任何东西。为否定一个字符集，在集合开始的位置放置一个 **^** 操作符即可。

[^123] 将匹配除123以外的任何东西

## 匹配范围

|       |                  |
| :---: | :--------------: |
| [a-z] | 匹配任意字母字符 |
| [0-9] |     匹配0到9     |

## 匹配特殊字符

|    元字符    |   说明   |
| :----------: | :------: |
|     \\\f     |   换页   |
|     \\\n     |   换行   |
|     \\\r     |   回车   |
|     \\\t     |   制表   |
|     \\\v     | 纵向制表 |
| \\\\.\|\\\\- |   .\|-   |
|    \\\\\     |    \     |

## 匹配字符类

略~~~

## 匹配多个实例

| 元字符 |             说明             |
| :----: | :--------------------------: |
|   *    |        0个或多个匹配         |
|   +    |    1个或多个匹配（{1，}）    |
|   ？   |   0个或多个匹配（{0，1}）    |
|  {n}   |        指定数目的匹配        |
|  {n,}  |     不少于指定数目的匹配     |
| {n,m}  | 匹配数目的范围（m不超过255） |

例子：

```mysql
SELECT prod_name
FROM products 
WHERE prod_name REGEXP '\\([0-9] sticks?\\)'
ORDER BY prod_name;
```

输出： TNT（1 stick） 和 TNT （5 sticks）

**分析： 正则表达式`\\([0-9] sticks?\\`中，\\\\( 匹配 ( , [0-9] 匹配任意数字， sticks?匹配stick和sticks，\\\\) 匹配 )** 

## 定位符

| 元字符  |    说明    |
| :-----: | :--------: |
|    ^    | 文本的开始 |
|    $    | 文本的结尾 |
| [[:<:]] |  词的开始  |
| [[:>:]] |  词的结尾  |

`^[0-9\\.]` 和 `anvil$`

**^的双重用途： ^有两种用法，在集合中（用 [ 和 ] 定义 ），用它来否定，否则，指串的开始处**

# 10.创建计算字段

## 计算字段

**字段(field)** 基本与列(column)的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常用在计算字段的连接上。

**拼接(concatenate)** 将值联结到一起构成单个值

```mysql
SELECT Concat(vend_name,' (', vend_country, ')')
FROM vendors
ORDER BY vend_name;
```

## 使用别名

```mysql
SELECT Concat(vend_name,' (', vend_country, ')') AS vend_title
FROM vendors
ORDER BY vend_name;
```

## 执行算术计算

```mysql
SELECT prod_id, quantity, item_price,
quantity*item_price AS expanded_price
FROM orderitems
WHERE order_num = 20005;
```

算术操作符：

| 操作符 | 说明 |
| :----: | :--: |
|   +    |  加  |
|   -    |  减  |
|   *    |  乘  |
|   /    |  除  |

# 11.数据处理函数

## 文本处理函数

|           函数           |             说明             |
| :----------------------: | :--------------------------: |
|     Left()\|Right()      |   返回串的左边\|右边的字符   |
|         Length()         |         返回串的字符         |
|         Locate()         |       找出串的一个字串       |
|      Upper()\|Lower      |      将串转为大写\|小写      |
| LTrim()\|Trim()\|RTrim() | 去掉串左边\|左右\|右边的空格 |
|        Soundex()         |      返回串的SOUNDEX值       |

Soundex() 是一个将任何文本串转换为描述其语音表示的字母数字模式的算法

```mysql
SELECT cust_name, cust_contact
FROM customers
WHERE Soundex(cust_contact) = 'Y. Lie';
```

在这个例子中会找到Y. Lee 因为它们发音相似。

## 日期和时间处理函数

函数表略~~~

**日期格式为 yyyy-mm-dd**

如果要的是日期，请使用Date() 如果你想要的仅是日期，则使用Date() 是一个良好的习惯，即使你知道相应的列只包含日期也是如此。

```mysql
SELECT cust_id, order_num
FROM orders
WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';
```

## 数值处理函数

函数表略~~~



# 12. 汇总数据

## 聚集函数

**聚集函数 (aggregate function)** 运行在行组上，计算和返回单个值的函数

|   函数   |            说明            |
| :------: | :------------------------: |
|  AVG( )  | 返回某列的平均值（忽略NULL |
| COUNT( ) |       返回某列的行数       |
|  MAX( )  | 返回某列的最大值（忽略NULL |
|  MIN( )  | 返回某列的最小值（忽略NULL |
|  SUM( )  |  返回某列值的和（忽略NULL  |

## COUNT 函数

COUNT函数有两种使用方法

- 使用COUNT(*) 对表中行的数目进行计数，不管表列中包含的是空值(NULL)还是非空值
- 使用COUNT(column名) 对特定列中具有值的行进行计数，忽略NULL值

## 聚集不同值

- 对所有的行执行计算，指定ALL参数或不给参数（因为ALL是默认行为）
- 只包含不同的值，指定DISTINCT参数

```mysql
SELECT AVG(DISTINCT prod_price) AS avg_price
FROM products
WHERE vend_id = 1003;
```

## 组合聚集函数

```mysql
SELECT COUNT(*) AS num_items,
MIN(prod_price) AS price_min,
MAX(prod_price) AS price_max,
AVG(prod_price) As price_avg
FROM products;
```



# 13.分组数据

## 创建分组

分组是在SELECT语句的GROUP BY子句建立的。

```mysql
SELECT vend_id, COUNT(*) AS num_prods
FROM products
GROUP BY vend_id;
```

分析：上面SELECT语句指定了两个列，vend_id 包含产品供应商的ID，num_prods 为计算字段(用COUNT(*)函数建立)。GROUP BY 子句指示MySQL 按vend_id 排序并分组数据。这导致对每个vend_id 而不是整个表计算num_prods 一次。

**GROUP BY 子句的重要的规定：**

- GROUP BY 子句可以包含任意数目的列，这使得能对分组进行嵌套，为数据分组提供更细致的控制
- 如果在GROUP BY 子句中嵌套了分组，数据将在最后规定的分组进行汇总
- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式(但不能是聚集函数)，**如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式，不能使用别名。**
- 如果分组列中具有NULL值，则NULL将作为一个分组返回。
- **GROUP BY子句必须出现在WHERE子句以后，ORDER BY子句之前**。一般在使用GROUP BY子句时，应该也要给出ORDER BY子句。

## 过滤分组

过滤分组使用HAVING子句

```mysql
SELECT cust_id, COUNT(*) AS orders
FROM orders
GROUP BY cust_id
HAVING COUNT(*) >= 2;
```

**HAVING和WHERE的差别： WHERE在数据分组前进行过滤，而HAVING在数据分组之后进行过滤。**所以说，WHERE过滤的是行，而HAVING过滤的是分组。只有当分组完成后（即进行GROUP BY后）才可以进行HAVING操作。

# 14.子查询

## 利用子查询进行过滤

子查询很好理解，就是嵌套使用SELECT语句，在SELECT语句中，子查询总是从内向外处理。

```mysql
SELECT cust_id FROM orders WHERE order_num IN
(SELECT order_num FROM orderitems WHERE prod_id = 'TNT2');
```

首先它执行

```mysql
SELECT order_num FROM orderitems WHERE prod_id = 'TNT2';
```

然后外部查询就成了：

```mysql
SELECT cust_id FROM orders WHERE order_num IN (20005,20007);
```

## 作为计算字段使用子查询

```mysql
SELECT cust_name, cust_state, 
(SELECT COUNT(*) FROM orders WHERE order.cust_id = customers.cust_id) AS orders
FROM customers
ORDER BY cust_name;
```



# 15.联结表

**外键**        外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。



## 创建联结

```mysql
SELECT vend_name, prod_name, prod_price
FROM vendors, products
WHERE vendors.vend_id = products.vend_id
ORDER BY vend_name, prod_name
```

这两个表用WHERE子句正确联结，WHERE子句指示MySQL匹配vendors表中的vend_id和products表中的vend_id。**这里需要这种完全限定列名。**

**笛卡尔积**：        由没有联结条件的表关系返回的结果为笛卡尔积。检索的行的数目将是第一个表中的行数乘上第二个表中的行数。

## 内部联结

上面所用的联结称为**等值联结**，它是基于两个表之间的相等的测试，这种联结也称为内部联结。

```mysql
SELECT vend_name, prod_name, prod_price
FROM vendors INNER JOIN products
ON vendors.vend_id = prodcuts.vend_id;
```

在使用这种语法时，联结条件用特定的ON子句而不是WHERE子句给出。传递给ON的实际条件与传递给WHERE的相同。

## 联结多个表

```mysql
SELECT prod_name, vend_name, prod_price, quantity
FROM orderitems, products, vendors
WHERE products.vend_id = vendors.vend_id
AND orderitems.prod_id = products.prod_id
AND order_num = 20005;
```



# 16.创建高级联结

## 使用表别名

```mysql
SELECT cust_name, cust_contact
FROM customers AS c, orders AS o, orderitems AS oi
WHERE c.cust_id = o.cust_id 
AND oi.order_num = o.order_num
AND prod_id = 'TNT2';
```

在此例中，表别名只用于WHERE子句中，但是，表别名不仅可以用于**WHERE子句**中，还可以用于**SELECT的列表**，**ORDER BY子句**以及**语句的其他部分**。

## 自联结

例子：此查询要求首先找到生产ID为DTNTR的物品的供应商，然后找出这个供应商生产的其他物品。

```mysql
SELECT p1.prod_id, p2.prod_id
FROM products AS p1, products AS p2
WHERE p1.vend_id = p2.vend_id
AND p2.prod_id = 'DTNTR';
```

**用自联结而不用子查询**   自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。

## 自然联结

迄今为止所建立的每个内部联结都是自然联结。



## 外部联结

```mysql
SELECT customers.cust_id, orders.order_num
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id;
```

与内部联结关联两个表中的行不同的是，外部联结还包括没有关联的行（NULL）



## 带聚集函数的联结

内部联结：

```mysql
SELECT customers.cust_name, customers.cust_id,
COUNT(orders.order_num) AS num_ord
FROM customers INNER JOIN orders
ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;
```

外部联结：

```mysql
SELECT customers.cust_name, customers.cust_id,
COUNT(orders.order_num) AS num_ord
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;
```

外部联结相较于内部联结会多一行没有订单数的情况。

|  cust_name  | cust_id | num_ord |
| :---------: | :-----: | :-----: |
| Mouse House |  10002  |    0    |



# 17.组合查询

用两种基本情况，其中需要使用查询：

- 在单个查询中从不同的表返回类似的结构的数据

- 对单个表执行多个查询，按单个查询返回数据



## 使用UNION

一个十分简单的例子：

```mysql
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5
UNION
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id In (1001,1002);
```



## UNION规则

- UINION必须由两条或两条以上的SELECT语句组成，语句之间用关键词UNION分隔

- UNION中的每个查询必须包含相同的列、表达式、聚集函数
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换地类型
- **UNION从查询中自动去除了重复的行**（换句话说，它的行为与单条SELECT语句中使用了多个WHERE子句条件一样）

## 包含或取消重复的行

使用**UNOIN ALL**

```mysql
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5
UNION ALL
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id In (1001,1002);
```

## 对组合查询结果排序

在用UNION组合查询时，**只能使用一条ORDER BY子句，它必须出现在最后一条SELECT语句之后。**



# 18.全文本搜索

## 进行全文本搜索

在索引之后，使用两个函数Match( )和Against( )执行全文本搜索，其中Match( )指定被搜索的列，Against( )指定要使用的搜索表达式

```mysql
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit');
```

除非使用BINARY方式，否则全文本搜索不区分大小写。

等级由MySQL根据行中词的数目、唯一词的数目、整个索引中词的总数一i包含该词的行的数目计算出来。

## 使用查询扩展

查询拓展用来设法放宽所返回的全文本搜索结果的范围。

```mysql
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION);
```

表中的行越多，使用查询扩展返回的结果就越好。

## 布尔文本搜索

布尔方式不同于之前使用的全文本搜索语法的地方在于，即使没有定义FULLTEXT索引，也可以使用它。

```mysql
SELECT note_text FROM productnotes
WHERE Match(note_text) Against('heavy -rope*' IN BOOLEAN MODE);
```

**全文本布尔操作符**

| 布尔操作符 |         说明         |
| :--------: | :------------------: |
|     +      |   包含，词必须出现   |
|     -      |  排除，词必须不出现  |
|     >      | 包含，而且增加等级制 |
|     <      |  包含，且减少等级制  |
|    ( )     |   把词组成子表达式   |
|     ~      |  取消一个词的排序值  |
|     *      |     词尾的通配符     |
|    " "     |     定义一个短语     |



# 19.插入数据

## 插入完整的行

```mysql
SELECT INTO customers
VALUES(NULL,
      'Pep E. LaPew',
      '100 Main Street',
      'Los Angeles',
      'CA',
      '90046',
      'USA',
      NULL,
      NULL);
```

这种语法简单，但是并不安全，上面的语句高度依赖于表中列的定义次序，并且还依赖于其次序容易获得的信息。

```mysql
SELECT INTO customers(cust_name,
                     cust_address,
                     cust_city,
                     cust_state,
                     cust_zip,
                     cust_country,
                     cust_contact,
                     cust_email)
VALUES('Pep E. LaPew',
      '100 Main Street',
      'Los Angeles',
      'CA',
      '90046',
      'USA',
      NULL,
      NULL);
```

上面语句中，在表名后面的括号里明确地给出了列名，因为给定了列名，VALUES必须以其指定的次序匹配指定的列名，不一定按照各个列出现在实际表中的次序。

如果是插入多个行：

```mysql
SELECT INTO customers(cust_name,
                     cust_address,
                     cust_city,
                     cust_state,
                     cust_zip,
                     cust_country)
VALUES('Pep E. LaPew',
      '100 Main Street',
      'Los Angeles',
      'CA',
      '90046',
      'USA'),
      ('M. Martian',
      '42 Galaxy Way',
      'New York',
      'NY',
      '11213',
      'USA');
```

**其中单条INSERT语句中有多组值，每组值用一对圆括号括起来，用逗号隔开。**

## 省略列

如果表的定义允许，则可以在INSERT操作中省略某些列。省略的列必须满足以下某个条件

- 该列定义为允许NULL值（无值或空值）
- 在表的定义中给出默认值

## 插入检索出来的数据

```mysql
INSERT INTO customers(cust_id,
                     cust_contact,
                     cust_email,
                     cust_name,
                     cust_address,
                     cust_city,
                     cust_state,
                     cust_zip,
                     cust_country)
SELECT cust_id,
	cust_contact,
	cust_email,
	cust_name,
    cust_address,
    cust_city,
    cust_state,
    cust_zip,
    cust_country
FROM custnew;
```

SELECT中列出的每个列对应于customers表名后所跟的列表中的每个列。

# 20. 更新和删除数据

## 更新数据

为了更新（修改）表中的数据，可使用UPDATE语句，可以用两种方式使用UPDATE

- 更新表中特定行
- 更新表中所有行

基本的UPDATE语句由3部分组成，分别是：

1. 要更新的表
2. 列名和它们的新值
3. 确定要更新行的过滤条件

> 不要省略WHERE子句

```mysql
UPDATE customers
SET cust_email = 'elmer@fudd.com',
	cust_name = 'The Fudds'
WHERE cust_id = 10005;
```

**为了删除某个列的值，可设置为NULL（假如表定义允许NULL值）**

```mysql
UPDATE customers
SET cust_email = NULL
WHERE cust_id = 10005;
```



## 删除数据

**DELETE删除整行而不是删除列**

删除一行：

```mysql
DELETE FROM customers
WHERE cust_id = 10006;
```

> **删除表的内容而不是表** DELETE语句从表中删除行，甚至是删除表中所有行，但是，DELETE不删除表本身。



## 更新和删除的指导原则

如果执行UPDATE而不带WHERE子句，则表中每个行都将用新值更新。如果执行DELETE语句而不带WHERE子句，表的所有数据都将被删除。



# 21.创建和操纵表

## 创建表

### 表的创建

```mysql
CREATE TABLE customers
(
	cust_id       int       NOT NULL  AUTO_INCREMENT,
    cust_name     char(50)  NOT NULL ,
    cust_address  char(50)  NULL ,
    cust_city     char(50)  NULL ,
    cust_state    char(5)   NULL ,
    cust_zip      char(10)  NULL ,
    cust_country  char(50)  NULL ,
    cust_contact  char(50)  NULL ,
    cust_email    char(255) NULL ,
    PRIMARY KEY(cust_id)
)ENGINE = InnoDB;
```

表的主键可以在创建表时用PRIMARY KEY关键词指定。

>  在创建新表时，指定的表名必须不存在，否则将出错。如果要防止意外覆盖现有的表，要求首先删除该表，然后再重建。如果仅想在一个表不存在时创建它，应该在表名后给出 IF NOT EXISTS。

### 主键再介绍

主键值必须唯一，如果使用多个列，则这些列的组合值必须唯一。

```mysql
CREATE TABLE orderitems
(
    order_num    int          NOT NULL,
    order_item   int          NOT NULL,
    prod_id      char(10)     NOT NULL,
    quantity     int          NOT NULL,
    item_price   decimal(8,2) NOT NULL,
    PRIMARY KEY (order_num, order_item)
)ENGINE=InnoDB;
```

**主键中只能使用不允许NULL值的列，允许NULL值得列不能做唯一标识符**

### 使用AUTO_INCREMENT

AUTO_INCREMENT告诉MySQL，本列每当增加一行时自动增量。每个表只允许一个AUTO_INCREMENT列，而且它必须被索引。

> **确定AUTO_INCREMENT的值** 通过自动增量生成主键的一个缺点时你不知道这些值都是谁。可以使用last_insert_id()这个函数获得这个值。
>
> ```mysql
> SELECT last_insert_id();
> ```
>
> 这个语句返回最后一个AUTO_INCREMENT值

### 指定默认值

```mysql
	quantity     int     NOT NULL  DEFAULT 1,
```

### 引擎类型

- InnoDB是一个可靠的事务处理引擎，它不支持全文本搜索
- MEMORY在功能等同于MyISAM，但由于数据存储在内存（不是磁盘）中，速度很快（特别适合临时表）
- MyISAM是一个性能极高的引擎，它支持全文本搜索但不支持事务处理。

## 更新表

为更新表定义，可使用ALTER TABLE语句

### 给表添加一个列

```mysql
ALTER TABLE vendors
ADD vend_phone CHAR(20);
```

### 给表删除一个列

```mysql
ALTER TABLE vendors
DROP COLUMN vend_phone;
```

**ALTER TABLE的一个常见用途就是定义外键**： 略~

## 删除表

```mysql
DROP TABLE customers2;
```

## 重命名表

```mysql
RENAME TABLE customers2 TO customers,
			 backup_vendors TO vendor,
			 backup_products TO products;
```



# 22.视图

**视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。**

理解视图的最好方法就是看一个例子，第15章中用下面的SELECT语句（联结表的方法）从3个表中检索数据：

```mysql
SELECT cust_name, cust_contact
FROM customers, orders, orderitems
WHERE customers.cust_id = orders.cust_id
AND orderitems.order_num = orders.order_num
AND prod_id = 'TNT2';
```

假如可以把整个查询**包装成一个名为productcustomers的虚拟表**

```mysql
SELECT cust_name, cust_contact
FROM productcustomers
WHERE prod_id = 'TNT2';
```

## 使用视图

- **视图用** `CREATE VIEW` **语句来创建**
- **使用** `SHOW CREATE VIEW viewname；` **来查看创建视图**
- **用** `DROP` **删除视图，其语法为 **`DROP VIEW viewname；`
- **更新视图时，可以先用 **`DROP` **再用 **`CREATE`， **也可以直接用** `CREATE OR REPLACE VIEW `

## 创建视图

```mysql
CREATE VIEW productcustomers AS
SELECT cust_name, cust_contact
FROM customers, orders, orderitems
WHERE customers.cust_id = orders.cust_id
AND orderitems.order_num = orders.order_num;
```

> 为什么不直接在创建视图时直接用WHERE子句呢？—————— **这样是为了创建可重用的视图： 创建不受特定数据限制的视图是一种好方法。拓展视图的范围不仅使得它能被重用，而且甚至更有用。这样做不需要创建和维护多个类似视图**

## 重新格式化检索出的数据

**视图的另一常见用途就是重新格式化检索出的数据**

```mysql
CREATE VIEW vendorlocations AS
SELECT Concat(RTrim(vend_name),' (',RTrim(vend_country),')')
AS vend_title
FROM vendors
ORDER BY vend_name;
```

假如经常需要用这个格式的结果，不必再每次需要时执行联结，创建一个视图则可以每次需要时使用视图即可。

```mysql
SELECT * FROM vendorlocations;
```

## 小结

视图为虚拟的表。它们包含的不是数据而是根据需要检索数据的查询。视图提供了一种MySQL语句层次的**封装**，可以用来简化数据处理以及重新格式化基础数据或保护基础数据。



# 23.存储过程

## 创建存储过程

如果存在相同名的存储过程，则不能再次创建它，即需要删除旧的再创建。

```mysql
CREATE PROCEDURE productpricing()
BEGIN
	SELECT Avg(prod_price) AS priceaverage
	FROM products;
END;
```

> 如果存储过程需要接受参数，它们将在() 中列举出来。此存储过程没有参数，但后面的()仍然需要。`BEGIN` 和 `END` 语句用来限定存储过程体。

**更改命令行实用程序的语句分割符** (除了\\符号，任何字符都可以用作语句分隔符)

```mysql
DELIMITER //
......
DELIMITER ;
```

如何是同这个储存过程？

```mysql
CALL productpricing();
```

**因为存储过程实际上是一个函数，所以存储过程名后需要有()符号**

## 删除存储过程

```mysql
DROP PROCEDURE productpricing;
```

> **仅当存在时删除** 如果指定过程不存在，则DROP PROCEDURE 将产生一个错误。当过程存在想删除时（或者说如果过程不存在也不产生错误） 可以使用 `DROP PROCEDURE IF EXISTS`

## 使用参数

**变量（variable）** 内存中一个特定的位置，用来临时存储数据， 所有的变量必须以@开始

```mysql
CREATE PROCEDURE productpricing(
    OUT pl DECIMAL(8,2),
    OUT ph DECIMAL(8,2),
    OUT pa DECIMAL(8,2)
)
BEGIN
	SELECT Min(prod_price) INTO pl FROM products;
	SELECT Max(prod_price) INTO ph FROM products;
	SELECT Avg(prod_price) INTO pa FROM products;
END;
```

> 分析： 每个参数必须具有指定的类型，这里使用十进制型。
>
> 关键字OUT指出相应的参数用来从存储过程传出一个值（返回给调用者）

MySQL支持IN（传递给存储过程）、OUT（从存储过程传出）、INOUT（对存储过程传入和传出）类型的参数。

下面是另一个例子，这次使用 **IN** 和 **OUT** 参数

```mysql
CREATE PROCEDURE orderototal(
	IN onumber INT,
    OUT ototal DECIMAL(8,2)
)
BEGIN
	SELECT Sum(item_price*quantity) FROM orderitems
	WHERE order_num = onumber INTO ototal;
END;
```



## 执行存储过程

```mysql
CALL productpricing(@pricelow,
                   @pricehigh,
                   @priceaverage);
```

为了获得3个值，可以

```mysql
SELECT @pricelow,@pricehigh,@priceaverage;
```



```mysql
CALL ordertotal(20005,@total);
SELECT ototal;
```

## 智能存储过程（高级）

```mysql
CREATE PROCEDURE ordertotal(
	IN onumber INT,
    IN taxable BOOLEAN,
    OUT ototal DECIMAL(8,2)
)
BEGIN
	DECLARE total DECIMAL(8,2);
	DECLARE taxrate INT DEFAULT 6;
	
	SELECT Sum(item_price*quantity) FROM orderitems
	WHERE order_num = onumber INTO total;
	
	IF taxable THEN
		SELECT total+(total/100*taxrate) INTO total;
	END IF;
	
	SELECT total INTO ototal;
END;	
```

`DECLARE` **要求指定变量名和数据类型，它也支持可选的默认值。**

```mysql
CALL ordertotal(20005,0,@total);
SELECT @total;
CALL ordertotal(20005,1,@total);
SELECT @total;
```

## 检查存储过程

```mysql
SHOW CREATE PROCEDURE ordertotal;
```

> **限制过程状态结果**     `SHOW PROCEDURE STATUS` 列出所有存储过程。为限制其输出，可以使用 LIKE 指定一个过滤模式，例如：`SHOW PROCEDURE STATUS LIKE 'ordertotal';`



# 24. 游标

使用简单的 SELECT 语句，没有办法得到第一行、下一行或前10行，也不存在每次一行地处理所有行的简单方法（相对于成批地处理它们）。

游标（cursor）是一个存储在MySQL服务器上的数据库查询，它不是一条SELECT语句，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据。

> **MySQL的游标只能用于存储过程（和函数）。**

## 创建游标

```mysql
CREATE PROCEDURE processorders()
BEGIN
	DECLARE ordernumbers CURSOR FOR
	SELECT order_num FROM orders;
END;
```

## 打开和关闭游标

```mysql
CREATE PROCEDURE processorders()
BEGIN
	DECLARE ordernumbers CURSOR FOR
	SELECT order_num FROM orders;
	-- 打开游标
	OPEN ordernumbers;
	-- 关闭游标
	CLOSE ordernumbers;
END;
```

## 使用游标数据

在一个游标被打开后，可以使用 `FETCH` 语句分别访问它的每一行。

```mysql
CREATE PROCEDURE processorders()
BEGIN
	DECLARE o INT;
	DECLARE ordernumbers CURSOR FOR
	SELECT order_num FROM orders;
	
	OPEN ordernumbers;
	FETCH ordernumbers INTO o;
	CLOSE ordernumebrs;
END;
```

分析：其中 `FETCH` 用来检索**当前行**的**order_num**列到一个名为o的局部声明的变量中。对检索出来的数据不做任何处理。

在下一个例子中，循环检索数据，从第一行到最后一行：

```mysql
CREATE PROCEDURE processorders()
BEGIN
	DECLARE done BOOLEAN DEFAULT 0;
	DECLARE o INT;
	
	DECLARE ordernumbers CURSOR FOR
	SELECT order_num FROM orders;
	
	DECLARE CONTINUE HANDLER FOR SQLSTATE'02000' SET done = 1;
	
	OPEN ordernumbers;
	REPEAT
		FETCH ordernumbers INTO o;
	UNTIL done END REPEAT;
	CLOSE ordernumebrs;
END;
```

与前一个例子不一样的是，这个例子的 `FETCH` 是在 `REPEAT` 内，因此它反复执行知道 done 为真（由 `UNTIL done END REPEAT;` 规定）

```mysql
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;
```

这条语句定义了一个 `CONTINUE HANDLER`， 它是在条件出现时被执行的代码。 

**`SQLSTATE'02000'` 是一个未找到条件，当 `REPEAT` 由于没有更多的行供循环而不能继续时，出现这个条件。**

> `DECLARE`语句的次序：     **用 `DECLARE` 语句定义的局部变量必须在定义任意游标或句柄之前定义，而句柄必须在游标之后定义。**



进一步修改的版本：

```mysql
CREATE PROCEDURE processorders()
BEGIN
	DECLARE done BOOLEAN DEFAULT 0;
	DECLARE o INT;
	DECLARE t DECIMAL(8,2);
	
	DECLARE ordernumbers CURSOR FOR
	SELECT order_num FROM orders;
	
	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;
	-- 创建表
	CREATE TABLE IF NOT EXISTS ordertotals
	(
        order_num INT, 
     	total DECIMAL(8,2)
    );
	
	OPEN ordernumbers;
	
	REPEAT
		FETCH ordernumbers INTO o;
		-- 执行前面的ordertotal这个存储过程
		CALL ordertotal(o,1,t);
		INSERT INTO ordertotals(order_num,total)
		VALUES(o,t);
	UNTIL done END REPEAT;
	CLOSE ordernumbers;
END;
```



# 25.触发器

触发器是MySQL响应以下任意语句而自动执行的一条MySQL语句（或位于BEGIN和END语句之间的一组语句）：

- `DELETE;`
- `INSERT;`
- `UPDATE;`

## 创建触发器

在创建触发器时，需要给出4条信息

- 唯一的触发器名
- 触发器关联的表
- 触发器应该影响的活动（DELETE、INSERT、UPDATE）
- 触发器何时执行

```mysql
CREATE TRIGGER newproduct AFTER INSERT ON products
FOR EACH ROW SELECT 'Product added' INTO @asd;
```

在这个例子中，文本Product added将对每个插入的行显示一次。调用 `SELECT @asd` 即可

## 删除触发器

```mysql
DROP TRIGGER newproduct;
```

**触发器不能更新或覆盖。为了修改一个触发器，必须先删除它，然后再重新创建。**



## INSERT触发器

INSERT触发器在INSERT语句执行之前或之后执行。

- 在INSERT触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行
- 在BEFORE INSERT触发器中，NEW中的值也可以被更新(允许更改被插入的值)
- 对于AUTO_INCREMENT列，NEW在INSERT执行之前包含0，在INSERT执行之后包含新的自动生成值

```mysql
CREATE TRIGGER neworder AFTER INSERT ON orders
FOR EACH ROW SELECT NEW.order_num INTO @asd;
```

> 分析：创建一个名为neworder的触发器，按照 `AFTER INSERT ON orders` 执行。在插入一个新订单到orders表中，MySQL生成一个新订单号并保存到order_num中。触发器从NEW.order_num取得这个值并返回它。此触发器必须按照`AFTER INSERT` 执行，因为在 `BEFORE INSERT` 语句执行之前，新order_num还没有生成。对于orders的每次插入使用这个触发器将总是返回新的订单号。

测试这个触发器：

```mysql
INSERT INTO orders(order_date, cust_id)
VALUES(Now(),10001);
SELECT @asd;
```

## DELETE触发器

DELETE触发器在DELETE语句执行之前或之后执行。

- 在DELETE触发器代码内，你可以引用一个名为OLD的虚拟表，访问被删除的行
- OLD中的值全都是只读的，不能更新

下面的例子演示使用OLD**保存将要被删除的行到一个存档表中**：

```mysql
CREATE TRIGGER deleteorder BEFORE DELETE ON orders 
FOR EACH ROW
BEGIN
	INSERT INTO archive_orders(order_num, order_date, cust_id)
	VALUES(OLD.order_num, OLD.order_date, OLD.cust_id);
END;
```

> 多语句触发器：   正如所见，触发器deleteorder使用 `BEGIN` 和 `END` 语句标记**触发器体**。  使用 `BEGIN END` 块的好处就是触发器能容纳多条SQL语句

## UPDATE触发器

UPDATE触发器在 ` UPDATE` 语句执行之前或之后执行。

- 在UPDATE触发器代码中，你可以引用一个名为OLD的虚拟表访问以前的值，引用一个名为NEW的虚拟表访问新更新的值
- 在 `BEFORE UPDATE` 触发器中，NEW中的值可能也被更新
- OLD中的值全都是只读，不能更新

下面的例子保证输入的名字总是大写：

```mysql
CREATE TRIGGER updatevendor BEFORE UPDATE ON vendors
FOR EACH ROW SET NEW.vend_state = Upper(NEW.vend_state);
```

> **任何数据净化都需要在UPDATE语句之前进行**

这里可以看出触发器可以保证**数据的一致性**（大小写，格式等）



# 26.事务处理

 事务处理是一种机制，用来管理必须成批执行的**MySQL**操作，以保证数据库不包含不完整的操作结果。利用事务处理，可以保证一组操作不会中途停止，它们或者作为整体执行，或者完全不执行（除非明确指示）

- **事务（transaction）**      指一组**SQL**语句
- **回退（rollback）**       指撤销指定的**SQL**语句的过程
- **提交（commit）**    指将未存储的**SQL**语句结果写入数据库表
- **保留点（savepoint）**   指事务处理中设置的临时占位符，你可以对它发布回退（与回退整个事务处理不同）

## 控制事务处理

SQL使用下面的语句来标识事务的开始

```mysql
START TRANSACTION;
```

**MySQL**的 **ROLLBACK** 命令用来回退（撤销）**MySQL**语句

```mysql
SELECT * FROM ordertotals;
START TRANSACTION;
DELETE FROM ordertotals;
SELECT * FROM ordertotals;
ROLLBACK;
SELECT * FROM ordertotals;
```

**ROLLBACK** 只能在一个事务处理内使用



一般的**MySQL**语句都是直接针对数据库执行和编写的。这就是所谓的隐含提交（**implicit commit**），即提交操作是自动进行的。但是在事务处理块中，提交不会隐含地进行。为进行明确的提交，使用**COMMIT**语句。

```mysql
START TRANSACTION;
DELETE FROM orderitems WHERE order_num = 20010;
DELETE FROM orders WHERE order_num = 20010;
COMMIT;
```

> **隐含事务关闭**             当 **COMMIT** 或 **ROLLBACK** 语句执行后，事务会自动关闭，将来的更改会**隐含提交**。



## 保留点

为了支持回退部分事务处理，必须能在事务处理块中合适的位置防止占位符。这样，如果需要回退，可以回退到某个占位符。这些占位符称为保留点。

```mysql
SAVEPOINT delete1;
ROLLBACK TO delete1;
```

> **释放保留点**      保留点在事务处理完成（执行一条 **ROLLBACK** 或 **COMMIT** ）后自动释放。也可以用 **`RELEASE SAVEPOINT`** 明确地释放保留点。



# 27.全球化和本地化

- **字符集**为字母和符号的集合
- **编码**为某个字符集成员的内部表示
- **校对**为规定字符如何比较的指定



为了给表指定字符集和校对，可使用带子句的 **`CREATE TABLE`**

```mysql
CREATE TABLE mytable
(
	column1 INT,
    column2 VARCHAR(10)
)DEFAULT CHARACTER SET hebrew
 COLLATE hebrew_general_ci;
```

除了能指定字符集和校对的表范围外，**MySQL**还允许对每个列设置它们

```mysql
CREATE TABLE mytable
(
	column1 INT,
    column2 VARCHAR(10),
    column3 VARCHAR(10) CHARACTER SET latin1 
    					COLLATE latin1_general_ci
)DEFAULT CHARACTER SET hebrew
 COLLATE hebrew_general_ci;
```

如果你需要用与创建表时不同的校对顺序排序特定的**SELECT**语句，可以在**SELECT**语句中进行

```mysql
SELECT * FROM customers
ORDER BY lastname, firstname COLLATE latin1_genneral_cs;
```



# 28. 安全管理

## 访问控制

MySQL服务器的安全基础是： 用户该对他们需要的数据具有适当的访问权，既不能多也不能少。你需要给用户提供他们所需的访问权，且仅提供他们所需的访问权。这就是所谓的访问控制，**管理访问控制需要创建和管理用户账号**。



## 创建用户账号

CREATE USER创建一个新用户账号。在创建用户账号时不一定需要口令，不过这个例子给出了口令。

```mysql
CREATE USER ben IDENTIFIED BY 'p@$$w0rd';
```

重新命名

```mysql
RENAME USER ben to bforta;
```



## 删除用户账号

```mysql
DROP USER bforta;
```



## 设置访问权限

为了看到用户账号已经赋予的权限，使用 **SHOW GRANTS FOR** 语句

```mysql
SHOW GRANTS FOR bforta;
```

为了设置权限，使用**GRANT**语句。要求给出如下信息

- 要授予的权限
- 被赋予访问权限的数据库或表
- 用户名

```mysql
GRANT SELECT ON crashcourse.* TO bforta;
```

> 此语句允许用户在**crashcourse**数据库上的所有表上使用SELECT。通过只授予**SELECT**访问权限，用户**bforta**对**crashcourse**数据库中的所有数据具有只读访问权限。

**`GRANT` 的反操作为 `REVOKE` ，用它来撤销特定的权限**

```mysql
REVOKE SELECT ON crashcourse.* FROM bforta;
```

被撤销的访问权限必须存在，否则会出错。

**GRANT**和**REVOKE**可在几个层次上控制访问权限

- 整个服务器，使用 **GRANT ALL** 和 **REVOKE ALL** 
- 整个数据库，使用 **ON database.***
- 特定的表，使用 **ON databases.table**
- 特定的列
- 特定的存储过程

权限表： 略~~~~

> **简化多次授权**    可通过列出各权限并用逗号分隔，将多条**GRANT**语句串在一起
>
> ```mysql
> GRANT SELECT, INSERT ON crashcourse.* TO bforta;
> ```



## 更改口令

为了更改用户口令，可使用 **SET PASSWORD** 语句

```mysql
SET PASSWORD FOR bforta = Password('n3w P@$$w0rd');
```

新口令必须传递到 **Password( )** 函数进行加密
