# MySQL基础

## 一、数据库简介

### 1.1 数据存储方式对比

内存存储：

- **优点**：读写速度极快，无磁盘IO开销
- **缺点**：临时性存储，程序终止、断电后数据全部丢失，无法持久化

文件存储：

- **优点**：数据永久保存在磁盘，断电、程序重启数据不丢失
- **缺点**：
  - 频繁磁盘IO操作，读写效率极低，不适合高频数据操作
  - 数据管理繁琐，操作数据需读取整个文件，无法精准定位单条数据

数据库存储（关系型数据库）：

主流关系型数据库：**MySQL、SQL Server、Oracle**，完美弥补内存与文件存储的缺陷，是企业主流数据存储方案。

- 支持数据永久持久化存储
- 提供标准化SQL语句，数据增删改查、管理、筛选高效便捷
- 支持数据约束、关联关系，保证数据完整性与规范性

### 1.2 MySQL 的卸载

1. 系统卸载：控制面板 → 程序和功能 → 卸载 MySQL 程序
2. 清理残留：删除系统隐藏目录 `C:\ProgramData\MySQL`，避免残留配置导致重装异常

### 1.3 数据库核心存储结构

数据库以**数据表**为最小组织单位存储数据，是结构化数据的核心载体。

>可通过Java面向对象思想快速理解数据库表结构：
>
>- Java 类 ↔ 数据库表
>- 类的属性 ↔ 表的字段（列）
>- 类的实例对象 ↔ 表的记录（行）

## 二、MySQL 单表操作

### 2.1 核心字段约束与数据类型

#### 1. 主键约束 PRIMARY KEY

主键是数据表中唯一标识单条记录的字段，等价于 `UNIQUE + NOT NULL`（唯一且非空），每张表**必须且只能有一个主键**。

>主键四大特性：
>
>1. 唯一性：任意两行数据主键值不重复
>2. 非空性：主键字段不允许为 NULL，每条记录必须有主键值
>3. 不可修改：主键值一旦写入，不允许更新、修改
>4. 不可复用：删除某条数据后，其主键值不会分配给新插入的数据

#### 2. 自增约束 AUTO_INCREMENT

仅适用于 `INT` 类型字段，配合主键/唯一键使用，无需手动赋值，插入数据时自动递增（默认从1开始，步长为1）。

#### 3. CHAR 与 VARCHAR 的区别

两者均为字符类型，限制最大存储字符数，核心差异为**定长与变长存储**：

| 对比维度 | CHAR(n) 定长字符                              | VARCHAR(n) 变长字符                             |
| :------- | :-------------------------------------------- | :---------------------------------------------- |
| 存储规则 | 固定占用n个字符空间，数据不足时尾部补空格填充 | 仅存储实际输入字符长度，额外占用1-2字节记录长度 |
| 空间占用 | 浪费空间，长度固定                            | 节省空间，按需分配                              |
| 读写性能 | 速度快，定长读取无需计算长度                  | 速度略慢，需解析长度标识                        |
| 适用场景 | 固定长度数据（手机号、身份证、性别、编码）    | 可变长度数据（姓名、地址、备注）                |

示例：`CHAR(10)`、`VARCHAR(10)` 存入「张三」

- CHAR(10)：固定占用10个字符空间，尾部补8个空格
- VARCHAR(10)：「张三」占用2个字符空间，无多余填充，但**额外占用1-2字节记录长度**

### 2.2 数据库基础操作

```sql
-- 查看所有数据库
SHOW DATABASES;

-- 创建数据库（指定utf8字符集，避免中文乱码）
CREATE DATABASE study DEFAULT CHARACTER SET utf8;

-- 删除数据库（高危操作，谨慎使用）
DROP DATABASE study;

-- 切换/选中指定数据库
USE study;
```

### 2.3 数据表基础操作

```sql
-- 查看当前库所有数据表
SHOW TABLES;

-- 查看表结构（字段、类型、约束）
DESC student;

-- 基础建表语句
CREATE TABLE student(
    id INT,
    `name` CHAR(10),
    age INT,
    gender CHAR(1)
);

-- 删除数据表（高危操作）
DROP TABLE student;

-- 修改表结构
-- 1. 添加字段
ALTER TABLE student ADD COLUMN address CHAR(10);
-- 2. 删除字段
ALTER TABLE student DROP COLUMN address;
-- 3. 修改字段名+字段类型
ALTER TABLE student CHANGE address addr CHAR(20);
-- 4. 修改表名
ALTER TABLE student RENAME TO stu;
```

