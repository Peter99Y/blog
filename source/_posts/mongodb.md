---
title: Mongodb
---

##### 概念

-   非关系型数据库 & 关系型数据库
    NoSql： Not Only SQL 指的是非关系型的数据库，是已 key-value 形式存储，和传统关系型数据库不一样，不一定遵循传统数据库的一些标准要求，比如说 SQL 标准、表结构等.
    强调的是与关系数据库区别对待，最近这些年被提及更多的是强调协助解决大数据相关问题.
-   MongoDb (非关系型数据库)
    最像关系数据库的 NoSql 数据库；没有行、列的概念。用 JSON 来存储数据。
    "集合" = "表"，"文档" = "行"

---

#### 表与表之间的关系

-   一对一关系：一个身份证对应一个驾驶证；
-   一对多关系：一个班级对应多名学生；
-   多对多关系：一个商品有多个关注用户；一个用户关注多个商品；

---

> cmd 打开命令行，输入 mongo 连接数据库;

```
    show dbs;
    use itying;                         切换到 itying数据库(如没有就会创建);
    show collections;
    db.dropDatabase();                  删除数据库(先use切换到某个数据库);
    db.user.drop();                     删除user集合;
```

-   插入数据

```
    插入一条数据代表 该集合还没有集合的数据库就创建成功;
    db.user.insert( {"name":"xiaoming"} );
```

-   查询数据

```
db.user.find();
db.user.findOne();
db.user.find({age:{$gte:25}}).count();
db.user.find({}, {age:1});                      /*查询只展示 age 这一列*/
```

-   1 升序; -1 降序

```
db.user.find().sort({age:1});		            /*查询只展示 age 这一列*/
db.user.find().limit(5); 	                    /*查询 前5条数据*/
db.user.find.skip(10);		                    /* 查询 第10条以后的数据*/
db.user.find.skip(5).limit(2);                  /*查询 第6,7条 的数据*/

    /*查询 第3页，每页10条数据;	起始页 = 第3页 - 1 * 10条*/
db.user.find.skip(20).limit(10);
```

```
db.user.find( {"age":22} );                       /*查询 age = 22的数据*/
db.user.find( {"age":22, name:"zhangsan"} );      /* 查询 age = 22 & name = "zhangsan"的数据*/
db.user.find( { $or: [ {age:22, {age:12}} ]} );   /*查询 age = 22 or age = 12的数据*/

db.user.find( {age:{ $gt:22} });    		      /*查询 age > 22的数据*/
db.user.find( {age:{ $gt:22} },{age:1})
db.user.find( {age:{ $gte:22} });  		          /*查询 age >= 22的数据*/
db.user.find( {age:{ $gte:20, $lte:25} });        /*查询 age >= 20 并且 age <= 25*/
```

-   模糊查询(搜索)

```
db.user.find( {name: /zhang/} );		 		/*查询 name中包含 "zhang" 的所有数据*/
db.user.find( name: /^zhang/ );        		    /*查询 name中包含 "zhang" 开头的所有数据		*/
db.user.find( name: /zhang$/ );                 /*查询 name中包含 "zhang" 结尾的所有数据*/

db.user.find({},{name:1,age:1});                /*查询指定列age; name、age数据*/
db.user.find( { age:{$gt:25}}, {name:1});       /*查询指定name、age数据，age>25*/
db.user.find().sort({age:1})
```

-   修改

```
db.user.update({name: "zhangsan", age: 20}, {$set:{male:'female'}});
/*  { "name" : "zhangsan", "age" : 20, "male" : "female", "uid" : 110011 }  */
```

-   没有$set 就会整条替换数据;

```
db.user.update({uid:110011}, {male:'female'});
/*  { "male" : "female" } */
```

-   批量修改；添加{multiple:true}; 否则只是第一条数据被修改

```
db.user.update({}, {$set:{male:"female"}}, {multi: true});
```

-   删除数据;
    `注意：({}); 空对象会把集合所有数据删除`

```
db.user.remove({uid:110011});
db.user.remove({age:{$gt:30}});
db.user.remove({age:{$gt:30}},{justOne:true})    /*只删除一条*/
```

---

#### 索引

索引是对数据库中一列 or 多列的值进行排序的一种结构，可以让查询数据库变得更快，但更新 or 增加操作变得慢一些
MongoDB 的索引几乎与传统的关系型数据库一样，这其中包括依稀额基本的查询优化。

-   获取当前列表索引

```
db.user.getIndexes();
```

-   查询具体的执行时间

```
db.collectionName.find().explain("executionStats");
/*  executionStats.executionTimeMillis: 8  */
```

