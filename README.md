[TOC]

# SQL 必知必会

最近在看这本书，这个仓库用来存放笔记。这里只是重点关注 MYSQL 数据库，其他数据库先不管。

## 第一课 了解 SQL

一些术语：

**数据库（database）**

保存有组织的数据的容器（通常是一个文件或一组文件）。

**数据库管理系统（DBMS）**

常用的数据库软件。

**表（table）**

某种特定类型数据的结构化清单。

**模式（schema）**

关于数据库和表的布局及特性的信息。

**列（column）**

表中的一个字段。所有表都是由一个或者多个列组成的。

**数据类型（datatype）**

所允许的数据的类型。每个表列都有相应的数据类型，它限制（或者允许）该列中存储的数据。

**行（row）**

表中的一个记录。

**主键（primary key）**

一列（或者一组列），其值能够唯一标识表中的每一行。

**SQL (Structured Query Language)**

本书讲述的是 ANSI SQL。我对于 MYSQL 的特定 SQL 会重点关注。

## 第二课 检索数据

**SELECT 语句**

```sql
-- 检索单个列
SELECT prod_name
FROM Products;

-- 检索多个列
SELECT prod_name, prod_price
FROM Products;

-- 检索所有列
SELECT *
FROM Products;

-- 检索不同的值
SELECT DISTINCT vend_id
FROM Products;
-- DISTINCT 关键字作用于所有的列

-- 限制结果
-- MYSQL 中使用 LIMIT 关键字
SELECT prod_name
FROM Products
LIMIT 5;

-- 起始行和结果数
SELECT prod_name
FROM Products
LIMIT 5 OFFSET 3;

-- 简写形式
SELECT prod_name
FROM Products
LIMIT 3,5;
-- 3 为 OFFSET，5 为 LIMIT

-- 注释

/*
  也是注释
*/
```

## 第三课 排序检索数据

**子句（clause）**

SQL 语句由子句构成，有些子句是必需的，有些则是可选的。一个子句通常由一个关键字加上所提供的数据组成。

```sql
-- 单个序列
SELECT prod_name
FROM Products
ORDER BY prod_name;
-- ORDER BY 子句必须是 SELECT 语句中最后的一条子句。
-- ORDER BY 子句中使用的列可以是非选择的列。

-- 按多个列排序
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY prod_price, prod_name;

-- 按列位置排序
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY 2, 3;

-- 指定排序方向
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY prod_price DESC, prod_name DESC;
```

## 第四课 过滤数据

**WHERE 子句**

只检索所需要的数据需要指定搜索条件（search certeria），搜索条件也称为过滤条件（filter condition）。

```sql
-- 检查单个值
SELECT prod_name, prod_price
FROM Products
WHERE prod_price < 3.49;

-- 不匹配检查
SELECT prod_name, prod_price
FROM Products
WHERE prod_price <> 3.49;
-- MYSQL 中‘不等于’可以使用 <> 和 !=

-- 范围值检查
SELECT prod_name, prod_price
FROM Products
WHERE prod_price BETWEEN 5 AND 10;
-- 匹配开始值和结束值

-- 空值检查
-- NULL 无值（no value），它与字段包含 0、空字符串或者仅仅包含空格不同。
-- 使用 IS NULL 子句
SELECT prod_name, prod_price
FROM Products
WHERE prod_price IS NULL;
```

## 第五课 高级数据过滤

**操作符（operator）**

用来联结或者改变 WHERE 子句中的子句的关键字，也称为逻辑操作符（logical operator）。

```sql
-- AND 操作符
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' AND prod_price <= 4;

-- OR 操作符
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' OR prod_price <= 4;

-- 求值顺序
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' OR vend_id = 'DLL01'
      AND prod_price <= 4
-- SQL 在处理 OR 操作符前，优先处理 AND 操作符。
-- 对于这种操作，最好在 WHERE 子句中使用圆括号来消除歧义。

-- IN 操作符
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id IN ('DLL01', 'BRS01')
ORDER BY prod_name;
-- IN 的最大优点是可以包含其他 SELECT 语句。

-- NOT 操作符
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE NOT vend_id = 'DLL01';
```

## 第六课 使用通配符进行过滤