### 2.4 完善约束的建表语句

```sql
-- 带主键、自增、变长字段的标准学生表
CREATE TABLE student(
    id INT PRIMARY KEY AUTO_INCREMENT,  -- 主键自增，唯一标识
    `name` VARCHAR(10),                  -- 变长姓名
    age INT,
    gender CHAR(1)
);
```

### 2.5 数据增删改操作（DML）

#### 1. 插入数据 INSERT

```sql
-- 完整字段插入，对应所有字段的时候前面的字段名可以省略
INSERT INTO student(id,`name`,age,gender) VALUES(1,'wangwu',23,'男');
INSERT INTO student VALUES(1,'wangwu',23,'男');

-- 省略字段插入（必须匹配表字段顺序）
INSERT INTO student VALUES(4,'赵六22',33,'男');

-- 部分字段插入（仅插入指定字段，其余为默认值/NULL）
INSERT INTO student(`name`,age,gender) VALUES('小张11',23,'男');

-- 批量插入多条数据（高效写法，推荐）
INSERT INTO student(`name`,age,gender) 
VALUES('小张77',23,'男'),('小王',22,'男');
```

**常见报错**：`Duplicate entry '1' for key 'PRIMARY'`

原因：主键值重复，违反主键唯一性约束，需更换主键值或使用自增主键

#### 2. 修改数据 UPDATE

**注意**：修改数据必须加 `WHERE` 条件，否则会更新全表数据（高危）

```sql
-- 无条件：全表年龄+1（禁止生产使用）
UPDATE student SET age=age+1;

-- 条件更新：指定id修改多个字段
UPDATE student SET age=age+1,name='zhangsan' WHERE id=7;
```

#### 3. 删除数据 DELETE/TRUNCATE

```sql
-- 条件删除：删除指定id数据（精准删除单条）
DELETE FROM student WHERE id=12;

-- 多条件删除：删除id为1、2的数据
DELETE FROM student WHERE id IN(1,2);

-- 条件删除：删除所有年龄24的用户（批量删除）
DELETE FROM student WHERE age=24;

-- 清空全表数据（保留表结构，自增主键不重置）
DELETE FROM student;

-- 清空全表数据（保留表结构，重置自增主键为1，效率更高）（TRUNCATE截断）
TRUNCATE TABLE student;
```

**DELETE 与 TRUNCATE（截断） 核心区别**

- DELETE：逐行删除数据，支持事务回滚，自增主键不重置
- TRUNCATE（截断）：清空整张表数据，不支持事务，自增主键重置，性能更高

### 2.6 数据查询操作（DQL）

查询是数据库使用频率最高的操作，企业开发中**禁止使用 SELECT \***，需按需指定字段，提升查询性能。

#### 1. 基础查询

```sql
-- 企业不推荐：查询所有字段
SELECT * FROM student;

-- 企业推荐：按需查询指定字段
SELECT id,`name`,age,gender FROM student;

-- 单独查询指定字段
SELECT `name`,age FROM student;
```

#### 2. 别名、常量列、字段运算、去重

```sql
-- 字段起别名、添加常量列
SELECT id,`name`,age AS '年龄','java2403' AS '班级' FROM student;

-- 字段运算（可用于分数求和、数值计算）
SELECT id,`name`,(php+java) AS '总成绩' FROM student;

-- 去除重复数据 DISTINCT
SELECT DISTINCT address FROM student;
```

#### 3. 条件查询 WHERE

##### 1）基础比较运算

运算符：`>、<、>=、<=、!=、<>`（!= 与 <> 均表示不等于）

```sql
-- 查询姓名为小王的学生
SELECT * FROM student WHERE `name`='小王';

-- 查询java成绩70-80分的学生（两种写法等价）
SELECT * FROM student WHERE java>=70 AND java<=80;
SELECT * FROM student WHERE java BETWEEN 70 AND 80;

-- 查询地址不是青岛的学生
SELECT * FROM student WHERE address <> '青岛';
```

##### 2）逻辑运算 AND / OR

```sql
-- 同时满足：姓名小王 且 地址青岛
SELECT * FROM student WHERE `name`='小王' AND address='青岛';

-- 满足其一：姓名小王 或 地址北京
SELECT * FROM student WHERE `name`='小王' OR address='北京';
```

##### 3）空值判断

NULL 代表无值，与空字符串、0、空格完全不同，**不能用 = 判断空值**

```sql
-- 查询地址为空的学生
SELECT * FROM student WHERE address IS NULL;

-- 查询地址不为空的学生
SELECT * FROM student WHERE address IS NOT NULL;
```

