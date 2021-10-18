### 运行MySQL

连接MySQL服务器：`mysql -u root -p`

退出MySQL命令行：`exit`

连接到远程MySQL服务器：`mysql -h <ip_address or domain_name> -u root -p`

备注： 

MySQL Client的可执行程序是mysql，MySQL Server的可执行程序是mysqld。

MySQL Server默认端口号是3306

### 数据类型

| 名称         | 类型           | 说明                                                         |
| :----------- | :------------- | :----------------------------------------------------------- |
| INT          | 整型           | 4字节整数类型，范围约+/-21亿                                 |
| BIGINT       | 长整型         | 8字节整数类型，范围约+/-922亿亿                              |
| REAL         | 浮点型         | 4字节浮点数，范围约+/-1038                                   |
| DOUBLE       | 浮点型         | 8字节浮点数，范围约+/-10308                                  |
| DECIMAL(M,N) | 高精度小数     | 由用户指定精度的小数，例如，DECIMAL(20,10)表示一共20位，其中小数10位，通常用于财务计算 |
| CHAR(N)      | 定长字符串     | 存储指定长度的字符串，例如，CHAR(100)总是存储100个字符的字符串 |
| VARCHAR(N)   | 变长字符串     | 存储可变长度的字符串，例如，VARCHAR(100)可以存储0~100个字符的字符串 |
| BOOLEAN      | 布尔类型       | 存储True或者False                                            |
| DATE         | 日期类型       | 存储日期，例如，2018-06-22                                   |
| TIME         | 时间类型       | 存储时间，例如，12:20:59                                     |
| DATETIME     | 日期和时间类型 | 存储日期+时间，例如，2018-06-22 12:20:59                     |

### 管理数据库

#### 列出所有数据库

```mysql
SHOW DATABASES;
```

#### 创建数据库

```mysql
CREATE DATABASE test_database;
```

#### 删除数据库

```mysql
DROP DATABASE test_database;
```

#### 切换数据库

```mysql
USE test_database;
```

#### 查看当前数据库的所有表

```MYSQL
SHOW TABLES;
```

#### 创建表

