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
15、执行字符串表达式
```  python
equation = '(1+2)/4'
print(eval(equation))  # 输出是 0.75
#保留分数表达
from sympy import Integer
import re
r = (Integer(1) + Integer(2)) / Integer(4)
print(r)  # 输出是 3/4 而不是 0.75
#转换为分数表达
new_equation = re.sub('(\d+)', 'Integer(\\1)', equation)
print(new_equation)  # 输出是 (Integer(1)+Integer(2))/Integer(4)
print(eval(new_equation))  # 输出是 3/4

``` 
16、读取json数据
```  python
data = []
for l in open(filename):
    data.append(json.loads(l))
# 格式化的json数据处理(多行组成一条json)
js = ""
data = []
f = open(filename, encoding="utf-8")
for i, s in enumerate(f):
    js += s.replace("\n", "").strip()
    i += 1
    if i % 6 == 0:  #每6行为一条json数据
        data.append(json.loads(js))
f.close()
        js = ""
     
``` 
17、pip代理
``` 
python -m pip --no-cache-dir install --upgrade --proxy="http://xxxx:8080" -i http://mirrors.aliyun.com/pypi/simple/  --trusted-host mirrors.aliyun.com"
``` 
18、wget代理
``` 
wget -e "https_proxy=http://xxxx:8080" https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz
``` 
19、解压与匹配
``` 
# 批量解压
gzip -d *.gz
# 批量匹配并将匹配行写入文件  
cat 2020-*.log|grep "TimeCount" -->alllog.log
``` 
20、进程、内存、目录大小查看
``` 
#查看进程
ps -ef|grep python
#查看内存消耗
ps -aux | grep predict
#查看当前目录大小
df -h --max-depth=1
``` 
21、keras按需分配显存
``` python
import keras.backend.tensorflow_backend as K
import tensorflow as tf
import os
 
os.environ["CUDA_VISIBLE_DEVICES"] = "1"
config = tf.ConfigProto()
config.gpu_options.allow_growth=True   #不全部占满显存, 按需分配
sess = tf.Session(config=config)
K.set_session(sess)
``` 
22、keras交叉验证时清除session
``` python
from keras import backend as K 

#del model
K.clear_session()
``` 
23、weget代理设置
``` 
#https
wget -e "https_proxy=http://xxxxx:8080" --proxy-user=user --proxy-passwd=passwd https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz
#http
wget -e "http_proxy=http://xxxxx:8080" --proxy-user=user --proxy-passwd=passwd http://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz
#ftp
wget -e "ftp_proxy=http://xxxxx:8080" --proxy-user=user --proxy-passwd=passwd ftp://gcc.gnu.org/pub/gcc/infrastructure/mpfr-2.4.2.tar.bz2
``` 
