python装饰器
===
## 作用
**尽量在不改变原来函数代码的基础上，给函数增加新的功能。**

假设我们现在有一个求斐波那契数列的递归实现，代码如下。
```python
def f(n):
    assert n > 0
    if n >= 3:
        return f(n-1) + f(n-2)
    else:
        return 1
```
上面的函数需要递归地进行，效率慢。如果我们能把之前的结果保存下来，再在求解过程中直接返回缓冲中的结果，可以提高求解的速度。
```python
from functools import wraps
# 定义缓冲装饰器
def cache(func):
    caches = {}

    @wraps(func)
    def wrap(n):
        if n not in caches:
            caches[n] = func(n)

        return caches[n]
    return wrap
    
# 使用装饰器，给函数增加缓存机制
@cache     
def f(n):
    assert n > 0
    if n >= 3:
        return f(n-1) + f(n-2)
    else:
        return 1
```
## 编写装饰器
### 1、无参数
```python
def log(func):
    def wrapper(*args, **kw):
        print "call %s():" % func.__name__
        return func(*args, **kw)
    return wrapper
    
@log
def now():
    print "2013-12-25"

#等价于
now = log(now)
```
### 2、带参数
```python
def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print "%s %s():" % (text, func.__name__)
            return func(*args, **kw)
        return wrapper
    return decorator
    
@log("execute")
def now():
    print "2013-12-25"

#等价于
now = log("execute")(now)
```
## 处理__name__属性
无参数now方法加了@log后,__name__属性会被修改，结果如下：
```python
>>> print now.__name__
"wrapper"
```
这会导致有些依赖函数签名的代码执行就会出错，所以调用python内置的functools.wraps，自动执行wrapper.__name__ = func.__name__，使__name__属性恢复‘now’。
```python
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print "call %s():" % func.__name__
        return func(*args, **kw)
    return wrapper
```
