# pycode_tools
1、基于pandas的映射
```  python
food = {'<=100':'1','100-500':'2','500-1000':'3','1000-3000':'4','>3000':'5'}
方式一：df['工作日消费指数'] = df['工作日消费金额'].replace(food)
方式二：df['工作日消费指数'] = df['工作日消费金额'].map(food)    （map中需要包含映射列的所有取值的映射关系）
```
2、三种方式交换键值对（前提：值唯一）
```  python
1.
mydict={"a":1,"b":2,"c":3}
mydict_new={}
for key,val in mydict.items():
    mydict_new[val]=key
2.
mydict={"a":1,"b":2,"c":3}
mydict_new=dict([val,key] for key,val in mydict.items())
3.利用zip运算符：
mydict={"a":1,"b":2,"c":3}
mydict_new=dict(zip(mydict.values(),mydict.keys()))
```
3、字典、列表(嵌套)、元组排序(嵌套)
```  python
普通排序：
a=sorted([5,2,3,4,1])    
print(a)   #[1,2,3,4,5]
倒叙
b=sorted((5,2,3,4,1), reverse=True) 
print(b)  #(5,4,3,2,1)
使用参数:key, 根据自定义规则排序
chars=['apple','banana','pear']
a=sorted(chars,key=lambda x:len(x))
print(a)  #['pear','apple','banana']

tuple_list=[('A',1,5),('B',3,4),('c',2,6)]
a=sorted(tuple_list,key=lambda x:x[1])
print(a)  #[('A',1,5),('c',2,6),('B',3,4)]
```
4、数据持久化
```  python
# 保存数据
with open('mode1_1.pickle', 'wb') as f:
    pickle.dump(mode1, f, -1)

# 恢复数据
with open(file_path, 'rb') as file:
    file_data = pickle.load(file)
```
5、显示进度条
```  python
from tqdm import tqdm
from time import sleep

for i in tqdm(range(10)):
    sleep(2)
```
6、多进程、多线程
```  python
from multiprocessing.dummy import Pool as Threadpool
from multiprocessing import Pool
import time

def fun(msg):
    time.sleep(1)
    return msg+1
 
arg = [i for i in range(10)]
start = time.time()
pool = Pool(processes=5)
# pool = Threadpool(processes=5) #多线程(只能用于IO类任务加速)
return_list=pool.map(fun, arg) #多进程
pool.close()
pool.join()
end = time.time()
print(end-start)
print(return_list)
```
7、忽略警告信息
```  python
import warnings
warnings.filterwarnings('ignore')
```
8、抛出代码异常所在行数
```  python
import logging
try:
    pass
except Exception as e:
    logging.error(
                'failed in file:{}, lineno:{}'.format(e.__traceback__.tb_frame.f_globals["__file__"],
                                                      e.__traceback__.tb_lineno))
```
9、计时器类
```  python
import time 
class Timer():
    def __init__(self, name='task', verbose=True):
        self.name = name
        self.verbose = verbose

    def __enter__(self):
        self.start = time.time()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.verbose:
            print('[Time] {} consumes {:.4f} s'.format(
                self.name,
                time.time() - self.start))
        return exc_type is None
#使用方法
with Timer('taks1'):
    time.sleep(2)
```
10、git相关
```
拉取指定分支：git clone -b feature-1 http://xxxx.git
本地分支合并到远程分支：git push --set-upstream origin feature-1
基本操作：
git status ./           查看这个文件夹下的文件状态，会列出有哪些没有加入追踪，哪些没有commit
git add ./*             把这个文件下的所有应该加入追踪的，加入到暂存区
git commit -m "日志描述" ./           把这个文件夹下可以commit的，都commit到本地库
git commit --amend      注释修改
git push                push到远程库
#github host添加
219.76.4.4 github-cloud.s3.amazonaws.com
192.30.253.118  gist.github.com
192.30.253.112  github.com
```
11、数组合并
```
import numpy as np
a = np.array([[1,2],[3,4],[5,6]])
b = np.array([[11,22],[33,44],[55,66]])
#直接将所有元素重新排列成新的一维数组
c = np.append(a,b)
print(c)
[1,2,3,4,5,6,11,22,33,44,55,66]

#纵向拼接(行增加)
np.concatenate((a,b),axis=0) 等价于 np.vstack((a,b))
#横向拼接(列增加)
np.concatenate((a,b),axis=1) 等价于 np.行stack((a,b))
```
12、获取主机hostname
```  python
import socket
print(socket.gethostname())
```
13、操作zookeeper
```  python
from kazoo.client import KazooClient
zk = KazooClient(hosts='192.168.91.128:2181,192.168.91.129:2181')    #如果是本地那就写127.0.0.1
zk.start()    #与zookeeper连接
zk.create('/abc/JQK/XYZ/0001',b'this is my house',makepath=True)    #创建节点
zk.get_children('/')     #查看某目录的子节点
zk.set('/abc/JQK/XYZ/0001',b"this is my horse!")     #更改节点值
zk.delete('/abc/JQK/XYZ/0001',recursive=True)      #删除节点
# zk锁使用
lock = self.zk_client.Lock('/abc/JQK/XYZ/lock', identifier='lock1')
with lock:
    print('do something in lock')
zk.stop()     #断开
```
14、操作kafka
```  python
from kafka import KafkaProducer,KafkaConsumer
import json
#生产者
producer = KafkaProducer(
                            value_serializer=lambda v: json.dumps(v).encode('utf-8'),
                            bootstrap_servers=['192.168.12.101:6667','192.168.12.102:6667','192.168.12.103:6667']
                         )
for i in range(10):
    data={
        "name":"李四",
        "age":23,
        "gender":"男",
        "id":i
    }
    producer.send('test_lyl2', data)
producer.close()

#消费者
consumer = KafkaConsumer('test_lyl2',group_id="lyl-gid1",
                         bootstrap_servers=['192.168.12.101:6667','192.168.12.102:6667','192.168.12.103:6667'],
                         auto_offset_reset='earliest',value_deserializer=json.loads
                         )
for message in consumer:
    print(message.value)
``` 

