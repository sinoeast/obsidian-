# Django 用户功能

## 1.[[定义用户模型类]]

模型类models.py继承自AbstractUser

```python
from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.


class User(AbstractUser):
    """自定义用户模型类"""
    mobile = models.CharField(max_length=11, unique=True, verbose_name='手机号')

    class Meta:
        db_table = 'tb_users'
        verbose_name = '用户'
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.username
```

## 2.[[指定用户模型类]]

setting.py

```python
# 指定本项目用户模型类
AUTH_USER_MODEL = 'users.User'
```
## 3.[[注册用户]]

```python
User.objects.create_user(username=username, password=password)
```

## 4.1. [[验证用户]]

```python
# 判断用户名密码是否正确
# authenticate 传递用户名和密码  
# 如果用户名和密码正确，则返回 User实例对象  
# 如果用户名和密码不正确，则返回 None
user = authenticate(username=username,password=passwrod)
```
## 4.2. [[request.user]]
```python
# 利用request.user可以获取用户实例对象 (登录后才可以用)
#  is_authenticated登陆用户返回 true# 未登陆用户返回 false# 
request.user.is_authenticated
```
## 5.1.[[状态保持（约实现request.session）]]
```python
# user是User实例对象
login(request, user)
```
## 5.2. [[多账号登录]]
```python
import re
if re.match('^1[3-9]\d{9}$', username):
    # 手机号
    User.USERNAME_FIELD = 'mobile'
else:
    # account 是用户名
    # 根据用户名从数据库获取 user 对象返回.
    User.USERNAME_FIELD = 'username'
```
## 6.[[判断用户是否登录]]
继承重写LoginRequiredMixin的handle_no_permission方法 返回json数据
```python
class LoginRequiredJsonMixin(LoginRequiredMixin):  
    def handle_no_permission(self):  
        return JsonResponse({'code':400,'errmsg':'用户未登录'})
```
```python
class CenterView(LoginRequiredJsonMixin,View):  
  
    def get(self,request):  
        # request.user 就是 已经登录的用户信息  
        # request.user 是来源于 中间件  
        # 系统会进行判断 如果我们确实是登录用户，则可以获取到 登录用户对应的 模型实例数据  
        # 如果我们确实不是登录用户，则request.user = AnonymousUser()  匿名用户  
        info_data = {  
            'username':request.user.username,  
            'email':request.user.email,  
            'mobile':request.user.mobile,  
            'email_active':request.user.email_active,  
        }  
  
        return JsonResponse({'code':0,'errmsg':'ok','info_data':info_data})
```
## 7. [[退出登录]]
```python
# 把request.user改为匿名用户并且把redis中session中的相关信息删除
logout(request)
```