**通配符（wildcard）**

用来匹配值的一部分的特殊字符。

**搜索模式（search pattern）**

由字面值、通配符或者两者组合构成的搜索条件。

**谓词（pardicate）**

操作符何时不是操作符？答案是，它作为谓词时。从技术上来说，LIKE 是谓词而不是操作符。

```sql
-- 百分号(%)通配符
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE 'Fish%';
-- 百分号(%)通配符能够匹配 0 个、1 个或者多个字符。
-- MYSQL 默认是不区分大小写的，这里 'fish%' 可以得到同样的结果。
-- 注意 NULL，'%' 不会匹配 NULL 的行。

-- 下划线(_)通配符
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE '__ inch teddy bear';
-- 下划线(_)通配符总是刚好匹配一个字符

-- 方括号([])通配符
SELECT cust_contact
FROM Customers
WHERE cust_contact LIKE '[JM]%'
ORDER BY cust_contact;
-- [JM]匹配方括号中任意一个字符
-- MYSQL 不支持这个操作符

-- 技巧
-- 不要过度使用通配符
-- 把通配符置于开始处，搜索起来是最慢的
-- 仔细注意通配符的位置

```

## 第七课 创建计算字段

**字段（field）**

基本上与列（column）的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常与计算字段一起使用。

**拼接（concatenate）**

将值联结到一起（将一个值附加到另一个值）构成单个值。

```sql
-- 加号(+)或者两个竖杠(||)
-- MYSQL 不支持这两个操作符，需要使用特殊函数实现
SELECT vend_name + '(' + vend_country + ')'
FROM Vendors
ORDER BY vend_name;

-- MYSQL 中需要使用 concat 函数实现
SELECT concat(vend_name, '(', vend_country, ')')
FROM Vendors
ORDER BY vend_name;

-- 使用别名(alias)
SELECT concat(vend_name, '(', vend_country, ')') AS vend_title
FROM Vendors
ORDER BY vend_name;
-- 别名常见的用途包括在实际的表列名中包含不合法的字符(如空格)时重新命名它，在原来的名字含混或者容易误解时扩充它

-- 执行算术计算
SELECT prod_id, quantity, item_price, quantity * item_price AS expanded_price
FROM OrderItems
WHERE order_num = 20008;

-- 返回当前时间
SELECT now();
```

## 第八课 使用函数处理数据

**函数**

SQL 也是可以使用函数来处理数据的，不同 DBMS 都有特定的函数，很多函数是不可移植的。

**可移植性（protable）**

所编写的代码可以在多个系统上运行。

```sql
-- 文本处理函数
-- UPPER() 将字符串转换为大写
SELECT vend_name, UPPER(vend_name) AS vend_name_upcase
FROM Vendors
ORDER BY vend_name;

-- 日期和时间处理函数(移植性最差)
-- YEAR() 获取年份
SELECT order_num
FROM Orders
WHERE YEAR(order_date) = 2012;

-- 数值处理函数(最一致、最统一)
-- ABS() 绝对值
-- PI() 圆周率
SELECT PI();
```

## 第九课 汇总数据

**聚集函数（aggregate function）**

对某些行运行的函数，计算并返回一个值。

```sql
-- AVG() 函数
SELECT AVG(prod_price) AS avg_price
FROM Products;
-- 只用于单列
-- 会忽略 NULL

-- COUNT() 函数
SELECT COUNT(*) AS num_count
FROM Customers;
-- 结果为 5

SELECT COUNT(cust_email) AS num_count
FROM Customers;
-- 结果为 3
-- COUNT() 会忽略指定列的值为空的行，但是使用星号则不会忽略。

-- MAX() 函数
SELECT MAX(prod_price) AS max_price
FROM Products;
-- 非数值数据，返回该列排序后的最后一行
-- 忽略 NULL

-- MIN() 函数
SELECT MIN(prod_price) AS min_price
FROM Products;
-- 非数值数据，返回该列排序后的最前面的一行
-- 忽略 NULL

-- SUM() 函数
SELECT SUM(quantity) AS items_ordered
FROM OrderItems
WHERE order_num = 20005;
-- 忽略 NULL

-- 组合聚集函数
SELECT COUNT(*) AS num_items,
       MIN(prod_price) AS price_min,
       MAX(prod_price) AS price_max,
       AVG(prod_price) AS price_avg
FROM Products;
```

