# Sql语句

## 库

```sql
CREATE DATABASE name；
USE name；
DROP DATABASE [IF EXISTS] <database_name>;除数据库,并检查是否存在
```

## 表

```sql
CREATE TABLE study(
 a1 INT PRIMARY KEY,  主键，唯一。不能重复
 a2 VARCHAR(50)  50字节
 a3 DELCIMAL(10,2) 2位小数
 OREIGN KEY (a1) REFERENCES study2(a1) //绑定外键
);

INSERT INTO study(a1,a2)  插入
VALUES(1,'尝试');

SELECT * FROM study //全部查看
SELECT a2 FROM study //查看a2
SELECT * FROM study//遍历查找
WHERE a2 = '尝试';

UPDATE a2
SET a1 = 1
WHERE a2 = 2;

DELETE FROM study//删除列
WHERE a1 = 2;

DROP TABLE [IF EXISTS] table_name; 删除表
```

### 索引

方便查询的

比如原理b+树索引，就是把这行建成树的结构，用b+树搜索（挖个坑，以后来补），一般来树的更新比较麻烦，所以不适合频繁更新的数据，还有树占空间，少开点

还有什么哈希索引等，常用的是b+;creacreate

```sql
CREATE INDEX index_name
ON table_name (column1 [ASC|DESC], column2 [ASC|DESC], ...);
ASC和DESC（可选）: //用于指定索引的排序顺序。默认情况下，索引以升序（ASC）排序
column//填入列名，可以一个或多个

//也可在创建表时INDEX index_name (column1 [ASC|DESC], column2 [ASC|DESC], ...)

//创建唯一索引，不允许重复数值，快一点
CREATE UNIQUE INDEX index_name
ON table_name (column1 [ASC|DESC], column2 [ASC|DESC], ...);

DROP INDEX index_name ON table_name;//删除
SHOW INDEX FROM table_name\G//显示索引
```

## 链接

方便查看

```sql
各连接结果对比
JOIN 类型	结果
INNER JOIN	返回两个表中匹配的记录。在给定的例子中，只有 CustomerID 为 1 和 2 的记录在两个表中都有匹配，所以只会返回这些记录。

LEFT JOIN	返回左表（Customers）中的所有记录，即使右表（Orders）中没有匹配的记录。对于左表中没有匹配的右表记录，结果中的右表字段将为 NULL。在例子中，CustomerID 为 3 的记录在右表中没有匹配，所以其对应的 Product 将为 NULL。

RIGHT JOIN	返回右表（Orders）中的所有记录，即使左表（Customers）中没有匹配的记录。对于右表中没有匹配的左表记录，结果中的左表字段将为 NULL。在例子中，OrderID 为 103 的记录在左表中没有匹配，所以其对应的 Name 将为 NULL。

FULL OUTER JOIN	返回两个表中的所有记录，无论它们是否匹配。如果某个表中没有匹配的记录，那么该表的字段将为 NULL。在例子中，CustomerID 为 3 和 OrderID 为 103 的记录将分别在对方的表中显示为 NULL。

CROSS JOIN	返回两个表的笛卡尔积，即左表中的每一行与右表中的每一行组合。在例子中，每个顾客都将与每个订单组合，产生多个结果。

SELF JOIN	表与其自身进行连接。这通常用于查询表中相互关联的记录，比如员工与其经理之间的关系。


SELECT Websites.id, Websites.name, access_log.count, access_log.date
FROM Websites
INNER JOIN access_log
ON Websites.id=access_log.site_id;
```

## 字查询

查询中的查询

```sql
-- 查询工资高于公司平均工资的员工
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees); -- 子查询返回一个平均值

-- 查询所有销售部（Sales）的员工
SELECT name
FROM employees
WHERE department_id IN (
    SELECT id FROM departments WHERE name = 'Sales' -- 子查询返回一个部门ID的集合
);
//any就是任意一个满足，即大于最小值，all就是满足所有，即大于最大值

-- 查询与特定员工（如张三）部门和工资都相同的其他员工
SELECT name
FROM employees
WHERE (department_id, salary) = (
    SELECT department_id, salary 
    FROM employees 
    WHERE name = '张三'
)
AND name != '张三';

-- 查询每个部门的平均工资，并找出高于公司总平均工资的部门
SELECT dept_avg.department_id, dept_avg.avg_salary
FROM (
    SELECT department_id, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department_id
) AS dept_avg -- 这是一个表子查询，生成了一个临时表 dept_avg
WHERE dept_avg.avg_salary > (SELECT AVG(salary) FROM employees);

//GROUP BY语句用于结合聚合函数，根据一个或多个列对结果集进行分组。它的核心思想是：将具有相同值的行归为一组，然后对每个组进行聚合计算，最终每组只返回一行摘要信息。
SELECT category, SUM(sale_amount) AS total_sales
FROM sales
GROUP BY category;
//计算和值，注意要是数值，不然无法计算
```



## 视图

创建一个虚拟表，通过函数搜索，隐藏关键数据

```sql
CREATE [OR REPLACE] VIEW view_name [(column1, column2, ...)]
AS
select_statement
[WITH CHECK OPTION];
CREATE VIEW view_name：这是命令的核心，表示要创建一个名为 view_name的视图。
OR REPLACE（可选）：这是一个非常有用的选项。如果同名视图已经存在，则替换它。如果不使用此选项，而视图已存在，则创建会失败。
(column1, column2, ...)（可选）：显式地定义视图的列名。如果省略，视图的列将使用 SELECT语句中的列名或别名。
AS：关键字，用于引入视图的定义。
select_statement：这是视图的灵魂，是一个完整的 SELECT查询语句。它决定了视图包含哪些数据。
WITH CHECK OPTION（可选）：主要用于可更新视图。它确保通过视图执行 INSERT或 UPDATE操作的数据，必须满足视图定义中的 WHERE条件。

CREATE VIEW high_paid_employees AS
SELECT name, salary
FROM employees
WHERE salary >= 10000;

```



```


```

