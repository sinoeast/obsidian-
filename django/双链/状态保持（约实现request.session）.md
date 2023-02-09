# 状态保持

> 说明：
> 
> -   如果需求是注册成功后即表示用户认证通过，那么此时可以在注册成功后实现状态保持
> -   如果需求是注册成功后不表示用户认证通过，那么此时不用在注册成功后实现状态保持
> 
> **美多商城的需求是：注册成功后即表示用户认证通过**

### 1. login()方法介绍

1.  状态保持：
    -   将通过认证的用户的唯一标识信息（比如：用户ID）写入到当前session会话中
2.  login()方法：
    -   Django用户认证系统提供了 `login()` 方法
    -   封装了写入session的操作，帮助我们快速实现状态保持
3.  login()位置：
    
    -   `django.contrib.auth.__init__.py`文件中
        
        ```
        login(request, user)
        ```
        

### 2. login()方法使用

```
# 保存注册数据
try:
    user = User.objects.create_user(username=username, password=password, mobile=mobile)
except DatabaseError:
    return render(request, 'register.html', {'register_errmsg': '注册失败'})

# 实现状态保持
login(request, user)

# 响应注册结果
return redirect(reverse('contents:index'))
```

### 3. 查看状态保持结果

![](https://gitee.com/sinoeast/imgs/raw/master/img/20220804165942.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220804165955.png)


