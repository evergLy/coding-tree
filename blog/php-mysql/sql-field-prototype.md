## 列属性（字段属性）

6个属性：

null、默认值、列描述、主键、唯一键、自动增长

## 1、null

代表字段为空

注意：

1. 在设计表的时候，尽量不要让数据为空
2. MySQL的记录长度为65535个字节，如果一个表中有字段允许为null, 那么系统就会设计保留一个字节来存储null,最终有效存储长度为65534个字节

## 2、默认值default

用户不设置数据的时候，默认赋值

```sql
create table my_default(
  name varchar(10) not null,
  age int default 18
);

mysql> desc my_default;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| name  | varchar(10) | NO   |     | NULL    |       |
| age   | int(11)     | YES  |     | 18      |       |
+-------+-------------+------+-----+---------+-------+

-- 插入数据，未设置的值会使用默认值
insert into my_default (name) values('Tom');
mysql> select * from my_default;
+------+------+
| name | age  |
+------+------+
| Tom  |   18 |
+------+------+

-- 显示告知，使用默认值
insert into my_default values('Tom', default);
mysql> select * from my_default;
+------+------+
| name | age  |
+------+------+
| Tom  |   18 |
| Tom  |   18 |
+------+------+
```

## 3、列描述

comment 注释说明

```sql
 create table my_comment(
  name varchar(10) not null comment '姓名，不能为空',
  age int default 18  comment '年龄，默认18岁'
);

-- 查看注释
show create table my_comment;

CREATE TABLE `my_comment` (
  `name` varchar(10) COLLATE utf8mb4_general_ci NOT NULL COMMENT '姓名，不能为空',
  `age` int(11) DEFAULT '18' COMMENT '年龄，默认18岁'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci

```

## 4、主键 

primary key 主要的键，在一张表中，有且只有一个值，具有唯一性

主键默认不允许为空`not null`

### 4.1、创建主键

1、随表创建

- 方案1：直接在需要当做主键的字段之后，增加`primary key`
- 方案2：在所有字段之后增加`primary key(字段信息)`

```sql
create table my_primary_key_1(
    username varchar(10) primary key
);

create table my_primary_key_2(
    username varchar(10),
    primary key(`username`)
);

```

2、表后增加


基本语法
```sql
alter table 表名 add primary key(字段名);
```

示例
```sql
create table my_primary_key_3(
    username varchar(10)
); 

alter table my_primary_key_3 add primary key(username);
```

### 2、查看主键

```sql
-- 方案一：查看表结构
desc my_primary_key_1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| username | varchar(10) | NO   | PRI | NULL    |       |
+----------+-------------+------+-----+---------+-------+

-- 方案二：查看创建语句
show create table my_primary_key_2;
CREATE TABLE `my_primary_key_2` (
  `username` varchar(10) COLLATE utf8mb4_general_ci NOT NULL,
  PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci

```

### 3、删除主键

```sql
alter table 表名 drop primary key;
```

示例

```sql
alter table my_primary_key_1 drop primary key;

mysql> desc my_primary_key_1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| username | varchar(10) | NO   |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
```

### 4、复合主键

案例：一张学生选修课表
- 一个学生可以选修多个选修课
- 一个选修课也可以由多个学生来选
- 一个学生在一个选修课中只有一个成绩

```sql
-- 添加复合主键
create table my_score(
  student_no char(10),
  course_no char(10),
  score tinyint not null,
  primary key(student_no, course_no)
);

desc my_score;
+------------+------------+------+-----+---------+-------+
| Field      | Type       | Null | Key | Default | Extra |
+------------+------------+------+-----+---------+-------+
| student_no | char(10)   | NO   | PRI | NULL    |       |
| course_no  | char(10)   | NO   | PRI | NULL    |       |
| score      | tinyint(4) | NO   |     | NULL    |       |
+------------+------------+------+-----+---------+-------+
```


### 5、主键约束

1. 当前字段对应的数据不能为空
2. 当前字段对应的数据不能有任何重复

```sql
-- 第一次可以成功插入
mysql> insert into my_primary_key_2 (username) values('Tom');
Query OK, 1 row affected (0.00 sec)

-- 第二次插入失败
mysql> insert into my_primary_key_2 (username) values('Tom');
ERROR 1062 (23000): Duplicate entry 'Tom' for key 'PRIMARY'

mysql> select * from my_primary_key_2;
+----------+
| username |
+----------+
| Tom      |
+----------+
```

### 6、主键分类

- 业务主键: 学生ID，课程ID
- 逻辑主键: 自然增长的整型（应用广泛）


https://www.bilibili.com/video/BV1Vx411g7uJ?p=26&spm_id_from=pageDriver