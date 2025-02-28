---
title: MySQL
---

## 概念

关系型数据库的编程语言

- 运行 mysql
  命令行中输入 services.msc, 找到 mysql80, 右键启动
  or
  net start/stop mysql80 (以管理员身份运行打开命令行)

- 连接数据库
  找到 MySQL 自带的程序：MySQL 8.0 Command Line Client，
  or
  系统命令行执行指令，连接数据库 (需先配置环境变量)
  mysql (-h 127.0.0.1 默认,可省略) (-p 3306 默认,可省略) -u root -p;

## 函数

| 函数                     | 描述                                                                     |
| ------------------------ | ------------------------------------------------------------------------ |
| concat(s1, s2,...)       | 字符串拼接，将 s1,s2 拼接成字符串返回                                    |
| lower(str)               | 将字符串转换为小写                                                       |
| upper(str)               | 将字符串转换为大写                                                       |
| trim(str)                | 去掉字符串首尾的空格                                                     |
| substring(str, pos, len) | 从字符串 str 中截取 len 个字符，从 pos 开始                              |
| lpad(str, len, pad)      | 左填充字符串，str 为填充的字符串，len 为填充后的长度，pad 为填充的字符串 |
| rpad(str, len, pad)      | 右填充字符串，str 为填充的字符串，len 为填充后的长度，pad 为填充的字符串 |

---

| 函数         | 描述                                 |
| ------------ | ------------------------------------ |
| cell(x)      | 返回 x 的自然对数，即以 e 为底的对数 |
| floor(x)     | 返回小于或等于 x 的最大整数          |
| mod(x)       | 返回 x 的余数                        |
| round(x)     | 返回最接近 x 的整数                  |
| round (x, d) | 返回 x 四舍五入到 d 位小数           |

---

| 函数                        | 描述                         |
| --------------------------- | ---------------------------- |
| curdate()                   | 返回当前日期                 |
| curtime()                   | 返回当前时间                 |
| now()                       | 返回当前日期和时间           |
| year(d)                     | 返回 d 的年                  |
| month(d)                    | 返回 d 的月                  |
| day(d)                      | 返回 d 的日                  |
| date_add(d, interval 3 day) | 返回 d 加上 3 day 后的时间值 |
| datediff(d1, d2)            | 返回 d1 与 d2 之间的天数     |

---

| 函数          | 描述                                 |
| ------------- | ------------------------------------ |
| if(val, t, f) | 如果 val 为 true，返回 t，否则返回 f |

```
// 左填充
update tb_user set work_no = LPAD(work_no, 4, '0');

// 查询入职时间
select username, datediff(curdate(), create_time) as 'joining_time' from tb_user order by joining_time desc;
```

## sql

### alter

DDL 定义数据库对象 (数据库，表，字段)

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

```
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

```
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

```SQL
// 新增字段
alter table table_name add column_name 数据类型(长度) [comment '注释'];

// 修改字段类型
alter table table_name modify column_name 数据类型(长度);

// 修改字段类型和字段名
alter table table_name change column_name 新字段名 数据类型(长度) [COMMENT '注释'] [约束];

// 删除字段
alter table table_name drop column_name;
```

### insert

DML 对数据库表中的数据进行增删改查

字符串和日期类型包含在引号内

```JS
// 插入一条/多条数据，按照对应字段名的顺序添加数据
insert into table_name (字段名1, 字段名2,...) values (字段值1, 字段值2,...), (字段值1, 字段值2,...);

// 插入一条/多条数据，按照表全部字段顺序给每个字段添加数据
insert into table_name values (表字段1值, 表字段2值,...), (表字段1值, 表字段2值,...);

// 修改数据
update table_name set column_name='Tom',column_name='1',... [where username='xxx'];

// 删除数据
delete from table_name [where username='xxx'];
```

### select

DQL 查询数据库中表的记录

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

