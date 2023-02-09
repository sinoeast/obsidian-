# redis 管道 将redis命令缓存后一起执行提高性能

```python
	# 管道 3步  
	# ① 新建一个管道  
	pipeline=redis_cli.pipeline()  
	# ② 管道收集指令  
	pipeline.setex(mobile, 300, sms_code)  
	pipeline.setex(mobile_flag, 60, flag)  
	# ③ 管道执行指令  
	pipeline.execute() 
```
