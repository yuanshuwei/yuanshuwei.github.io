---
title: Python入门-基础语法笔记
date: 2018-01-12 16:38:17
tags: python
categories: Python
---
python基础语法，比较详细, 查阅和初学
<!--more-->

### 1.vi技巧：  
中英切换：shift  
wq = x  
### 2.注释  
单行：#  
多行：三个单引号或三个双引号  
```python
"""
print("hello world")  
""" 
```
### 3.编码  
文件中有中文，不管是否为注释，python2执行报错。  
解决：程序首行加 #coding=utf-8 或 #\*-\* coding:utf-8 \*-\*  
### 4.输入  
```python
name = input("请输入名字：") 
```
### 5.输出  

```python
print("name is %s"%name)   
print("name:%s, age:%d"%(name,age))  
print("name:{0}, age:{1}".format(name,age)) #(标号可不写，为默认顺序) 
print("name:{name}, age:{age}".format(name = "ss",age = 23)) 

import math
print('常量 PI 的值近似为 {0:.3f}。'.format(math.pi)) #常量 PI 的值近似为 3.142。

table = {'Google': 1, 'Runoob': 2, 'Taobao': 3}
print('Runoob: {0[Runoob]:d}; Google: {0[Google]:d}; Taobao: {0[Taobao]:d}'.format(table))
#Runoob: 2; Google: 1; Taobao: 3

table = {'Google': 1, 'Runoob': 2, 'Taobao': 3}
print('Runoob: {Runoob:d}; Google: {Google:d}; Taobao: {Taobao:d}'.format(**table))
#Runoob: 2; Google: 1; Taobao: 3
```
### 6.类型转换 

```python
a = int("3") 
```
### 7.python2与python3  
- 编码。
- python2中input的内容当做执行的内容，python3当做字符串。如a = input(""). python2中的raw_input相当于python3中的input。  
- python2 中支持 <> 是不等于的意思，python3不支持，应该 != 表示  

### 8.运算符 

```python
"a" * 5 = "aaaaa"
2 ** 3 = 8
5 / 2 = 2.5
5 // 2 = 2 #(取商)
5 % 2 = 1 #（取余）
```

### 9.逻辑运算符  
and , or  
### 10.流程

```python
if 1 > 2:
    print("1")
elif 2 == 3:
    print("2")
else:
    print("3")
 

while 条件:  
    执行

for i int str:  
 执行
 
注: i++,++i在python中不允许使用。    
```

### 11.字符串(不可变)  

- 格式化

```python
>>>'a: %s, b:%s' %s ('a', 'b')
'a: a, b: b'

>>> '{}, {} and {}'.format("first", "second", "third")
'first, second and third'

>>> '{3} {0} {2} {1} {3} {0}'.format('be', 'not', 'or', 'to')
'to be or not to be'

>>> "{name} is approximately {value:.2f}.".format(value=pi,name="π")
'π is approximately 3.14.'

#python3 中，同名可省略
>>> from math import e
>>> f"Euler's constant is roughly {e}."

>>> f"Euler's constant is roughly {e}.".format(e=e)

#格式
>>> 'the number is {num:f}'.format(num=2)
'the number is 2.000000'
```
- 类型转换：

```python
int("100"), str(100)
```
- 长度：

```python
len(str)
```
- 字符串连接:

```python
a = b + c  #或者
a = "===%s==="%(b+c)  #或者
a = "==={}===".format(b+c)
```
- 切片：

```python
str = "dasfaf"
str[2:4] #(取得2到3的),  
str[2:]  #(到最后),  
str[2:-1:2] #(步长2，隔一个取一个)
```
- 逆序:

```python
str = "abcdefABCDEF"  
str[0:]  # out:"abcdefABCDEF"  
str[-1:]  # out:"F"  
str[-1:0]  # out:""  
str[-1:0:-1]  # out:"FEDCBAfedcb"  
str[-1::-1], str[::-1]  # out:"FEDCBAfedcba"
```

- 常见操作