```sql
from table_name, // 1
where 条件, //2
groud by 字段, having 条件,// 3
select 字段, //4
order by 字段, // 5
limit 0,10 // 6

select user.name as username, from tb_user as user where user.age > 15 order by username asc;
```

```sql
-- 指定返回字段
select 字段1,字段2,... from 表名;

-- 去重
select distinct 字段1,字段2,... from 表名;

-- 设置返回的别名, as可省略
select 字段1 as 别名,字段2 as 别名,... from 表名 表别名;

select * from tb_user where id != 1;

select * from tb_user where id_card is null; -- 查询id_card 为 null的数据

select * from tb_user where id_card is not null; -- 查询id_card 不为 null的数据

select * from tb_user where age >= 10 and age <= 20;
select * from tb_user where age between 10 and 20; -- 等价于

select * from tb_user where age=10 or age=20 or age=30;
select * from tb_user where age in (10, 20, 30); -- 等价于

-- 查询名称长度为2个字符的用户
select * from tb_user where name like '__';

-- 查询id最后一个字符为X的用户, %相当于正则的.
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
select gender, avg(salary) from tb_user group by gender;

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

### grant

DCL 创建数据库用户、控制数据库的访问权限

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

## 约束

| 约束           | 描述                             |
| -------------- | -------------------------------- |
| UNIQUE         | 唯一约束, 字段唯一值             |
| DEFAULT        | 默认约束, 保存数据时，指定默认值 |
| NOT NULL       | 非空约束, 字段数据必填           |
| CHECK          | 检查约束, 字段值满足条件         |
| PRIMARY KEY    | 主键约束, 唯一且非空             |
| FOREIGN KEY    | 外键约束, 关联其他表             |
| AUTO_INCREMENT | 自增约束, 插入数据时，自增       |

```SQL
create table table_name(
  id int primary key auto_increment comment '主键', // 主键, 自增
  name varchar(20) not null unique comment '姓名', // 唯一, 不能为空
  age init check (age>18 && age <= 120) comment '年龄', // 条件检查
  status char(1) default '1' comment '状态', // 默认值
  gender char(1) comment '性别'
) comment '用户表';
```

| 外键行为          | 描述                                                                                              |
| ----------------- | ------------------------------------------------------------------------------------------------- |
| NOACTION/RESTRICT | 当父表删除/更新数据时，如有对应外键字段，则不允许删除/更新 (NOACTION 与 restrict 一致)            |
| CASCADE           | 当父表**删除/更新**数据时，如有对应外键字段，则同样删除/更新子表数据                              |
| SET NULL          | 当父表**删除**数据时，如有对应外键字段，则将子表外键字段值设为 NULL (需设置该外键允许数据为 NULL) |
| SET DEFAULT       | 父表有变更时，子表将外键字段设置为默认值                                                          |

```SQL
// 主表tb_department，查看是否有主键约束
show create table tb_department;

// 主表tb_department，添加主键约束
alter table tb_department add primary key (id);

// 删除外键约束
alter table tb_name drop foreign key fk_department_id;
```

## 多表关系

### 一对多关系

如一个部门对应多名员工；
在多的一方设置外键， 指向少的一方的主键；(设置外键时，需先设置主表 primary key 主键约束)

```SQL
-- 创建表，并添加外键约束
create table tb_name add [constraint] [外键名称] foreign key (外键字段名) references 主表名(主键名);

-- 创建tb_user子表；添加外键约束
create table tb_user add constraint fk_department_id foreign key (department_id) references tb_department(id);