-   设置索引; 1 表示某个键的索引按照升序存储，-1 表示降序；

```
db.collectionName. createIndex({"columnName": 1});
```

-   删除索引

```
db.collectionName.dropIndex({"columnName": 1});
```

###### 复合索引

```
db.collectionName. createIndex({"userName": 1, "age": 1});
```

该索引被创建后，基于 userName 和 age (查询的索引顺序无关系)的查询会用到该索引，or userName 的查询会用到该索引。只有 age 不行！
也就是说查询条件中必须包含索引的前 N 个索引列。
`注意：随着集合的增长，如果没有对索引的键调用sort，MongoDB需要将所有数据提取到内存做排序。因此在做无索引排序时，如果数据量过大以致于无法在内存中进行排序，MongoDB会报错`

###### 唯一索引

给某个字段设置唯一索引，这个字段将不能重复; 重复插入相同的唯一索引的字段时会报错

```
db.collectionName. createIndex({userId: 1}, {unique: true});
```

---

#### 账户权限

-   MongoDB 数据库角色
    -   超级管理员账户: root (只 admin 数据库中可用)
    -   数据库管理角色: dbAdmin、dbOwner(常用)、userAdmin;
    -   数据库用户角色: read、readWrite;
    -   所有数据库权限: readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase;

```
use sthCollection;
show users;
//  "user": "testAccount"
db.dropUser("testAccount")
```

1.  创建超级管理员账户

```
  use sthCollection;

  db.createUser({
    	user: "testAccount",
    	pwd:  "123456",
    	roles:[ {role:"dbAdminAnyDatabase", db:"admin"} ]
  });
  // Successfully added user;
```

2.  C:/Program Files/MongoDB/Server/4.4/bin/mongod.cfg
    security:
    authorization: enabled(启动权限认证)

3.  重启服务器

| ![](https://upload-images.jianshu.io/upload_images/20409039-165d41ec10a4a1b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) | ![](https://upload-images.jianshu.io/upload_images/20409039-0df70e81ab69a0d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) |
| ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |

4.  用账户连接数据库

```
mongo 数据库名( or 远程方式192.168.1.200:27017) -u 用户名 -p 密码
```

5.  在服务器中连接数据库

```
const url = "mongodb://testAccount:123456@localhost:27017";
```

---

#### aggregate 聚合管道

使用聚合管道可以对集合中的文档进行变换和组合。
应用场景：**表关联查询**、数据统计。

-   $project: 修改文档的结构，用来增加、删除、重命名、显示某些字段；

```
db.order.find({}, { order_id:1, all_price:1 });

db.order.aggregate([
  {$project: {order_id:1, all_price: 1}}
]);

/*
  { "order_id" : "1", "all_price" : 100 }
*/
```

-   $match：过滤文档

```
db.order.find(
  {all_price: {$lte:30}},
  {order_id: 1, all_price: 1}
);

db.order.aggregate([
  {$project: {order_id:1, all_price:1}},
  {$match: {all_price: {$lte: 30}}}
]);
```

-   $group：对集合中的文档进行分组，可用于统计结果

```
// 以order_id进行分组，每组的num进行相加 放入到total中;

db.order_item.aggregate([
  {
     $group:  {
       _id: "$order_id",
       total: {$sum:  "$num"}
     }
  },
]);
```

-   $sort：将集合中的文档进行排序

```
db.order_item.aggregate([
  {
    $project: {price: 1}               // 展示price列
  },
  {
    $match: { price: {$gte: 10} }     // 条件是price大于等于10
  },
  {
    $sort: {price: -1}                //  price降序排列
  }
]);
```

-   limit + skip

```
db.order_item.aggregate([
  {
    $project: {price: 1};
  },
  {
    $match: {price: {$gte: 10} }
  },
  {
    $sort: {price: -1}
  },
  {
    $limit: 10               // 每页10条
  },
  {
    $skip: 20,               // 从第3页开始
  }
]);
```

-   **$lookup 表关联查询**

```
db.order.aggregate([
  {
    $lookup:{
      from: "order_item",        // 需要关联的表
      localField: "id",          // 关联字段
      foreignField: "pid",       // 关联表的字段
      as:  "items"               // 关联后的数据放在items字段中
    }
  },
  {
    $match: {price: {$gte: 10}}
  },
  {
    $sort: {price: -1}
  },
]);
```

---

##### Nodejs 项目中连接 MongoDB

```
npm install mongodb --save
```

github 详情: https://mongodb.github.io/node-mongodb-native/3.5/quick-start/quick-start/