```mysql
CREATE TABLE IF NOT EXISTS `test_table`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `title` VARCHAR(100) NOT NULL,
   `author` VARCHAR(40) NOT NULL,
   `date` DATE,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

#### 查看创建表的SQL语句

```mysql
SHOW CREATE TABLE test_table;
```

#### 删除表

```MYSQL
DROP TABLE test_table;
```

#### 查看表中所有列

```MYSQL
DESC test_table;
```

#### 新增列

```MYSQL
ALTER TABLE test_table ADD COLUMN test_column VARCHAR(10) NOT NULL;
```

#### 修改列

```mysql
ALTER TABLE test_table CHANGE COLUMN test_colume test_colume_2 VARCHAR(20) NOT NULL;
```

#### 删除列

```mysql
ALTER TABLE test_table DROP COLUMN;
```

### 关系模型

#### 主键

一般使用**BIGINT自增**或者**GUID类型**作为主键：

1. **BIGINT自增**：`BIGINT NOT NULL AUTO_INCREMENT` 类型-非空-自增

2. **GUID类型**：global/ universal unique identifier

   ```python
   import uuid
   # uuid.uuid1()：基于MAC地址，时间戳，随机数来生成唯一的uuid，可以保证全球范围内的唯一性。
   guid = uuid.uuid1()
   ```

#### 外键

##### 增加外键约束

```mysql
ALTER TABLE students
ADD CONSTRAINT fk_class_id
FOREIGN KEY (class_id)
REFERENCES classes (id);
```

1. 修改表`students`
2. 增加名为`fk_class_id`的外键约束
3. 指定了本表的列`class_id`作为外键
4. 关联到表`classes`的列`id`

##### 删除外键约束

```mysql
ALTER TABLE students
DROP FOREIGN KEY fk_class_id;
```

备注：外键关联一般为一表对应多表，多对多时需要建立中间表，一对一可用于拆分大表提高查询效率

#### 索引

索引是关系数据库中对某一列或多个列的值进行预排序的数据结构。通过使用索引，可以让数据库系统不必扫描整个表，而是直接定位到符合条件的记录，这样就大大加快了查询速度。

##### 创建索引

```mysql
ALTER TABLE students
ADD INDEX idx_score (score);
```

##### 创建唯一索引

```mysql
ALTER TABLE students
ADD UNIQUE INDEX uni_name (name);
```

##### 增加唯一性约束

```mysql
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);
```

### 查询数据

#### 基本查询

```mysql
SELECT * FROM <表名>;
```

#### 条件查询 WHERE

```mysql
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';
```

#### 投影查询 <列名> <别名>

```mysql
SELECT id, score points, name FROM students;
-- 将score在新表中重命名为points
```

#### 排序查询 ORDER BY/ ORDER BY ... DESC

```mysql
SELECT id, name, gender, score FROM students ORDER BY score;
SELECT id, name, gender, score FROM students ORDER BY score DESC;
-- 先WHERE子句，后ORDER BY子句
```

#### 分页查询 LIMIT OFFSET

```mysql
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 3;
-- LIMIT 3表示每页最多3条记录， OFFSET 3（初始项为0）表示从第4条记录开始显示
-- LIMIT 15 OFFSET 30还可以简写成LIMIT 30, 15
-- 
-- LIMIT总是设定为pageSize
-- OFFSET计算公式为pageSize * (pageIndex - 1)
```

#### 聚合查询 COUNT/ SUM/ AVG/ MAX/ MIN/ FLOOR/ CEILING

```mysql
SELECT AVG(score) average FROM students WHERE gender = 'M';
-- 向下四舍五入FLOOR(1.5) = 1， 向上四舍五入CEILING(1.5) = 2
```

#### 分组查询 GROUP BY

```mysql
SELECT class_id, COUNT(*) num FROM students GROUP BY class_id;
-- 聚合查询的列中只能放入分组的列
```

#### 多表查询 <表1>, <表2>

```mysql
SELECT * FROM students, classes;
-- 结果的列是两个表列数的和，结果的行是两个表列数之积
```

```mysql
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c
WHERE s.gender = 'M' AND c.id = 1;
-- 给表设置别名
```

#### 连接查询 INNER JOIN <连接表> ON <连接条件>

```mysql
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id;
-- INNER JOIN 返回两张表都有的记录（交集）
-- LEFT OUTER JOIN 返回左表存在的记录
-- RIGHT OUTER JOIN 返回右表存在的记录
-- FULL OUTER JOIN 返回两张表存在的记录（并集）
```

### 修改数据

CRUD: Create、Retrieve、Update、Delete

#### 插入 INSERT INTO

```mysql
-- INSERT INTO <表名> (字段1, 字段2, ...) VALUES (值1, 值2, ...);
INSERT INTO students (class_id, name, gender, score) VALUES (2, '大牛', 'M', 80);
```

#### 更新 UPDATE ... SET ...

```mysql
-- UPDATE <表名> SET 字段1=值1, 字段2=值2, ... WHERE ...;
UPDATE students SET name='大牛', score=score+10 WHERE id=1;
```

#### 删除 DELETE FROM

```mysql
-- DELETE FROM <表名> WHERE ...;
DELETE FROM students WHERE id>=5 AND id<=7;
```

### 数据库事务

#### ACID特性

- A：Atomic，原子性，将所有SQL作为原子工作单元执行，要么全部执行，要么全部不执行；
- C：Consistent，一致性，事务完成后，所有数据的状态都是一致的，即A账户只要减去了100，B账户则必定加上了100；
- I：Isolation，隔离性，如果有多个事务并发执行，每个事务作出的修改必须与其他事务隔离；
- D：Duration，持久性，即事务完成后，对数据库数据的修改被持久化存储。

```mysql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE ID = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

`BEGIN`开始事务，`COMMIT`提交事务， `ROLLBACK`回滚事务

#### 隔离级别

| Isolation Level                  | 脏读（Dirty Read） | 不可重复读（Non Repeatable Read） | 幻读（Phantom Read） |
| -------------------------------- | :----------------- | :-------------------------------- | :------------------- |
| Read Uncommitted                 | Yes                | Yes                               | Yes                  |
| Read Committed                   | -                  | Yes                               | Yes                  |
| Repeatable Read (InnoDB默认级别) | -                  | -                                 | Yes                  |
| Serializable                     | -                  | -                                 | -                    |

```mysql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

