# celery使用笔记

###安装celery
```python

$ pip install celery

```

###创建app
```python

$ vim tasks.py    //创建应用
from celery import Celery

app = Celery('tasks', backend='redis://127.0.0.1:6379/1', broker='redis://127.0.0.1:6379/0') //backend存储 broker中间件
@app.task
def add(x, y):
    return x + y
    
 $celery -A tasks worker --loglevel=info  //启动tasks应用
 
```
###调用app
```python

$ vim test_demo.py
from tasks import add

result = add.delay(4, 6)   //result.ready() 查看执行状态
print result

```

###从落地数据查询结果
```python
$ vim get_ret.py
import redis
import json
improt time

result = add.delay(4, 6)
sdd = 'celery-task-meta-' + str(result)
time.sleep(5)
r = redis.Redis(host='127.0.0.1', port=6379, db=1)
ret = r.get(sdd)
data = json.loads(ret)
print data['result']

```
