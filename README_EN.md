[中文文档](README.md)  | [English Docs](README_EN.md)  
## Redis high concurrency queue
 
#### Functional description
* A distributed crawler framework that is more flexible than scrapy and easier to use than Celery. Do the most with the least code and the simplest way
* Can skillfully use the framework to crawl data within 1 minute, without learning complex documents. Easily expand various middleware

#### version description
* Supported version: Python 3.0+

#### Pip installation
```shell
pip install redis-queue-tool
```

#### DEMO description

##### 1. Publish tasks and consumer tasks
```python
from redis_queue_tool import task_deco

@task_deco('test1', qps=10, threads_num=10, fliter_rep=True)  # Add task queue decorator to consumer function
def f1(a, b):
    print(f"a:{a},b:{b}")


# Post tasks
for i in range(1, 51):
    f1.pub(i, i+1)  # or f1.publish_redispy(i, i+1)

# Consumption tasks
f1.start()  # or f1.start_consuming_message()
```

##### 2. Publish consumption multi-parameter type tasks
```python
from redis_queue_tool import RedisPublish, RedisCustomer

for zz in range(1, 501):
    # Write dictionary task {"a":zz,"b":zz,"c":zz}
    param = {"a": zz, "b": zz, "c": zz}
    RedisPublish(queue_name='test2').publish_redispy(param)


def print_msg_dict(a, b, c):
    print(f"msg_dict:{a},{b},{c}")


# Consumption multi-parameter type task queue_name consumption queue name qps consumption tasks per second (no limit by default)
RedisCustomer(queue_name='test2', consuming_function=print_msg_dict,
              qps=50).start_consuming_message()
```

##### 3. Batch submit task consumption

```python
from redis_queue_tool import RedisPublish, RedisCustomer
from gevent import monkey
monkey.patch_all()

# #### 3. Submit tasks in batches
result = [{'a': i,'b': i,'c': i} for i in range(1, 501)]
# Batch submit tasks queue_name Submit task queue name max_push_size Number of batch submission records per time (default 50)
RedisPublish(queue_name='test3', max_push_size=100).publish_redispy_list(result)
def print_msg_dict1(a, b, c):
    print(f"msg_dict1:{a},{b},{c}")
# Consumer type string support ('thread','gevent') default thread, if you use gevent, please add at the beginning of the code: from gevent import monkey monkey.patch_all()
RedisCustomer(queue_name='test3', consuming_function=print_msg_dict1, customer_type='gevent',
              qps=50).start_consuming_message()
```

##### 4. Switch the task queue middleware to sqlite (default is redis)

```python
from redis_queue_tool import RedisPublish, RedisCustomer

for zz in range(1, 101):
    RedisPublish(queue_name='test4', middleware='sqlite').publish_redispy(a=zz, b=zz, c=zz)

def print_msg_dict2(a, b, c):
    print(f"msg_dict:{a},{b},{c}")

RedisCustomer(queue_name='test4', consuming_function=print_msg_dict2, middleware='sqlite',
              qps=50).start_consuming_message()

```
##### 5. Minimal consumption model (strongly recommended)
```python
from redis_queue_tool import task_deco

@task_deco('test5') #Add task queue decorator to consumer function
def f(a, b):
     print(f" a: {a}, b: {b}")

#Post tasks
for i in range (1, 51):
     f.publish_redispy(a=i, b=i) # or f.pub(a=i, b=i)

#Consumption tasks
f.start_using_message() # or f.start()
```

#### Reids install
[reids install](https://www.runoob.com/redis/redis-install.html)

reids docker install
```shell
docker run  -d -p 6379:6379 redis
```

redis web manage tool [flask-redisboard](https://github.com/hjlarry/flask-redisboard)
![avatar](https://s1.ax1x.com/2020/07/07/UAIHFe.jpg)


#### Usage scenarios and features

```shell
1. Highly concurrent distributed crawler (verified by tens of millions of online data crawling verification)

2. Distributed data cleaning (automatic deduplication of cleaning, support to continue cleaning after interruption at any time)

3. Short video processing (video download and upload, bandwidth is sufficient without waiting)

4. Asynchronous real-time online query interface (speed reaches millisecond level)

5. Other usage scenarios are being expanded

```


#### Release Notes


```java
2020-06-11 Version 4.1.5 Added support for gevent coroutine consumption parameter customer_type='gevent'

2020-05-20 Added consumption function timeout time parameter

2020-05-10 Added sqlite middleware support

2020-04-13 The consumption function adds automatic control of the number of threads

2020-04-10 New frequency limiting parameter for consumption function

2020-01-08 Consumer function supports multiple parameter types

2019-12-06 Simplified multi-threaded consumer queue class

2019-10-14 Added consumption function error retry mechanism, retry by default 3 times

2019-10-12 Task deduplication ignores parameter order

2019-09-27 Fix the bug of submitting list task

2019-05-25 Added dynamic parameter transfer when adding tasks

2019-04-06 Added automatic deduplication function for crawling tasks

2019-03-23 ​​Added single-thread asynchronous batch submission function
```