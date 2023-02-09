# MongoDB 概念解析

不管我们学习什么数据库都应该学习其中的基础概念，在mongodb中基本的概念是文档、集合、数据库，下面我们挨个介绍。

下表将帮助您更容易理解Mongo中的一些概念：
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108152118.png)

# MongoDB 创建集合
`db.createCollection(name, options)`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108152345.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108152501.png)

# MongoDB 删除集合
`db.collection_name.drop()`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108152629.png)

# MongoDB 插入文档
`db.collection_name.insertOne({xxx})` 用于向集合插入一个新文档
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108153220.png)

`db.collection_name.insertMany([{xxx}])` 用于向集合插入一个多个文档
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108153525.png)

# MongoDB 更新文档

```
db.collection_name.updateOne(
   <query>,
   <update>
)
```
```
db.collection_name.updateMany(
   <query>,
   <update>
)
```
-   **query** : update的查询条件，类似sql update查询内where后面的。
-   **update** : update的对象和一些更新的操作符($ set:设置，$inc：增加（要数字类型）)，也可以理解为sql update查询内set后面的
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108160011.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108160145.png)

# MongoDB 删除文档
`db.collection_name.deleteOne(<query>)`
`db.collection_name.deleteMany(<query>)`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108155533.png)

# MongoDB 查询文档
`db.collection_name.find(query)`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108163717.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221108163902.png)