```python
find: str.find("abc") # 从左向右有返回第一个匹配字符串的起始下标，没有返回-1。rfind():从右向左。  
str.index("abc") #找到返回起始下标，没有抛出异常。 存在rindex().  
str.count("abc") #匹配的个数。

str.replace("abc", "def") #把左边的替换成右边的同java，  
str.replace("abc", "def",1) #第三个参数是从左到右替换个数。  

str.split(" ") #字符串分割   
str.capitalize() #字符串的第一个字母大写。  
str.title() #字符串的每个单词的首字母大写。  
str.startswith("abc"), str.endswith("abc") #是否以此字符串开头或结尾.  
str.lower() str.uper() #所有的字母小写和大写。  
str.center(50) #居中显示，行总长50 "     abc     " 
str.ljust(50), str.rjust(50) #左(右)对齐

str.lstrip() #删除左边空格
str.rstrip() #右边空格
str.strip() #删除两端空格.

#center居中
>>> 'hello world'.center(20)
'    hello world     '
>>> 'hello world'.center(20, '*')
'****hello world*****'
```

### 12.列表  (类似数组，可变，针对自身的变化)  

```python
["zhangsan","lisi"]
#定义
names = ["zhangsan", "lisi", 3.14]  #列表中的数据可以是不同的数据类型。  可以下标，和切片。
#增
names.append("abc") #-->插入到最后;  
names.insert(0, "bcc") #-->插入指定位置。  

names = names1 + names2 #两个列表用连接符
names1.extend(names2) #扩充

#删
names.pop() #-->删除最后一个；  
names.remove("lisi") #-->根据内容删除;  
del names[0] #-->下标删除  
#改
names[0] = "abc"  
#查
name[1:] # "lisi"
in, not in #是否存在 （if "zhangsan" in names:）  
#可以for... in 循环遍历

len(names) #元素个数

#注意：append添加的元素；extend连接的列表

# 其他
''.join(list) # 列表转字符串
names.sort() #排序，改变原结构; names.sort(reverse=True) 反向顺序
```

运算符

|表达式|结果|描述|
|-|-|-|
|len([1, 2, 3])|3|计算元素个数|
|[1, 2, 3] + [4, 5, 6]|[1, 2, 3, 4, 5, 6]|连接|
|['Hi!'] * 4|['Hi!', 'Hi!', 'Hi!', 'Hi!']|复制|
|3 in [1, 2, 3]|True|元素是否存在|
|for x in [1, 2, 3]: print(x, end=" ")|1 2 3|迭代|

```python
params = {"server":"mpilgrim", "database":"master", "uid":"sa", "pwd":"secret"}
["%s=%s" % (k, v) for k, v in params.items()]
['server=mpilgrim', 'uid=sa', 'database=master', 'pwd=secret']
";".join(["%s=%s" % (k, v) for k, v in params.items()])
'server=mpilgrim;uid=sa;database=master;pwd=secret'
```

### 13.字典（可变）  

```python
a = {"name":"yy", "age": 12}

#增
a["name"] = "yy" #直接写key-value  
#删
del a["name"]
#改
a["name"] = "zz" #相同key的值覆盖。
#查：
a["name"], a.get("name")
```

for-else

```python
#for中没有break,则else一定会执行
for temp in strs:  
   print(temp)    
else:  
   print("")
```

const 修改变量为不可变。  
字典常见操作

```python
len(a) #键值对的个数

a.keys() #["name","age"] (pyhton2) #dict_keys(["name","age"])(pyhton3)返回key的列表
if "abc" in a.keys(): #判断是否存在某个key
    print("")
a.values() #返回value的列表

```

### 14.元组(类似列表，不可变)  

列表可以增删改，元组不能改

```python
tup1 = (); #空元组
```

```python
>>> tup1 = (50)
>>> type(tup1)     # 不加逗号，类型为整型
<class 'int'>

>>> tup1 = (50,)
>>> type(tup1)     # 加上逗号，类型为元组
<class 'tuple'>
```

访问：