-- 已有tb_user子表，添加外键约束
alter table tb_user add constraint fk_department_id foreign key (department_id) references tb_department(id) on update cascade on delete cascade;
```

### 多对多关系

如一个商品有多个关注用户，一个用户关注多个商品；
创建第三方中间表，中间表包含至少包含**两个外键**，分别关联两方主键；

```SQL
create table tb_mid_user_goods (
    id int auto_increment not null comment '主键',
    user_id int not null comment '用户id',
    good_id int not null comment '商品id',
    constraint fk_user_id foreign key (user_id) references tb_user(id),
    constraint fk_good_id foreign key (good_id) references tb_goods(id),
    primary key (id)
) comment '用户与商品中间表';
```

### 一对一关系

如一个身份证对应一个驾驶证；
在任意一方加入外键 (UNIQUE)，关联另一方主键；
多用于单表拆分，将基础字段放一张表，详情字段放在另一张表，以提升操作效率；

```SQL
create table tb_user_details (
    id int auto_increment not null comment '主键id',
    grade VARCHAR(50) comment '年级',
    major VARCHAR(50) comment '专业',
    university VARCHAR(50) comment '大学',
    user_id int unique not null comment '用户id',
    constraint fk_detail_user_id foreign key (user_id) references tb_user (id),
    PRIMARY key (id)
) COMMENT '用户详情表';
```

## 多表查询

#### 内连接

查询两张表**交集**数据（有重复数据，使用 distince 可去重）

```SQL

-- 这里会查询两张表 tb_user.department_id = tb_department.id 条件为真的数据

-- 隐式内连接
select * from tb_user, tb_department where tb_user.department_id = tb_department.id;

-- 显示内连接
select * from tb_user [inner] join tb_department on tb_user.department_id = tb_department.id;
```

#### 外连接

查询两张表**合集**数据（有重复数据，使用 distince 可去重）

```SQL

-- 左外连接；查询左侧用户表的所有数据，并且每条用户数据会展示部门表对应的部门数据
select * from tb_user left [outer] join tb_department on tb_user.department_id = tb_department.id;

-- 右外连接；查询右侧部门表的所有数据，并且每条部门会展示用户表对应的用户数据
select * from tb_user right [outer] join tb_department on tb_user.department_id = tb_department.id;
```

#### 自连接

当前表与自身连接查询，自连接必须使用表别名；
可以使用自连接或外连接方式；
看成两张表；

```SQL
select a.user, b.user from tb_user as a, tb_user as b where a.superior_id = b.id;

select a.user '员工', b.user '上级' from tb_user as a left outer join tb_user as b on a.superior_id = b.id;
```

#### 联合查询

把多次查询的结果合并起来，形成一个新的查询结果集；
union 返回交集，数据会去重；
union all 返回合集, 有重复部分；
select \* 查询的展示**字段保持一致**；

```sql
select * from A表... union [all] select * from B表...;

select * from tb_user where salary > 5000
union all
select * from tb_user where age > 20;
```

#### 子查询

子查询/嵌套查询
子查询外部的语句可以是 select、insert、update、delete；
查询位置：select 之后、from 之后、where 之后；

| 操作符 | 描述                             |
| ------ | -------------------------------- |
| IN     | 在指定的范围之内，多选 1         |
| NOT IN | 不在指定的范围之内               |
| ANY    | 子查询返回的列表中，满足任意一个 |
| some   | 子查询返回的列表中，满足任意一个 |
| ALL    | 子查询返回的列表中，满足全部     |

- 标量子查询 (子查询结果为单个值)

```sql
从员工表中查询某个部门的所有员工信息（1.查询某个部门的id，2.根据部门id查询所有员工信息）

select id from tb_department where name = '研发部';
select * from tb_user where department_id = 3;
-- 等价于
select * from tb_user where department_id = (select id from tb_department where name = '研发部');


查询tom之后创建的员工信息（1.查询tom的创建时间，2.查询创建时间之后的员工信息）

select create_time from tb_user where user = 'Tom';
select * from tb_user where create_time > '2024-11-20';
-- 等价于
select * from tb_user where create_time > (select create_time from tb_user where user = 'Tom');
```

- 列子查询 (子查询结果为一列)

```sql
查询研发部和财务部的所有员工信息（1.查询这2个部门id，2.根据部门id查询所有员工信息）