#### 4. 聚合函数查询

常用聚合函数：`SUM()`求和、`AVG()`平均值、`MAX()`最大值、`MIN()`最小值、`COUNT()`统计行数

```sql
-- 分数求和
SELECT SUM(php) AS 'php总成绩' FROM student;
-- 分数平均值
SELECT AVG(php) AS 'php平均值' FROM student;
-- 分数最大值
SELECT MAX(php) AS 'php最大值' FROM student;

-- 统计总人数
SELECT COUNT(*) AS '总人数' FROM student;       -- 统计所有行，忽略NULL
SELECT COUNT(id) AS '总人数' FROM student;      -- 统计id非空行
SELECT COUNT(address) AS '总人数' FROM student; -- 忽略address为NULL的行
```

**COUNT 核心区别**：`COUNT(*)` 统计全表行数，`COUNT(字段)` 忽略该字段为NULL的行

#### 5. 排序查询 ORDER BY

- `ASC`：升序（默认，可省略）
- `DESC`：降序
- ORDER BY 必须放在SQL语句末尾

```sql
-- 默认升序
SELECT * FROM student ORDER BY php;
-- 手动升序
SELECT * FROM student ORDER BY php ASC;
-- 降序排序
SELECT * FROM student ORDER BY php DESC;

-- 多字段排序：php降序，分数相同则java升序
SELECT * FROM student ORDER BY php DESC, java ASC;
```

#### 6. 分组查询 GROUP BY

分组后**行级过滤用WHERE，组级过滤用HAVING**

```sql
-- 统计男女学生人数
SELECT gender AS '性别',COUNT(*) AS '人数'
FROM student GROUP BY gender;

-- 筛选人数大于1的分组结果（分组后过滤用HAVING）
SELECT gender AS '性别',COUNT(*) AS '人数'
FROM student GROUP BY gender HAVING COUNT(*)>1;
```

**分组查询核心规则**

1. GROUP BY 必须在 WHERE 之后、ORDER BY 之前
2. WHERE 过滤单条数据（分组前），HAVING 过滤分组结果（分组后）
3. 分组字段为NULL的数据，会单独归为一个分组

#### 7. 分页查询 LIMIT

LIMIT 是 MySQL 专属分页语句，用于限制查询结果返回条数，**必须放在整条SQL语句的最后执行**，优先级最低。

##### 1. 整语法格式

```sql
-- 格式1：只写一个参数 = 展示前N条数据
SELECT 字段 FROM 表名 LIMIT 条数;

-- 格式2：写两个参数 = 偏移量, 展示条数（标准分页语法）
SELECT 字段 FROM 表名 LIMIT 偏移量, 条数;
```

##### 2. 参数详细解析

- **第一个参数（偏移量 offset）**：跳过前多少条数据，默认从0开始
- **第二个参数（条数 size）**：最终查询、返回的数据行数

##### 3. 通用分页公式（开发常用）

已知：页码 page、每页条数 pageSize

**偏移量 = (页码 - 1) \* 每页条数**

```sql
-- 示例：第1页，每页3条
SELECT * FROM goods ORDER BY market_price DESC LIMIT 0,3;

-- 示例：第2页，每页3条（跳过前3条，取3条）
SELECT * FROM goods ORDER BY market_price DESC LIMIT 3,3;

-- 示例：第3页，每页3条
SELECT * FROM goods ORDER BY market_price DESC LIMIT 6,3;
```

##### 4. LIMIT 搭配各类查询实操案例

```sql
-- 1. 基础查询+分页：查询前5条学生数据
SELECT id,name,age FROM student LIMIT 5;

-- 2. 排序+分页（高频组合）：价格最高的前3件商品
SELECT * FROM goods ORDER BY market_price DESC LIMIT 0,3;

-- 3. 条件+排序+分页：点击量前5的手机商品
SELECT * FROM goods WHERE cat_id=3 ORDER BY click_count DESC LIMIT 5;

-- 4. 分组+排序+分页：统计各班级人数，取人数最多的前2个班级
SELECT banji_id,COUNT(*) AS 人数 
FROM student 
GROUP BY banji_id 
ORDER BY 人数 DESC 
LIMIT 2;
```

### 2.8 字段属性设置

- **NOT NULL**：字段非空，禁止存入NULL值
- **AUTO_INCREMENT**：int字段自增，必须配合主键/唯一键使用
- **PRIMARY KEY**：主键，唯一+非空，一张表仅一个
- **UNIQUE**：唯一约束，字段值不可重复，允许为空
- **DEFAULT**：字段默认值，未赋值时自动填充
- **COMMENT**：字段注释，提升代码可读性