```python
tup1 = ('Google', 'Runoob', 1997, 2000)
tup2 = (1, 2, 3, 4, 5, 6, 7 )

print ("tup1[0]: ", tup1[0]) # Google
print ("tup2[1:5]: ", tup2[1:5]) #(2, 3, 4, 5)
```

修改：

```python
tup1 = (12, 34.56);
tup2 = ('abc', 'xyz')

# 以下修改元组元素操作是非法的。
# tup1[0] = 100

# 创建一个新的元组
tup3 = tup1 + tup2;
print (tup3) #(12, 34.56, 'abc', 'xyz')
```

删除

```python
tup = ('Google', 'Runoob', 1997, 2000)

print (tup)
del tup;
```

运算符

|表达式|结果|描述|
|-|-|-|
|len((1, 2, 3))|3|计算元素个数|
|(1, 2, 3) + (4, 5, 6)|(1, 2, 3, 4, 5, 6)|连接|
|('Hi!') * 4|('Hi!', 'Hi!', 'Hi!', 'Hi!')|复制|
|3 in (1, 2, 3)|True|元素是否存在|
|for x in (1, 2, 3): print x,|1 2 3|迭代|
类似拆包

```python
a = (11,12)
b = a
b #out (11,12)
c,d = a #类似拆包
c #out 11
d #out 12

#例如
info = {"name":"ysw", "age":24}
for temp in info:
    print(temp)
#("name":"ysw")
#("age":24)
for temp in info.items():
    print("key=%s,value=%s"%(temp[0],temp[1]))
#or
for a,b in info.items():
    print("key=%s,value=%s"%(a,b))
```

**遍历技巧**：

```python
#在字典中遍历时，关键字和对应的值可以使用 items() 方法同时解读出来
knights = {'gallahad': 'the pure', 'robin': 'the brave'}
for k, v in knights.items():
          print(k, v)
          
  #gallahad the pure 
  #robin the brave

#在序列中遍历时，索引位置和对应值可以使用 enumerate() 函数同时得到
>>> for i, v in enumerate(['tic', 'tac', 'toe']):
...     print(i, v)
...
0 tic
1 tac
2 toe

#同时遍历两个或更多的序列，可以使用 zip() 组合
>>> questions = ['name', 'quest', 'favorite color']
>>> answers = ['lancelot', 'the holy grail', 'blue']
>>> for q, a in zip(questions, answers):
...     print('What is your {0}?  It is {1}.'.format(q, a))
...
What is your name?  It is lancelot.
What is your quest?  It is the holy grail.
What is your favorite color?  It is blue.

#要反向遍历一个序列，首先指定这个序列，然后调用 reversed() 函数
>>> for i in reversed(range(1, 10, 2)):
...     print(i)
...
9
7
5
3
1

#要按顺序遍历一个序列，使用 sorted() 函数返回一个已排序的序列，并不修改原值
>>> basket = ['apple', 'orange', 'apple', 'pear', 'orange', 'banana']
>>> for f in sorted(set(basket)):
...     print(f)
...
apple
banana
orange
pear
```

### 15.函数  

```python
def abc():
    print("")
    
abc()
#注意： 函数的定义需要在函数的调用之前，否则报错。
```

可更改与不可更改对象：  
在python中，strings,tuples和numbers是不可更改对象，list.dict等则是可更改对象。
- 不可变类型：变量赋值 a=5后在赋值a=10,生成了新的对象，原对象丢弃。  
- 可变类型：变量赋值 la=[1,2,3,4] 后再赋值 la[2]=5 则是将 list la 的第三个元素值更改，本身la没有动，只是其内部的一部分值被修改了。

python 函数的参数传递：  
- 不可变类型:类似 c++ 的值传递，如 整数、字符串、元组。如fun（a），传递的只是a的值，没有影响a对象本身。比如在 fun（a）内部修改 a 的值，只是修改另一个复制的对象，不会影响 a 本身
- 可变类型：类似 c++ 的引用传递，如 列表，字典。如 fun（la），则是将 la 真正的传过去，修改后fun外部的la也会受影响  

