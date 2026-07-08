## 偏函数

`functools.partial`函数：固定函数的参数，返回一个新的函数，默认从左到右

```python
from functools import partial

def mod(x, y):
    return x % y
mod_by_2 = partial(mod, 2) # 固定第1个参数（x）为 2
print(mod_by_2(10)) # 2 % 10 = 2

mod_2 = partial(mod, y=2) # 固定第2个参数（y）为 2
print(mod_2(10)) # 0
```

如可以通过设置一个通用的幂函数，来计算任意数的任意次幂

```python
def power(x, exp: int):
  return x**exp
square = partial(power, exp=2)
cube = partial(power, exp=3)
print(square(3)) # 9
print(cube(3)) # 27
```


## yield表达式

首先，可迭代对象、迭代器、生成器的概念
> 可迭代对象，是其内部实现了，__iter__ 这个魔术方法。  
> 迭代器，内部多实现了一个__next__()方法，可以不再使用for循环来间断获取元素值。而可以直接使用next()方法来实现。  
> 生成器，在迭代器的基础上，再实现了yield。 

```python
from collections.abc import Iterable

def flatten(items, ignore_types=(str, bytes)):
  for x in items:
    if isinstance(x, Iterable) and not isinstance(x, ignore_types):
      yield from flatten(x)
    else:
      yield x
items = [i for i in range(10)]
# 以下不同
tmp=flatten(items)
print(next(flatten(items)))
print(next(flatten(items)))
print(next(flatten(items)))
print(next(flatten(items)))

tmp=flatten(items)
print(next(tmp))
print(next(tmp))
print(next(tmp))
print(next(tmp))
```

> - 0 0 0 0
> - 0 1 2 3

yield表达式，实际执行是”时序”型的，一个“时钟”就是一个next(or send),每走一步，return之后，等待,等别人通过next(or send)，叫醒自己继续走.

当读取一个巨大的文本文件，如超过10w行数据，可以每次读取一行，而不是一次读取所有数据，这样可以节省内存空间。

每次的yield，都会暂停当前函数的执行，yield如果后面跟了一个值，则会返回这个值，如果没有跟值，则会返回None。
这个值的获取方法：next()方法，具体如上代码所示。

yield支持通过send()方法，来向生成器发送值。
如：
```python
def test():
  x = yield 1
  print('收到了外部的值', x)
  yield x*2

tmp = test()
print(next(tmp))   # 1
print(tmp.send(2)) # 4
```

> 1  
> 收到了外部的值 2  
> 4


### yield from
```python
# 字符串
astr='ABC'
# 列表
alist=[1,2,3]
# 字典
adict={"name":"wangbm","age":18}
# 生成器
agen=(i for i in range(4,8))

def gen(*args, **kw):
    for item in args:
        # yield 方法
        for i in item:
            yield i
        # yield from方法
        # yield from item

new_list=gen(astr, alist, adict， agen)
print(list(new_list))
# ['A', 'B', 'C', 1, 2, 3, 'name', 'age', 4, 5, 6, 7]
# 也可以这样生成一个生成器
f = gen(astr, alist, adict， agen)
# 那每一次调用next(f)，就会返回一个元素
print(next(f))
```

简单来说，`yield from`等价于for循环
```python
for i in item:
    yield i
```
即，省去了显式的迭代逻辑

