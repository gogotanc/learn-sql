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
