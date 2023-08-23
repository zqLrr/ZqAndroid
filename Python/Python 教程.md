Python 教程

学习目的

1. 了解基本语法和使用特性
2. 自动化测试Case
3. FastBIOT
4. 可视化工具&&算法

1、配置环境

下载python和在VScode下载python插件

2、pip. python第三方包的管理工具

```bash
#python3 下载pip
curl https://bootstrap.pypa.io/get-pip.py | python3
#更新pip
pip install --upgrade pip
```

* 更新pip源

```bash
```

# 语法

### 函数

必选参数(也可称为位置参数)

> 必须传入的参数的值

定义默认参数

> 默认参数必须指向不变的对象

可变参数

> 在参数前添加*,可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。

```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
  
#使用结果  
>>> calc(1, 2)
5
>>> calc()
0
```

关键字参数

> 关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
    
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, city=extra['city'], job=extra['job'])
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'} 
```

命名关键字参数

> 为了限制关键字参数的key值

```python
def person(name, age, *, city, job):
    print(name, age, city, job)
#关键字参数**kw不同，命名关键字参数需要一个特殊分隔符*，*后面的参数被视为命名关键字参数。
```

```python
#如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了：
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

```python
#命名关键字参数 可以添加默认参数，简化调用
def person(name, age, *, city='Beijing', job):
    print(name, age, city, job)
```

参数组合

> 定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

> 最神奇的：对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的。

```python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
    
>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
```

