使用 字段 abstract= True
```python
class BaseModel(models.Model):  
    """为模型类补充字段"""  
  
    create_time = models.DateTimeField(auto_now_add=True, verbose_name="创建时间")  
    update_time = models.DateTimeField(auto_now=True, verbose_name="更新时间")  
  
    class Meta:  
        abstract = True  # 说明是抽象模型类, 用于继承使用，数据库迁移时不会创建BaseModel的表
```
其他model继承它就可以获取字段

```python
class GoodsCategory(BaseModel):  
    """商品类别"""  
    name = models.CharField(max_length=10, verbose_name='名称')  
    parent = models.ForeignKey('self', related_name='subs', null=True, blank=True, on_delete=models.CASCADE,  
                               verbose_name='父类别')  
  
    class Meta:  
        db_table = 'tb_goods_category'  
        verbose_name = '商品类别'  
        verbose_name_plural = verbose_name  
  
    def __str__(self):  
        return self.name
```