```python
#默认函数参数

def printinfo( name, age = 35 ):
   "打印任何传入的字符串"
   print ("名字: ", name);
   print ("年龄: ", age);
   return;
   
printinfo( age=50, name="runoob" );
print ("------------------------")
printinfo( name="runoob" );

#名字:  runoob
#年龄:  50
#------------------------
#名字:  runoob
#年龄:  35

#可变参数
def printinfo( *vartuple ):
   "打印任何传入的参数"
   print ("输出: ")
   for var in vartuple:
      print (var)
   return;

printinfo(10,32,22)

#匿名函数(lambda创建匿名函数)

sum = lambda arg1, arg2: arg1 + arg2;
 
print ("相加后的值为 : ", sum( 10, 20 ))
print ("相加后的值为 : ", sum( 20, 20 ))

#global关键字修改外部作用域变量
num = 1
def fun1():
    global num  # 需要使用 global 关键字声明
    print(num) 
    num = 123
    print(num)
fun1()

#1
#123

#nonlocal 修改嵌套作用域变量
def outer():
    num = 10
    def inner():
        nonlocal num   # nonlocal关键字声明
        num = 100
        print(num)
    inner()
    print(num)
outer()

#100
#100
```

### 16.迭代器和生成器  
字符串，列表和元组对象都可用于创建迭代器。  
```python
list = [1,2,3,4]
it = iter(list)
print(next(it)) #1
print(next(it)) #2

#遍历
#1
for x in it:
    print(x, end = "") # 1 2 3 4
    
#2
while True:
    try:
        print(next(it))
    except StopIteration:
        sys.exit()
```
生成器(TODO: 待理解)  
yield的函数，生成器是返回迭代器的函数，只能用于迭代操作。  
```python
import sys
 
def fibonacci(n): # 生成器函数 - 斐波那契
    a, b, counter = 0, 1, 0
    while True:
        if (counter > n): 
            return
        yield a
        a, b = b, a + b
        counter += 1
f = fibonacci(10) # f 是一个迭代器，由生成器返回生成
 
while True:
    try:
        print (next(f), end=" ")
    except StopIteration:
        sys.exit()
# 0 1 1 2 3 5 8 13 21 34 55
```
### 17.模块  
为此 Python 提供了一个办法，把这些定义存放在文件中，为一些脚本或者交互式的解释器实例使用，这个文件被称为模块。  
- import语句，想使用 Python 源文件，只需在另一个源文件里执行 import 语句。

```python
#!/usr/bin/python3
# Filename: support.py

def print_func( par ):
    print ("Hello : ", par)
    return
```
```python
#!/usr/bin/python3
# Filename: test.py
 
# 导入模块
import support #文件名
 
# 现在可以调用模块里包含的函数了
support.print_func("Runoob")
```

- from...import语句,从模块中导入一个指定的部分到当前命名空间中。

```python
#导入模块 fibo 的 fib 函数
from fibo import fib, fib2
>>> fib(500)
```

- From...import* 语句，把一个模块的所有内容全都导入到当前的命名空间。
- 模块除了方法定义，还可以包括可执行的代码。这些代码一般用来初始化这个模块。这些代码只有在第一次被导入时才会被执行。
- _name_属性,一个模块被另一个程序第一次引入时，其主程序将运行。如果我们想在模块被引入时，模块中的某一程序块不执行,可以用__name__属性。

```python
#!/usr/bin/python3
# Filename: using_name.py

if __name__ == '__main__':
   print('程序自身在运行')
else:
   print('我来自另一模块')
```

- Python 会根据 sys.path 中的目录来寻找这个包中包含的子目录。
- 目录只有包含一个叫做 __init__.py 的文件才会被认作是一个包，主要是为了避免一些滥俗的名字（比如叫做 string）不小心的影响搜索路径中的有效模块。
- 推荐：from Package import specific_submodule

### 18.文件  
open()方法返回文件，第二个参数为文件打开方式。默认只读r。w写，a追加...

