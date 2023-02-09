## ORM 管理器对象

那么应该怎样对数据表进行操作呢？我们可以直接使用类名（即数据表名）来插入数据，下面是插入数据的一种方法：

```
UserInfo.objects.create(name='jay',password='abc123')
```

上面代码插入一条名字是“jay”，密码是“abc123”的数据。读到这里，您可能会对“ objects ”产生疑问，所以在此处讲解一个重要的概念：每个继承自 `models.Model` 的模型类，都会有一个 objects 对象被同时继承下来，这个对象就叫做“管理器对象”，数据库的增删改查可以用 objects 管理器对象来实现。

利用 ORM 插入数据有两种方式，上面已经介绍了一种，下面介绍第二种方法，也就是创建 UserInfo 的实例对象，然后调用`save()`方法保存，代码如下：

```python
Obj=UserInfo（name="jay",password="abc123"）
Obj.name="john"
Obj.save()
```

上述代码中 name 属性值会被赋值为“john”，最后调用 `save()`方法保存。

ORM 的增删改查称为 CURD 操作，下面列举几个常用语句： 

```python
UserInfo.objects.all()#查询表中的所有记录
UserInfo.objects.filter(name_contains='j')查询表中name含有“j”的所有记录,被使用较多
UserInfo.objects.get(name="john")#有且只有一个查询结果，如果超出一个或者没有,则抛出异常
UserInfo.objects.get(name="john").delete()#删除名字为john的记录
UserInfo.objects.get(name="john").update(name='TOM')#更新数据表的name为TOM
```