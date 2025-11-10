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

