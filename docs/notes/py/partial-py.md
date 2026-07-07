# 偏函数

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
