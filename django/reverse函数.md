# reverse函数

reverse函数的作用是用来进行URL反转的，接下来我们介绍reverse函数的几种用法

1. 之前我们都是通过url来访问视图函数。有时候我们知道这个视图函数，但是想反转回他的url。这时候就可以通过reverse来实现。示例代码如下：
```py
reverse("list")
> /list/
```
2. 如果有应用命名空间或者有实例命名空间，那么应该在反转的时候加上命名空间。示例代码如下：
```py
> reverse('book:list')
> /book/list/
```
3.  **如果这个url中需要传递参数，那么可以通过kwargs来传递参数。示例代码如下：**
```py
> reverse("front:articles",kwargs={"id":1})
> /front/articles/1
```
![image-20220728214514208](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220728214514208.png)

![image-20220728214535267](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220728214535267.png)

![image-20220728214558169](https://gitee.com/sinoeast/imgs/raw/master/img/image-20220728214558169.png)

4. 因为django中的reverse反转url的时候不区分GET请求和POST请求，因此不能在反转的时候添加查询字符串的参数。如果想要添加查询字符串的参数，只能手动的添加。示例代码如下：

```py
> login_url = reverse('login') + "?next=/"
```
接下来我们模仿知乎做个小案列，我们访问知乎首页https://www.zhihu.com/，如果你未登录，网站会重定向到https://www.zhihu.com/signin?next=%2F登录页面，接下来我们实现这个功能

```py
# urls.py
app_name = "front"
urlpatterns = [
    path('', views.index, name="index"),
    path('signIn/', views.login, name="login"),
]

# views.py
def index(request):
    username = request.GET.get('username')
    if username:
        return HttpResponse("front首页")
    else:
        return redirect(reverse('front:login')  + "?next=/")

def login(request):
    return HttpResponse('front登录页面')
```
接下来我们通过浏览器访问127.0.0.1/front/，页面会自动重定向到127.0.0.1:8000/front/signIn/?next=/，我们可以通过pycharm的控制台清楚的看到重定向的过程
```py
[14/May/2021 09:46:58] "GET /front/ HTTP/1.1" 302 0
[14/May/2021 09:46:58] "GET /front/signIn/?next=/ HTTP/1.1" 200 17
```