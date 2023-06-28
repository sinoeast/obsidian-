## 检索数据

```sql
SELECT DISTINCT ven_id FROM products 
LIMIT 5; 
```

## 排序检索数据

```sql
SELECT prod_price, prod_name FROM products 
ORDER BY prod_price DESC, prod_name
```

## 过滤数据

```sql
SELECT prod_price, prod_name FROM products 
WHERE prod_price BETWEEN 5 AND 10
```

## 数据过滤

```sql
SELECT prod_price, prod_name FROM products 
WHERE prod_id NOT IN (50,60)
```

## 用通配符进行过滤

```sql
-- '%'表示任何字符出现任意次数；'_'只匹配单个字符
SELECT name FROM products
WHERE name LIKE '_jet%';
```

## 用正则表达式进行搜索

```sql
SELECT * from bge_api_log where uuid REGEXP '7$'
```

## 创建计算字段
ex:如果想在一个字段中既显示公司名，又显示公司的地址，但这两个信息一般包含在不同的表列中
```sql
SELECT 
	id*3+2 AS new_id，
	Concat(RTrim(name),'(',LTrim(country),')')AS vend_title
FROM venders
```

## 使用数据处理函数
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220926150046.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220926150136.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220926150215.png)

## 汇总数据（聚集函数）
聚集函数（aggregate function） 运行在行组上，计算和返回单个值的函数。
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220926150346.png)

## 分组数据