## 第十课 分组数据

**GROUP BY 子句和 HAVING 子句**

```sql
-- 创建分组
-- GROUP BY 子句
-- 分组统计数量
SELECT vend_id, COUNT(*) AS num_prods
FROM Products
GROUP BY vend_id;
-- GROUP BY 的一些规定
-- 1.GROUP BY 子句可以包含任意数目的列，因而可以对分组进行嵌套，更细致地进行数据分组
-- 2.如果在 GROUP BY 子句中嵌套了分组，数据将在最后指定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算(所以不能从个别的列取回数据)
-- 3.GROUP BY 子句中列出的每一列都必须是检索列或有效的表达式(但不能是聚集函数)。如果在 SELECT 中使用表达式，则必须在 GROUP BY 子句中指定相同的表达式。不能使用别名
-- 4.大多数 SQL 实现不允许 GROUP BY 列带有长度可变的数据类型(如文本或备注型字段)。
-- 5.除聚集计算语句外，SELECT 语句中的每一列都必须在 GROUP BY 子句中给出。
-- 6.如果分组列中包含具有 NULL 值的行，则 NULL 将作为一个分组返回。如果列中有多行 NULL 值，它们将分为一组。
-- 7.GROUP BY 子句必须出现在 WHERE 子句之后，ORDER BY 子句之前。

-- 过滤分组
-- HAVING 子句
-- WHERE 过滤行，HAVING 过滤分组
SELECT cust_id, COUNT(*) AS orders
FROM Orders
GROUP BY cust_id
HAVING COUNT(*) >= 2;

-- 另一种理解，WHERE 在分组之前过滤，HAVING 在分组之后过滤。
SELECT vend_id, COUNT(*) AS num_prods
FROM Products
WHERE prod_price >= 4
GROUP BY vend_id
HAVING COUNT(*) >= 2;

SELECT vend_id, COUNT(*) AS num_prods
FROM Products
GROUP BY vend_id
HAVING COUNT(*) >= 2;
-- WHERE 子句用于标准行级过滤

-- 分组和排序
SELECT order_num, COUNT(*) AS items
FROM OrderItems
GROUP BY order_num
HAVING COUNT(*) >= 3
ORDER BY items, order_num;
-- 不要依赖 GROUP BY 排序数据
```

**SELECT 子句顺序**

| 子句       | 说明        | 是否必须        |
| -------- | --------- | ----------- |
| SELECT   | 要返回的列或表达式 | 是           |
| FROM     | 从中检索数据的表  | 仅在从表选择数据时使用 |
| WHERE    | 行级过滤      | 否           |
| GROUP BY | 分组说明      | 仅在按组计算聚集时使用 |
| HAVING   | 组级过滤      | 否           |
| ORDER BY | 输出排序顺序    | 否           |

## 第十一课 使用子查询

**查询（query）**

任何 SQL 语句都是查询。但此术语一般指 SELECT 语句。

MYSQL 是 4.1 版本引入子查询。

```sql
-- 使用子查询进行过滤
SELECT order_num
FROM OrderItems
WHERE prod_id = 'RGAN01';

SELECT cust_id
FROM Orders
WHERE order_num IN (20007, 20008);

-- 上面两句使用子查询
SELECT cust_id
FROM Orders
WHERE order_num IN (SELECT order_num
                    FROM OrderItems
                    WHERE prod_id = 'RGAN01');
-- SELECT 语句中，子查询总是从内向外处理。在处理上面的 SELECT 语句时，DBMS 实际上是执行了两个操作。
-- 注意：只能是单列
-- 作为子查询的 SELECT 语句只能查询单个列。企图检索多个列将返回错误。

-- 作为计算字段使用子查询
SELECT cust_name, cust_state,
       (SELECT COUNT(*)
        FROM Orders
        WHERE Orders.cust_id = Customers.cust_id) AS orders
FROM Customers
ORDER BY cust_name;
-- 使用完全限定名(如 Orders.cust_id)来消除歧义。
```