select id from tb_department where name = '研发部' or name = '财务部';
select * from tb_user where department_id in (2,3);
-- 等价于
select * from tb_user where department_id in (select id from tb_department where name = '研发部' or name = '财务部');


查询比研发部所有人工资都高的员工信息（1.查询研发部的id，2.根据财务部id查询财务部所有人工资，3.查询大于财务部所有人的工资的所有员工）

select salary from tb_user where department_id = (select id from tb_department where name = '研发部');
select * from tb_user where salary > 7000;
-- 等价于
select * from tb_user where salary > all ( select salary from tb_user where department_id = (select id from tb_department where name = '研发部') );
```

- 行子查询 (子查询结果为一行多列 > 结果为一条数据，多个列字段 > 根据返回的多个字段组合的条件查询数据)

```sql
查询与某个员工相同工资 且 相同上司的员工信息；

select salary, superior_id from tb_user where username = 'Mary';
select * from tb_user where salary = 6000 and superior_id = 1;

// 等价于；(将salary, superior_id组合作为一个条件)
select * from tb_user where (salary, superior_id) = (6000, 1)

-- 等价于；
select * from tb_user where (salary, superior_id) = (select salary, superior_id from tb_user where username = 'Mary');
```

- 表子查询 (子查询结果为多行多列，相当于返回一张表，常用操作符为 IN)
  数据会重复；

```sql
查询与Tom 和 William 的职位和薪资一致的员工信息 （1.查询Tom和William各自的职位和薪资，2.根据Tom的职位和薪资，根据William的职位和薪资来查询员工信息，数据会重复）
-- select position, salary from tb_user where username = 'Tom' or username = 'William';
select * from tb_user where (position, salary) in ( select position, salary from tb_user where username = 'Tom' or username = 'William' );


查询入职日期是 '2024-11-20' 之后的员工信息及其部门信息（1.查询入职日期是 '2024-11-20' 之后的员工信息，2.根据这部分员工查询对应的部门信息）
-- select * from tb_user where create_time > '2024-11-20';
select user.*, dept.* from (select * from tb_user where create_time > '2024-11-20') as user left join tb_department as dept on user.department_id = dept.id;
```

#### 案例

```sql
-- 查询员工的姓名、年龄、职位、部门信息

-- 隐式内连接
select u.username, d.name from tb_user as u, tb_department as d where u.department_id = d.id;

-- 显示内连接 (on外键与主键的id)
select u.username, d.name from tb_user as u inner join tb_department as d on u.department_id = d.id;


-- 查询年龄小于 30 岁的员工姓名、部门信息；
select u.username, d.name from tb_user as u inner join tb_department as d on u.department_id = d.id where u.age < 30;
```

```sql
-- 查询有员工的部门id和部门名称；

-- 隐式内连接
select distinct d.id, d.name from tb_user as u, tb_department as d where u.department_id = d.id;

-- 显示内连接
select distinct d.id, d.name from tb_user as u inner join tb_department as d on u.department_id = d.id where u.department_id = d.id;


-- 查询所年龄大于 30 岁的员工，及其所属的部门名称；如果员工没有分配部门，也展示出来 (合集)；
select d.name as departmentName, u.* from tb_user as u left join tb_department as d on u.department_id = d.id where u.age > 30;


-- 查询所有员工的工资等级；
select s.name as levelName, u.* from tb_user as u inner join tb_salaryLevel as s where s.min < u.salary and u.salary <= s.max;
select s.name as levelName, u.* from tb_user as u inner join tb_salaryLevel as s where u.salary between s.min and s.max;
```

```sql
-- 查询 研发部 所有员工的信息 以及工资等级；

select d.name as 'departmentName', u.username, s.name as 'salaryLevel'
from
    tb_user as u,
    tb_department as d,
    tb_salaryLevel as s
where
    u.department_id = d.id
    and (
        u.salary between s.min and s.max
    )
    and d.name = '研发部';


