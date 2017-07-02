[TOC]

# SQL 必知必会

最近在看这本书，这个仓库用来存放笔记。这里只是重点关注 MYSQL 数据库，其他数据库先不管。

## 第一课 了解 SQL

一些术语

数据库（database）

保存有组织的数据的容器（通常是一个文件或一组文件）。

数据库管理系统（DBMS）

常用的数据库软件。

表（table）

某种特定类型数据的结构化清单。

模式（schema）

关于数据库和表的布局及特性的信息。

列（column）

表中的一个字段。所有表都是由一个或者多个列组成的。

数据类型（datatype）

所允许的数据的类型。每个表列都有相应的数据类型，它限制（或者允许）该列中存储的数据。

行（row）

表中的一个记录。

主键（primary key）

一列（或者一组列），其值能够唯一标识表中的每一行。

SQL (Structured Query Language)

本书讲述的是 ANSI SQL。我对于 MYSQL 的特定 SQL 会重点关注。

## 第二课 检索数据

SELECT 语句

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

子句（clause）

SQL 语句由子句构成，有些子句是必需的，有些则是可选的。一个子句通常由一个关键字加上所提供的数据组成。

ORDER BY 子句

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

WHERE 子句

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

操作符（operator）

用来联结或者改变 WHERE 子句中的子句的关键字，也称为逻辑操作符（logical operator）。

AND 操作符

用在 WHERE 子句中的关键字，用来指示检索满足所有给定条件的行。

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' AND prod_price <= 4;
```

OR 操作符

WHERE 子句中使用的关键字，用来标识检索匹配任一给定条件的行。

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' OR prod_price <= 4;
```

求值顺序

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' OR vend_id = 'DLL01'
      AND prod_price <= 4
-- SQL 在处理 OR 操作符前，优先处理 AND 操作符。
-- 对于这种操作，最好在 WHERE 子句中使用圆括号来消除歧义。
```

IN 操作符

WHERE 子句中用来指定要匹配值的清单的关键字，功能与 OR 相当。

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id IN ('DLL01', 'BRS01')
ORDER BY prod_name;
-- IN 的最大优点是可以包含其他 SELECT 语句。
```

NOT 操作符

WHERE 子句中用来否定其后条件的关键字。

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE NOT vend_id = 'DLL01';
```

## 第六课 使用通配符进行过滤

通配符（wildcard）

用来匹配值的一部分的特殊字符。

搜索模式（search pattern）

由字面值、通配符或者两者组合构成的搜索条件。

谓词（pardicate）

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

字段（field）

基本上与列（column）的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常与计算字段一起使用。

拼接（concatenate）

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

-- 
```

