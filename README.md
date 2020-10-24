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