```sql
-- 带完整约束的教师表
CREATE TABLE teacher(
    id INT PRIMARY KEY AUTO_INCREMENT,
   `name` CHAR(10) NOT NULL,
    age INT COMMENT '年龄',
    address CHAR(10) DEFAULT '中国', -- 默认值
    UNIQUE KEY(`name`) -- 姓名唯一约束
);

-- 唯一约束报错：Duplicate entry 'zhangsan' for key 'name'
-- 原因：姓名重复，违反唯一约束
INSERT INTO teacher(`name`) VALUES('zhangsan');
```

## 三、MySQL 多表操作

### 3.1 数据库分类与设计思想

#### 1. 关系型数据库和非关系型数据库

- **关系型数据库**：MySQL、SQL Server、Oracle，数据分表存储、通过外键关联，保证数据一致性，适合结构化数据
- **非关系型数据库**：Redis、MongoDB，键值对存储（类似Java Map），读写速度极快，适合存储日志、缓存等非结构化数据

#### 2. 多表设计核心思想

消除数据冗余，**一种数据一张表**，通过公共字段（外键）建立表关联，避免数据重复存储，提升维护效率。

#### 3. 表关系类型

- 一对一：用户-用户详情
- 一对多：班级-学生（核心常用）
- 多对多：学生-课程（需中间关联表实现）

### 3.2 多表结构创建

以下使用**班级-学生-课程**案例

#### 1. 班级表

```sql
CREATE TABLE banji(
     id INT PRIMARY KEY AUTO_INCREMENT,
    `name` VARCHAR(10) NOT NULL
);
INSERT INTO banji(`name`) VALUES('java1807'),('java1812');
```

#### 2. 学生表（含外键）

使用外键关联班级主键的写法：

```sql
FOREIGN KEY(banji_id) REFERENCES banji(id);
```

完整的创建学生表SQL语句：

```sql
CREATE TABLE student(
    id INT PRIMARY KEY AUTO_INCREMENT,
    `name` VARCHAR(10) NOT NULL,
    age INT,
    gender CHAR(1),
    banji_id INT,
    FOREIGN KEY(banji_id) REFERENCES banji(id) -- 外键关联班级表主键
);
INSERT INTO student(`name`,age,gender,banji_id)
VALUES('张三',20,'男',1),('李四',21,'男',2),('王五',20,'女',1);
```

**外键报错**：`Cannot add or update a child row`

原因：外键值在关联主表中不存在，如插入 banji_id=3（班级表无id=3的数据）

#### 3. 课程表

```sql
CREATE TABLE course(
    id INT PRIMARY KEY AUTO_INCREMENT,
    `name` VARCHAR(10) NOT NULL,
    credit INT COMMENT '学分'
);
INSERT INTO course(`name`,credit) VALUES('Java',5),('UI',4),('H5',4);
```

#### 4. 班级课程中间表（多对多关联）

```sql
CREATE TABLE banji_course(
    banji_id INT,
    course_id INT,
    PRIMARY KEY(banji_id,course_id), -- 联合主键（防止重复关联）
    FOREIGN KEY(banji_id) REFERENCES banji(id),
    FOREIGN KEY(course_id) REFERENCES course(id)
);
INSERT INTO banji_course(banji_id,course_id) VALUES(1,1),(1,3),(2,1),(2,2),(2,3);
```

### 3.3 子查询

子查询，又称**嵌套查询**，是**将一条查询结果作为另一条查询的条件**，实现嵌套查询，简化多表操作。

#### 1. 单行子查询（= 匹配）

```sql
-- 查询Java1812班所有学生
SELECT * FROM student WHERE banji_id=(SELECT id FROM banji WHERE `name`='Java1812');
```

#### 2. 多行子查询（IN 匹配）

```sql
-- 查询Java1807、Java1812班所有学生
SELECT * FROM student WHERE banji_id IN(SELECT id FROM banji WHERE `name` IN('Java1807','Java1812'));
```

#### 3. 字段子查询

```sql
-- 统计班级ID、班级名称、班级人数
SELECT 
  banji_id AS '班级ID',
  (SELECT name FROM banji WHERE banji.id=banji_id) AS '班级名',
  COUNT(*) AS '人数'
FROM student GROUP BY banji_id;
```

**子查询核心规则**