```python
f = open("/tmp/test.txt","w")

f.write("人生苦短，我用python！")

f.close()
```

- f.read(size) 读取文件内容，size为空或负数则全部返回。
- f.readline() f.readline() 会从文件中读取单独的一行。换行符为 '\n'。f.readline() 如果返回一个空字符串, 说明已经已经读取到最后一行。
- f.readlines() 读取文件所有行，并以列表返回。
- f.write(string) 将 string 写入到文件中, 然后返回写入的字符数。如果要写入一些不是字符串的东西, 那么将需要先进行转换。

### 19.类  
- 类方法

```python
class MyClass:
    i = 12345
    #类方法必须有一个额外的第一个参数，惯例是self，不固定；代表的的类的实例而非类
    def f(self):
        return "hello world"

x = MyClass()
print("MyClass 类的属性 i 为：", x.i)
print("MyClass 类的方法 f 输出为：", x.f())
```

- 构造方法

```python
class Complex:
    #构造方法
    def __init__(self, realpart, imagpart):
        self.r = realpart
        self.i = imagpart
x = Complex(3.0, -4.5)
print(x.r, x.i)   # 输出结果：3.0 -4.5
```

```python
#类定义
class people:
    #定义基本属性
    name = ''
    age = 0
    #定义私有属性,私有属性在类外部无法直接进行访问
    __weight = 0
    #定义构造方法
    def __init__(self,n,a,w):
        self.name = n
        self.age = a
        self.__weight = w
    def speak(self):
        print("%s 说: 我 %d 岁。" %(self.name,self.age))
 
# 实例化类
p = people('runoob',10,30)
p.speak()
```

- 继承

```python
#类定义
class people:
    #定义基本属性
    name = ''
    age = 0
    #定义私有属性,私有属性在类外部无法直接进行访问
    __weight = 0
    #定义构造方法
    def __init__(self,n,a,w):
        self.name = n
        self.age = a
        self.__weight = w
    def speak(self):
        print("%s 说: 我 %d 岁。" %(self.name,self.age))
 
#单继承示例
class student(people):
    grade = ''
    def __init__(self,n,a,w,g):
        #调用父类的构函
        people.__init__(self,n,a,w)
        self.grade = g
    #覆写父类的方法
    def speak(self):
        print("%s 说: 我 %d 岁了，我在读 %d 年级"%(self.name,self.age,self.grade))
 
#另一个类，多重继承之前的准备
class speaker():
    topic = ''
    name = ''
    def __init__(self,n,t):
        self.name = n
        self.topic = t
    def speak(self):
        print("我叫 %s，我是一个演说家，我演讲的主题是 %s"%(self.name,self.topic))
 
#多重继承
class sample(speaker,student):
    a =''
    def __init__(self,n,a,w,g,t):
        student.__init__(self,n,a,w,g)
        speaker.__init__(self,n,t)
 
test = sample("Tim",25,80,4,"Python")
test.speak()   #方法名同，默认调用的是在括号中排前地父类的方法
```

### 20.正则表达式  
- re.match与re.search的区别  
re.match只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None；而re.search匹配整个字符串，直到找到一个匹配。

```python
import re

line = "Cats are smarter than dogs";

matchObj = re.match( r'dogs', line, re.M|re.I)
if matchObj:
   print ("match --> matchObj.group() : ", matchObj.group())
else:
   print ("No match!!")

matchObj = re.search( r'dogs', line, re.M|re.I)
if matchObj:
   print ("search --> matchObj.group() : ", matchObj.group())
else:
   print ("No match!!")
   
#No match!!
#search --> matchObj.group() :  dogs
```

- 检索和替换

```python
import re

phone = "2004-959-559 # 这是一个电话号码"

# 删除注释
num = re.sub(r'#.*$', "", phone)
print ("电话号码 : ", num)

# 移除非数字的内容
num = re.sub(r'\D', "", phone)
print ("电话号码 : ", num)

#电话号码 :  2004-959-559 
#电话号码 :  2004959559
```