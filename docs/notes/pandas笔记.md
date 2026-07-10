1. 将`Series`转化成`frame`

通过索引获取指定行数据，会得到一个`Series`对象。这个`Series`对象，可以直接保存为`CSV`文件，但是会得到一列数据。
可以使用`Series`的`to_frame().T`方法，将`Series`转换为`DataFrame`，再保存为`CSV`文件。

```python linenums="1"
import pandas as pd
df = pd.DataFrame({
    'cola': [10, 20, 30],
    'colb': [100, 200, 300],
    'colc': [1000, 2000, 3000]
})
# 取第2行，cola和colb两列
row = df.loc[2, ['cola', 'colb']]
```

=== "不使用`to_frame().T`"
    ```python linenums="1" hl_lines="1"
    row.to_csv('wrong.csv')
    # ,0
    # cola,30
    # colb,300
    ```

=== "使用`to_frame().T`"
    ```python linenums="1" hl_lines="1"
    row.to_frame().T.to_csv('right.csv', index=False)
    # cola,colb
    # 30,300
    ```


2. 通过df.loc/df.iloc访问数据

`df.loc[2, ['cola', 'colb']]`  
其中，2是索引，如果df被条件过滤，则可能不存在索引为2的行。  
!!! note
    也即，通过条件过滤得到新的frame，它的索引值还是原始df的索引。

`df.iloc[2, [0, 1]]`  
其中，2是第2行，即使df被条件过滤，第二行就是物理上的第二行，也就是一个列表的第二个元素，只要有第2行，就会返回第2行的数据。