1. `=` 匹配单行单列子查询结果，`IN` 支持多行单列结果
2. 子查询只能返回单个字段，多字段会报错
3. 不建议多层嵌套，会严重影响查询性能

## 四、多表联结查询

### 4.1 笛卡尔积

无联结条件的多表查询，结果为**表行数乘积**，产生大量无效冗余数据，业务中必须避免。

叉联结（CROSS JOIN）：专门生成笛卡尔积，无业务使用场景

```sql
SELECT *
FROM student,banji;
```

### 4.2 等值连接

等值链接又叫**隐式内连接**，通过 `WHERE` 条件匹配多表关联字段，筛选有效数据，无匹配数据不展示：

```sql
-- 统计每个班级ID、班级名称、学生人数
SELECT banji.id,banji.name, COUNT(*)
FROM student,banji
WHERE student.banji_id=banji.id
GROUP BY banji.id;
```

### 4.3 内连接 INNER JOIN（推荐）

`JOIN` 是 `INNER JOIN` 缩写，只返回**左右表匹配成功**的数据，匹配失败则过滤。

```sql
-- 内连接统计班级人数
SELECT b.id,b.`name`,COUNT(*)
FROM student AS s INNER JOIN banji AS b
ON s.banji_id=b.id 
GROUP BY b.id;

-- 四表联查：学生+班级+班级课程+课程
SELECT *
FROM student s,banji b,banji_course bc,course c
WHERE s.banji_id=b.id AND b.id=bc.banji_id AND bc.course_id=c.id;
```

### 4.4 左外连接 LEFT JOIN（高频常用）

左外连接简称**左连接**，核心规则：**以左表为基准，保留左表所有数据，匹配右表关联数据，无匹配项则右表字段填充NULL**。

适用场景：需要查询主表全部数据，关联附表可选数据（如：查询所有学生，包含未分配班级的学生）。

```sql
-- 左连接完整语法：查询所有学生及对应班级信息
SELECT s.id AS 学生ID,s.name AS 学生姓名,b.name AS 班级名称
FROM student s
LEFT JOIN banji b ON s.banji_id = b.id;

-- 筛选无班级的学生（左连接专属场景：右表数据为NULL）
SELECT s.id,s.name
FROM student s
LEFT JOIN banji b ON s.banji_id = b.id
WHERE b.id IS NULL;
```

### 4.5 右外连接 RIGHT JOIN

右外连接简称**右连接**，核心规则：**以右表为基准，保留右表所有数据，匹配左表关联数据，无匹配项则左表字段填充NULL**。

适用场景：查询附表全部数据，匹配主表关联数据（开发中使用频率极低，均可通过左连接改写替代）。

```sql
-- 右连接完整语法：查询所有班级及对应学生信息（含无学生的班级）
SELECT b.id AS 班级ID,b.name AS 班级名称,s.name AS 学生姓名
FROM student s
RIGHT JOIN banji b ON s.banji_id = b.id;

-- 筛选无学生的班级
SELECT b.id,b.name
FROM student s
RIGHT JOIN banji b ON s.banji_id = b.id
WHERE s.id IS NULL;
```

### 4.6 三种联结核心区别

1. **INNER JOIN（内连接）**：只展示左右表匹配成功的数据
2. **LEFT JOIN（左外连接）**：左表数据全部展示，右表无匹配则显示NULL
3. **RIGHT JOIN（右外连接）**：右表数据全部展示，左表无匹配则显示NULL

```sql
-- 左连接：展示所有学生，无班级的学生班级信息为NULL
SELECT * 
FROM student as s LEFT JOIN banji as b
on s.banji_id=b.id;
```

## 五、模糊查询与通配符

### 5.1 LIKE 模糊查询语法

仅用于字符类型字段匹配，支持两个通配符：

- `%`：匹配0个或多个任意字符
- `_`：匹配**1个**任意字符

> **注意事项：**
>
> - `%` 无法匹配 NULL 值，`LIKE '%'` 不会查询出字段为NULL的数据
> - 通配符前置（`%关键字`）会失效索引，查询效率极低，开发中尽量避免
> - 优先使用精准查询，非必要不使用模糊查询

### 5.2 常用模糊查询案例

```sql
-- 以张开头
SELECT * FROM student WHERE `name` LIKE '张%';
-- 张开头的两字姓名
SELECT * FROM student WHERE `name` LIKE '张_';
-- 姓名包含张字
SELECT * FROM student WHERE `name` LIKE '%张%';
-- 不以诺基亚开头
SELECT * FROM goods WHERE goods_name NOT LIKE '诺基亚%';
```
