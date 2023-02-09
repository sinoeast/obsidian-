# Django新特性之外键

![image-20220726234835892](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220726234835892.png)

当执行 **python manage.py makemigrations** 出现错误：TypeError: init() missing 1 required positional argument: ‘on_delete’

**解决方案：**

定义外键的时候需要加上 on_delete=;
即：contract = models.ForeignKey(Contract`, on_delete=models.CASCADE`)

**原因如下：**

**django 升级到2.0之后,表与表之间关联的时候,必须要写on_delete参数,否则会报异常:**  
**TypeError: init() missing 1 required positional argument: ‘on_delete’**  

- None, # 删除关联表中的数据时,当前表与其关联的field的行为  

- CASCADE, # 删除关联数据,与之关联也删除  
- DO_NOTHING, # 删除关联数据,什么也不做  
- PROTECT, # 删除关联数据,引发错误ProtectedError  
- SET_NULL, # 删除关联数据,与之关联的值设置为null（前提FK字段需要设置为可空,一对一同理）
models.ForeignKey('关联表', on_delete=models.SET_NULL, blank=True, null=True)  

- SET_DEFAULT, # 删除关联数据,与之关联的值设置为默认值（前提FK字段需要设置默认值,一对一同理）  
models.ForeignKey('关联表', on_delete=models.SET_DEFAULT, default='默认值')  

- SET, # 删除关联数据, 
  a. 与之关联的值设置为指定值,设置：models.SET(值)  
  b. 与之关联的值设置为可执行对象的返回值,设置：models.SET(可执行对象)  

由于多对多(ManyToManyField)没有 on_delete 参数,所以以上只针对外键(ForeignKey)和一对一(OneToOneField)

# 官方文档：

## 关系字段[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#module-django.db.models.fields.related)

Django 还定义了一组表示关系的字段。



### `ForeignKey`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#foreignkey)

- *class* `ForeignKey`(*to*, *on_delete*, ***options*)[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey)

  

一个多对一的关系。需要两个位置参数：模型相关的类和 [`on_delete`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey.on_delete) 选项。

要创建一个递归关系——一个与自己有多对一关系的对象——使用 `models.ForeignKey('self', on_delete=models.CASCADE)`。

如果你需要在一个尚未定义的模型上创建关系，你可以使用模型的名称，而不是模型对象本身：

```
from django.db import models

class Car(models.Model):
    manufacturer = models.ForeignKey(
        'Manufacturer',
        on_delete=models.CASCADE,
    )
    # ...

class Manufacturer(models.Model):
    # ...
    pass
```

