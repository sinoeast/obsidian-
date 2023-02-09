方式一

```python
		redis_cli = get_redis_connection('code')  
        # 3.2 指令操作  设置环境有效时间100秒
        # name, time, value  
        redis_cli.setex(uuid,100,text) 
```
方式二

```python
cache.setex(uuid,100,text) 
```
方式三
[[redis 管道 将redis命令缓存后一起执行提高性能]]