```sql
SELECT 
	id, 
	name, 
	COUNT(*) AS num_prods
FROM products
WHERE id>1000 AND id < 1005
GROUP BY name
HAVING COUNT(*)>= 2
ORDER BY id;
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212142720.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212142758.png)

## 使用子查询
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212142923.png)

## 联结表
- 内部联结
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212143212.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212143348.png)


## 创建高级联结
- 自联结
假如你发现某物品（其ID为DTNTR）存在问题，因此想知道生产该物品的供应商生产的其他物品是否也存在这些问题。此查询要求首先找到生产ID为DTNTR的物品的供应商，然后找出这个供应商生产的其他物品。
下面是解决此问题的一种方法：
子查询：
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212143608.png)
自联结：
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212143625.png)
用自联结而不用子查询 自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。虽然最终的结果是相同的，但有时候处理联结远比处理子查询快得多。应该试一下两种方法，以确定哪一种的性能更好。
- 左外联结
 ![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212144117.png)

## 组合查询
多数SQL查询都只包含从一个或多个表中返回数据的单条SELECT语句。MySQL也允许执行多个查询（多条SELECT语句），并将结果作为单个查询结果集返回。这些组合查询通常称为并（union）或复合查询（compound query）。
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212144309.png)
正如所见，并是非常容易使用的。但在进行并时有几条规则需要注意。
- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔（因此，如果组合4条SELECT语句，将要使用3个UNION关键字）。
- UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过分析各个列不需要以相同的次序列出）。
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。
如果遵守了这些基本规则或限制，则可以将并用于任何数据检索任务。
union all会有重复 union 不会
**包含或取消重复的行**
在分别执行时，第一条SELECT语句返回4行，第二条SELECT语句返回5行。
但在用UNION组合两条SELECT语句后，只返回了8行而不是9行。
UNION从查询结果集中自动去除了重复的行（换句话说，它的行为与
单条SELECT语句中使用多个WHERE子句条件一样）。因为供应商1002生产
的一种物品的价格也低于5，所以两条SELECT语句都返回该行。在使用
UNION时，重复的行被自动取消。
这是UNION的默认行为，但是如果需要，可以改变它。事实上，如果
想返回所有匹配行，可使用*UNION ALL*而不是*UNION*。
## 全文本搜索
并非所有引擎都支持全文本搜索 正如第21章所述，MySQL支持几种基本的数据库引擎。并非所有的引擎都支持本书所描述的全文本搜索。两个最常使用的引擎为MyISAM和InnoDB，前者支持全文本搜索，而后者不支持。这就是为什么虽然本书中创建的多数样例表使用 InnoDB ，而有一个样例表（productnotes表）却使用MyISAM的原因。如果你的应用中需要全文本搜索功能，应该记住这一点
## 插入数据
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212150152.png)
- 提高整体性能 
数据库经常被多个客户访问，对处理什么请求以及用什么次序处理进行管理是MySQL的任务。INSERT操作可能很耗时（特别是有很多索引需要更新时），而且它可能降低等待处理的SELECT语句的性能。
如果数据检索是最重要的（通常是这样），则你可以通过在INSERT和INTO之间添加关键字LOW_PRIORITY，指示MySQL降低INSERT语句的优先级，如下所示：
顺便说一下，这也适用于下一章介绍的UPDATE和DELETE语句。
- 插入多条数据（单条插入语句性能好一些）
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212150301.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212150310.png)
- 插入检索出的数据
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212150432.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212150440.png)
INSERT SELECT中的列名 为简单起见，这个例子在INSERT和SELECT语句中使用了相同的列名。但是，不一定要求列名匹配。
事实上，MySQL甚至不关心SELECT返回的列名。它*使用的是列的位置*，因此SELECT中的第一列（不管其列名）将用来填充表列中指定的第一个列，第二列将用来填充表列中指定的第二个列，如此等等。这对于从使用不同列名的表中导入数据是非常有用的。
## 更新和删除数据
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212150730.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212150750.png)
## 创建和操纵表
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212152310.png)
以下是几个需要知道的引擎：
- InnoDB是一个可靠的事务处理引擎（参见第26章），它不支持全文本搜索；
- MEMORY在功能等同于MyISAM，但由于数据存储在内存（不是磁盘）中，速度很快（特别适合于临时表）；
- MyISAM是一个性能极高的引擎，它支持全文本搜索（参见第18章），但不支持事务处理。
表新增字段
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212152441.png)

重命名表
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212152507.png)

## 使用视图
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212154632.png)
此查询用来检索订购了某个特定产品的客户。任何需要这个数据的人都必须理解相关表的结构，并且知道如何创建查询和对表进行联结。为了检索其他产品（或多个产品）的相同数据，必须修改最后的WHERE子句。
现在，假如可以把整个查询包装成一个名为productcustomers的虚拟表，则可以如下轻松地检索出相同的数据：
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212154639.png)
这就是视图的作用。productcustomers是一个视图，作为视图，它不包含表中应该有的任何列或数据，它*包含的是一个SQL查询*（与上面用以正确联结表的相同的查询）。
- 创建视图
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212154751.png)

## 使用存储过程
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212160722.png)
此存储过程有很大的变动。首先，增加了注释（前面放置--）。
在存储过程复杂性增加时，这样做特别重要。添加了另外一个参数taxable，它是一个布尔值（如果要增加税则为真，否则为假）。在存储过程体中，用DECLARE语句定义了两个局部变量。DECLARE要求指定变量名和数据类型，它也支持可选的默认值（这个例子中的taxrate的默认被设置为6%）。SELECT语句已经改变，因此其结果存储到total（局部变量）而不是ototal。IF语句检查taxable是否为真，如果为真，则用另一SELECT语句增加营业税到局部变量total。最后，用另一SELECT语句将total（它增加或许不增加营业税）保存到ototal。
COMMENT关键字 本例子中的存储过程在CREATE PROCEDURE语句中包含了一个COMMENT值。它不是必需的，但如果给出，将在SHOW PROCEDURE STATUS的结果中显示。
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212160840.png)

## 使用游标
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212162100.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212162109.png)
在这个例子中，我们增加了另一个名为t的变量（存储每个订单的合计）。此存储过程还在运行中创建了一个新表（如果它不存在的话），名为ordertotals。这个表将保存存储过程生成的结果。FETCH像以前一样取每个order_num，然后用CALL执行另一个存储过程（我们在前一章中创建）来计算每个订单的带税的合计（结果存储到t）。最后，用INSERT保存每个订单的订单号和合计。
游标（cursor）是一个存储在MySQL服务器上的数据库查询，它不是一条SELECT语句，而是被该语句检索出来的结果集。只能用于存储过程（和函数）
## 使用触发器
![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212162435.png)
CREATE TRIGGER用来创建名为newproduct的新触发器。触发器可在一个操作发生之前或之后执行，这里给出了AFTER INSERT，所以此触发器将在INSERT语句成功执行后执行。这个触发器还指定FOR EACH ROW，因此代码对每个插入行执行。在这个例子中，文本Product added将对每个插入的行显示一次。为了测试这个触发器，使用INSERT语句添加一行或多行到products中，你将看到对每个成功的插入，显示Product added消息。
## 管理事务处理

![](https://gitee.com/sinoeast/imgs/raw/master/img/20221212162709.png)

## 性能
- 首先，MySQL（与所有DBMS一样）具有特定的硬件建议。在学习和研究MySQL时，使用任何旧的计算机作为服务器都可以。但对用于生产的服务器来说，应该坚持遵循这些硬件建议。
- 一般来说，关键的生产DBMS应该运行在自己的专用服务器上。
- MySQL是用一系列的默认设置预先配置的，从这些设置开始通常是很好的。但过一段时间后你可能需要调整内存分配、缓冲区大小等。（为查看当前设置，可使用SHOW VARIABLES;和SHOWSTATUS;。）
- MySQL一个多用户多线程的DBMS，换言之，它经常同时执行多个任务。如果这些任务中的某一个执行缓慢，则所有请求都会执行缓慢。如果你遇到显著的性能不良，可使用SHOW PROCESSLIST显示所有活动进程（以及它们的线程ID和执行时间）。你还可以用KILL命令终结某个特定的进程（使用这个命令需要作为管理员登录）。
- 总是有不止一种方法编写同一条SELECT语句。应该试验联结、并、子查询等，找出最佳的方法。
- 使用EXPLAIN语句让MySQL解释它将如何执行一条SELECT语句。
- 一般来说，存储过程执行得比一条一条地执行其中的各条MySQL语句快。
- 应该总是使用正确的数据类型。
- 决不要检索比需求还要多的数据。换言之，不要用SELECT *（除非你真正需要每个列）。
- 有的操作（包括INSERT）支持一个可选的DELAYED关键字，如果使用它，将把控制立即返回给调用程序，并且一旦有可能就实际执行该操作。
- 在导入数据时，应该关闭自动提交。你可能还想删除索引（包括FULLTEXT索引），然后在导入完成后再重建它们。
- 必须索引数据库表以改善数据检索的性能。确定索引什么不是一件微不足道的任务，需要分析使用的SELECT语句以找出重复的WHERE和ORDER BY子句。如果一个简单的WHERE子句返回结果所花的时间太长，则可以断定其中使用的列（或几个列）就是需要索引的对象。
- 你的SELECT语句中有一系列复杂的OR条件吗？通过使用多条SELECT语句和连接它们的UNION语句，你能看到极大的性能改进。
- 索引改善数据检索的性能，但损害数据插入、删除和更新的性能。如果你有一些表，它们收集数据且不经常被搜索，则在有必要之前不要索引它们。（索引可根据需要添加和删除。）
- LIKE很慢。一般来说，最好是使用FULLTEXT而不是LIKE。
- 数据库是不断变化的实体。一组优化良好的表一会儿后可能就面目全非了。由于表的使用和内容的更改，理想的优化和配置也会改变。
- 最重要的规则就是，每条规则在某些条件下都会被打破。
## 索引
CREATE INDEX index_name  
ON table_name (column_name)