在 [抽象模型](https://docs.djangoproject.com/zh-hans/4.0/topics/db/models/#abstract-base-classes) 上以这种方式定义的关系在模型被子类化为具体模型时得到解析，与抽象模型的 `app_label` 不是相关的：

`products/models.py`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#id3)

```
from django.db import models

class AbstractCar(models.Model):
    manufacturer = models.ForeignKey('Manufacturer', on_delete=models.CASCADE)

    class Meta:
        abstract = True
```

`production/models.py`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#id4)

```
from django.db import models
from products.models import AbstractCar

class Manufacturer(models.Model):
    pass

class Car(AbstractCar):
    pass

# Car.manufacturer will point to `production.Manufacturer` here.
```

要引用定义在另一个应用程序中的模型，你可以明确地用完整的应用程序标签指定一个模型。例如，如果上面的 `Manufacturer` 模型是在另一个叫做 `production` 的应用程序中定义的，你需要使用：

```
class Car(models.Model):
    manufacturer = models.ForeignKey(
        'production.Manufacturer',
        on_delete=models.CASCADE,
    )
```

这种被称为懒惰关系的引用，在解决两个应用程序之间的循环导入依赖关系时很有用。

在 `ForeignKey` 上会自动创建一个数据库索引。你可以通过设置 [`db_index`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.Field.db_index) 为 `False` 来禁用它。 如果你创建外键是为了保持一致性，而不是为了连接，或者你将创建一个替代性的索引，如部分索引或多列索引，你可能希望避免索引的开销。



#### 数据库表现[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#database-representation)

在幕后，Django 在字段名后附加 `"_id"` 来创建数据库列名。在上面的例子中，`Car` 模型的数据库表将有一个 `manufacturer_id` 列。（你可以通过指定 [`db_column`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.Field.db_column) 来显式地改变这一点）然而，你的代码应该永远不需要处理数据库列名，除非你编写自定义 SQL。你将总是处理你的模型对象的字段名。



#### 参数[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#arguments)

[`ForeignKey`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey) 接受其他定义关系工作细节的参数。

- `ForeignKey.``on_delete`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey.on_delete)

  当一个由 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey) 引用的对象被删除时，Django 将模拟 [`on_delete`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey.on_delete) 参数所指定的 SQL 约束的行为。例如，如果你有一个可空的 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey)，并且你希望当被引用的对象被删除时，它被设置为空：`user = models.ForeignKey(    User,    models.SET_NULL,    blank=True,    null=True, ) ``on_delete` 不会在数据库中创建 SQL 约束。支持数据库级联选项 [可能会在以后实施](https://code.djangoproject.com/ticket/21961)。

[`on_delete`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey.on_delete) 的可能值可以在 [`django.db.models`](https://docs.djangoproject.com/zh-hans/4.0/topics/db/models/#module-django.db.models) 中找到。

- - `CASCADE`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.CASCADE)

    级联删除。Django 模拟了 SQL 约束 ON DELETE CASCADE 的行为，也删除了包含 ForeignKey 的对象。[`Model.delete()`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/instances/#django.db.models.Model.delete) 在相关的模型上没有被调用，但是 `pre_delete` 和 `post_delete` 信号是为所有被删除的对象发送的。

- - `PROTECT`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.PROTECT)

    通过引发 [`ProtectedError`](https://docs.djangoproject.com/zh-hans/4.0/ref/exceptions/#django.db.models.ProtectedError)，即 [`django.db.IntegrityError`](https://docs.djangoproject.com/zh-hans/4.0/ref/exceptions/#django.db.IntegrityError) 的子类，防止删除被引用对象。

- - `RESTRICT`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.RESTRICT)

    通过引发 [`RestrictedError`](https://docs.djangoproject.com/zh-hans/4.0/ref/exceptions/#django.db.models.RestrictedError) （ [`django.db.IntegrityError`](https://docs.djangoproject.com/zh-hans/4.0/ref/exceptions/#django.db.IntegrityError) 的一个子类）来防止删除被引用的对象。与 [`PROTECT`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.PROTECT) 不同的是，如果被引用的对象也引用了一个在同一操作中被删除的不同对象，但通过 [`CASCADE`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.CASCADE) 关系，则允许删除被引用的对象。思考下下面这组模型：

    ```python
    class Artist(models.Model):    
        name = models.CharField(max_length=10) 
        
    class Album(models.Model):    
        artist = models.ForeignKey(Artist, on_delete=models.CASCADE) 
        
    class Song(models.Model):    
        artist = models.ForeignKey(Artist, on_delete=models.CASCADE)    
        album = models.ForeignKey(Album, on_delete=models.RESTRICT) 
    ```

     `Artist` 可以被删除，即使这意味着删除被 `Song` 引用的 `Album`，因为 `Song` 也通过级联关系引用 `Artist` 本身。例如：

    ```
    >>> artist_one = Artist.objects.create(name='artist one') 
    >>> artist_two = Artist.objects.create(name='artist two') 
    >>> album_one = Album.objects.create(artist=artist_one) 
    >>> album_two = Album.objects.create(artist=artist_two) 
    >>> song_one = Song.objects.create(artist=artist_one, album=album_one) 
    >>> song_two = Song.objects.create(artist=artist_one, album=album_two) 
    >>> album_one.delete() # Raises RestrictedError. 
    >>> artist_two.delete() # Raises RestrictedError. 
    >>> artist_one.delete() (4, {'Song': 2, 'Album': 1, 'Artist': 1})
    ```

    

- - `SET_NULL`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.SET_NULL)

    设置 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey) 为空；只有当 [`null`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.Field.null) 为 `True` 时，才有可能。

- - `SET_DEFAULT`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.SET_DEFAULT)

    将 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey) 设置为默认值，必须为 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey) 设置一个默认值。

- - `SET`()[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.SET)

    将 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.ForeignKey) 设置为传递给 [`SET()`](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.SET) 的值，如果传递了一个可调用的值，则为调用它的结果。在大多数情况下，为了避免在导入 models.py 时执行查询，传递一个可调用对象是必要的：

    ```python
    from django.conf import settings 
    from django.contrib.auth import get_user_model 
    from django.db import models 
    
    def get_sentinel_user():    
    	return get_user_model().objects.get_or_create(username='deleted')[0] 
    class MyModel(models.Model):    
        user = models.ForeignKey(        
            settings.AUTH_USER_MODEL,        
            on_delete=models.SET(get_sentinel_user),    
        )
    ```

    

- - `DO_NOTHING`[¶](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#django.db.models.DO_NOTHING)

    不采取任何行动。如果你的数据库后端强制执行引用完整性，这将导致一个 [`IntegrityError`](https://docs.djangoproject.com/zh-hans/4.0/ref/exceptions/#django.db.IntegrityError) 除非你手动添加一个 SQL `ON DELETE` 约束条件到数据库字段。