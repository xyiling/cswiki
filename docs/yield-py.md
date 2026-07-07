# yield表达式

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

> 0 0 0 0

> 0 1 2 3