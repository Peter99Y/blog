---
title: MySQL
---

## 概念

关系型数据库的编程语言

## 运行

命令行中输入 services.msc, 找到 mysql80, 右键启动
or
net start/stop mysql80 (以管理员身份运行打开命令行)

- 连接数据库
  找到 MySQL 提供的程序：MySQL 8.0 Command Line Client，
  or
  系统自带命令行执行指令连接数据库 (需先配置环境变量)
  mysql (-h 127.0.0.1 默认,可省略) (-p 3306 默认,可省略) -u root -p;

## sql

### DDL

定义数据库对象 (数据库，表，字段)

| 分类    | 字节 (byte) | SIGNED 范围 | UNSIGNED 范围 |
| ------- | ----------- | ----------- | ------------- |
| TINYINT | 1           | -128-127    | 0-255         |
| INT     | 4           | -           |               |
| BIGINT  | 8           | -           |               |
| FLOAT   | 4           | -           | 单精度浮点数  |
| DOUBLE  | 8           | -           | 双精度浮点数  |

---

| 分类     | 字节 (byte) | 描述                |
| -------- | ----------- | ------------------- |
| CHAR     | 0-255       | 定长字符串          |
| VARCAHR  | 0-65535     | 变长字符串          |
| DATA     | 3           | YYYY-MM-DD          |
| DATATIME | 8           | YYYY-MM-DD HH:MM:SS |

```JS
// 查看数据库
show databases;

// 查询当前所处的数据库名称
select database();

// 创建数据库
create database db_name;
create database [if exists] db_name [default charset utf8mb4];

// 删除数据库
drop database db_name;
drop database [if exists] db_name;

// 使用数据库
use db_name;
```

```JS
// 查看创建表的语句
show create table table_name;

// 查看数据库所有表
show tables;

// 查看表
desc table_name;

// 创建表
create table table_name (
    id int comment '编号',
    name varchar(20) comment '姓名',
    age tinyint unsigned comment '年龄',
    gender varchar(1) comment '性别' // 最后不能加逗号
) comment '用户表';

// 重命表名
alter table table_name rename to new_table_name;

// 删除表
drop table [if exists] table_name;

// 清空表
truncate table table_name;
```

```JS
// 新增字段
alter table table_name add column_name 数据类型(长度) [comment '注释'];

// 修改字段类型
alter table table_name modify column_name 数据类型(长度);

// 修改字段类型和字段名
alter table table_name change column_name 新字段名 数据类型(长度) [COMMENT '注释'] [约束];

// 删除字段
alter table table_name drop column_name;
```

### DML

对数据库表中的数据进行增删改查

字符串和日期类型包含在引号内

```JS
// 给指定字段添加数据
insert into table_name (字段名1,字段名2,...) values (值1,值2,...);
insert into table_name (字段名1,字段名2,...) values (值1,值2,...), (值1,值2,...); // 批量

// 给全部字段添加数据
insert into table_name values (值1,值2,...);
insert into table_name values (值1,值2,...), (值1,值2,...); // 批量

// 修改数据
update table_name set column_name='Tom',column_name='1',... [where username='xxx'];

// 删除数据
delete from table_name [where username='xxx'];
```

### DQL

查询数据库中表的记录

| 运算符         | 描述                                    |
| -------------- | --------------------------------------- |
| >              | -                                       |
| >=             | -                                       |
| <              | -                                       |
| <=             | -                                       |
| =              | 等于                                    |
| <> or !=       | 不等于                                  |
| between... and | 最小，最大                              |
| in(...)        | 在 in 之后的列表中的值，多选 1          |
| like           | 模糊匹配(\_匹配单个字符，%匹配任意字符) |
| is null        | 为 null                                 |
| and 或 &&      | 与                                      |
| or             | 或                                      |
| not 或 !       | 非                                      |

sql 执行顺序

```JS
from table_name, // 1
where 条件, //2
groud by 字段, having 条件,// 3
select 字段, //4
order by 字段, // 5
limit 0,10 // 6

select user.name username, from tb_user user where user.age > 15 order by username asc;
```

```JS
// 指定返回字段
select 字段1,字段2,... from 表名;

// 去重
select distinct 字段1,字段2,... from 表名;

// 设置返回的别名
select 字段1 as 别名,字段2 as 别名,... from 表名 表别名;

select * from tb_user where id=1;

select * from tb_user where is null;

select * from tb_user where is not null;

select * from tb_user where id != 1;

select * from tb_user where age >= 10 and age <= 20;
select * from tb_user where age between 15 and 20; // 等价于

select * from tb_user where age=10 or age=20 or age=30;
select * from tb_user where age in (10,20,30); // 等价于

// 查询名称长度为2个字符的用户
select * from tb_user where name like '__';

// 查询id最后一个字符为X的用户, %相当于正则的.
select * from tb_user where id_card like '%X';
```

| 聚合函数 | 描述 (null 会被过滤) |
| -------- | -------------------- |
| count    | 统计数量             |
| max      | 最大值               |
| min      | 最小值               |
| avg      | 平均值               |
| sum      | 求和                 |

```JS
// 统计员工数量
select count(*) from tb_user;

// 统计有ID的员工数量
select count(id_card) from tb_user;

// 统计工资的平均值
select avg(salary) from tb_user;

// 统计年龄最大值的数据
select max(age) from tb_user;

// 统计年龄大于18的员工的工资总和
select sum(salary) from tb_user where age > 18;
```

```JS
// where是对查询数据过滤，不满足where条件不参与分组；having是分组之后，对分组结果进行过滤；

select 字段 from 表名 [where 条件] group by 字段 [having 分组后过滤条件];

// 根据性别分组，返回字段性别和统计数量；
select gender, count(*) from  tb_user group by gender;
select name, gender, count(*) from  tb_user group by gender; // 查询字段一般是聚合函数和分组字段，展示其他字段无意义；

// 根据性别分组，返回字段性别和平均数量；
select gender, avg(salary) from tb_user gouyp by gender;

// 查询年龄小于30，根据部门分组，返回部门名称和统计数量，并且部门的统计数量大于1；
select apartment_name, count(*) from tb_user where age < 30 group by apartment_name having count(*) > 1;
```

```JS
select * from tb_user order by age asc;

// 根据年龄升序排序，年龄相同时 再按薪水降序排序；
select * from tb_user order by age asc, salary desc;

// 起始索引默认0开始，其实索引 = (查询页码 - 1) * 页数量；
select * from tb_user limit 起始索引, 页数量;

select * from tb_user limit 0, 10; // 查询第1页
select * from tb_user limit 10, 10; // 查询第2页
```

### DCL

创建数据库用户、控制数据库的访问权限

```JS
use mysql; // 就是mysql这个数据库

select * from user; // 查看所有用户

create user 'tom'@'localhost' identified by '123456'; // 限本地主机访问数据库
create user 'tom'@'%' identified by '123456'; // 任意地址访问数据库

alter user '用户名'@'主机名' identified by '新密码';

drop user '用户名'@'主机名';
```

```JS
show grants for '用户名'@'主机名'; // 查看用户权限
show grants for 'tom'@'%';

grant 权限 on db_name.tb_name to '用户名'@'主机名'; // 授予权限
grant all on study.* to 'tom'@'%';

revoke 权限 on db_name.tb_name from '用户名'@'主机名'; // 撤销权限
```

## 函数

## 约束

## 多表查询

## 事务
