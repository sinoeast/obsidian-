# celery
## 1.创建Celery实例并加载配置
> **1.定义Celery包**

![](https://gitee.com/sinoeast/imgs/raw/master/img/20220806153258.png)



> **2.创建Celery实例**

![](https://gitee.com/sinoeast/imgs/raw/master/img/20220806153308.png)


> celery_tasks.main.py

```python
# celery启动文件
from celery import Celery

# 为celery使用django配置文件进行设置
import os
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "meiduo_mall.settings")

# 创建celery实例（实例名称随意）
celery_app = Celery('celery_tasks')
```

> **3.加载Celery配置**

![](https://gitee.com/sinoeast/imgs/raw/master/img/20220806153355.png)


> celery_tasks.config.py

```python
broker_url = "redis://127.0.0.1/15"

###########################说明#############################

# 如果使用别的作为中间人, 例如使用 rabbitmq
# 则 rabbitmq 配置如下:
broker_url= 'amqp://用户名:密码@ip地址:5672'

# 例如: 
# meihao: 在rabbitq中创建的用户名, 注意: 远端链接时不能使用guest账户.
# 123456: 在rabbitq中用户名对应的密码
# ip部分: 指的是当前rabbitq所在的电脑ip
# 5672: 是规定的端口号
broker_url = 'amqp://meihao:123456@172.16.238.128:5672'
```

> celery_tasks.main.py

```python
# 加载celery配置
celery_app.config_from_object('celery_tasks.config')
```

### 3. 定义发送短信任务

![](https://gitee.com/sinoeast/imgs/raw/master/img/20220806153528.png)



> 1.定义任务：celery_tasks.sms.tasks.py**

[tasks](https://docs.celeryproject.org/en/latest/userguide/tasks.html)
文件名要是tasks.py
装饰器
执行的代码用函数包装
```python
from celery_tasks.main import celery_app
from libs.yuntongxun.sms import CCP
import logging
logger = logging.getLogger('django')

# name：异步任务别名
@celery_app.task(name='send_sms_code')
def send_sms_code(mobile, sms_code):
    """
    发送短信异步任务
    :param mobile: 手机号
    :param sms_code: 短信验证码
    """
    try:
        send_ret = CCP().send_template_sms(mobile, [sms_code, 5], 1)
    except Exception as e:
        logger.error(e)
```
> 2.注册任务：celery_tasks.main.py**

```python
celery_app.autodiscover_tasks(['celery_tasks.sms'])
```

### 4. 启动Celery服务

```shell
cd ~/meiduo_mall
celery -A celery_tasks.main worker -l info
```
如果是win10
要`pip install eventlet`
```shell
celery -A celery_tasks.main worker -l info -P eventlet
```
> -   `-A`指对应的应用程序, 其参数是项目中 Celery实例的位置。
> -   `worker`指这里要启动的worker。
> -   `-l`指日志等级，比如`info`等级。

![](https://gitee.com/sinoeast/imgs/raw/master/img/20220806154033.png)


### 5. 调用发送短信任务

```python
# 发送短信验证码
# CCP().send_template_sms(mobile,[sms_code, 5], 1)
# Celery异步发送短信验证码

send_sms_code.delay(mobile, sms_code)
```

![](https://gitee.com/sinoeast/imgs/raw/master/img/20220806154233.png)


### 6. 补充celery worker的工作模式

-   默认是进程池方式，进程数以当前机器的CPU核数为参考，每个CPU开四个进程。
-   如何自己指定进程数： `celery worker -A proj --concurrency=4`
-   如何改变进程池方式为协程方式： `celery worker -A proj --concurrency=1000 -P eventlet -c 1000`

```python
# 安装eventlet模块
pip install eventlet

# 启用 Eventlet 池
celery -A celery_tasks.main worker -l info -P eventlet -c 1000
```