-- 查询研发部员工的平均薪资；
select avg(u.salary) as '部门平均薪资' from tb_user as u, tb_department as d where u.department_id = d.id;


-- 查询工资比 Tom 高的员工信息
select * from tb_user as u where u.salary > (select salary from tb_user where username = 'Tom');


-- 查询工资比平均工资高的员工信息
select * from tb_user as u where salary > (select avg(salary) from tb_user);


-- 查询工资低于本部门平均工资的员工信息
-- select avg(u1.salary) from tb_user as u1 where department_id = 1;  查询部门为1的平均工资

-- select * from tb_user as u2 where u2.salary < (select avg(u1.salary) from tb_user as u1 where department_id = 1); 查询低于 部门为1的平均工资 的员工信息

select *
from tb_user as u2
where
    u2.salary < (
        select avg(u1.salary)
        from tb_user as u1
        where
            u1.department_id = u2.department_id
    );
```

```sql
-- 查询所有部门信息，并统计部门的员工人数
-- select id, name from tb_department; 查询部门信息

-- select count(*) from tb_user as u where u.department_id = 2;  统计部门人数

select
    d.id,
    d.name,
    (select count(*) from tb_user as u where u.department_id = d.id) as '部门人数'
from
    tb_department as d;
```

## 事务

> 每一条 sql 语句都是一个事务，且默认自动提交；设置事务提交方式改为手动提交；

> mysql 的事务是默认自动提交的，当执行一条 DML 语句后，mysql 会立即隐式的提交事务；

> 一组操作的集合，是不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，要么同时成功，要么同时失败；
> 如：转账操作，先查询张三余额，再将张三的余额减少，再查询李四余额，再给李四增加余额，如果中间某一步失败，则整个事务失败，之前的操作全部撤销；将以上操作控制在一个事务内，在执行操作前，先开启事务，执行第一步、第二步、第三步... 如果其中一步抛出异常，则事务回滚，之前的操作全部撤销。

- 原子性：事务是一组不可分割的最小操作单位，要么全部成功，要么全部失败；
- 一致性：事务完成时，必须使所有的数据都保持一致状态；
- 隔离性：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行；
- 持久性：事务一旦提交或回滚，它对数据库中的数据的改变是永久的；

```sql method first
select @@autocommit;    -- 1是自动提交，0是手动提交

set @@autocommit = 0; -- 设置事务提交方式

select * from tb_account where name = 'Tom';

update tb_account set money = money - 1000 where name = 'Tom';

update tb_account set money = money + 1000 where name = 'Mary';

commit; -- 提交事务

-- rollback; -- 回滚事务
```

```sql method second 常
start TRANSACTION; -- 开启事务

select * from tb_account where name = 'Tom';

update tb_account set money = money - 1000 where name = 'Tom';

update tb_account set money = money + 1000 where name = 'Mary';

-- 要么所有事务操作执行成功，提交事务
commit;

-- 要么回滚事务；
-- ROLLBACK;
```

#### 事务的并发问题

- 脏读：一个事务读取到另外一个事务还没有提交的数据；
- 不可重复读：一个事务读取同一个数据的两次查询结果不一致，因为另外一个事务操作导致了这个事务两次查询结果不一致；
- 幻读：一个事务按照条件查询数据时，没有查询到对应的数据行，但在执行插入数据时，又发现这行数据已经存在了；

| 事务隔离级别                    | 脏读 | 不可重复读 | 幻读 |
| ------------------------------- | ---- | ---------- | ---- |
| 读未提交 Read uncommitted       | √    | √          | √    |
| 读已提交 Read committed         | ×    | √          | √    |
| 可重复读 Repeatable read (默认) | ×    | ×          | √    |
| 串行化 Serializable             | ×    | ×          | ×    |

```sql
-- 查询事务隔离级别
select @@transaction_isolation; 

-- session指当前会话客户端，global指全局客户端；
set [session|global] transaction isolation level [read uncommitted|read committed|repeatable read|serializable];
```
