## OrderedDict — 记录键添加顺序的字典

<b>OrderedDict</b>是一个能记录内容添加顺序的字典类型的子类。

```python
# collections_ordereddict_iter.py

import collections

print('Regular dictionary:')
d = {}
d['a'] = 'A'
d['b'] = 'B'
d['c'] = 'C'

for k, v in d.items():
    print(k, v)

print('\nOrderedDict:')
d = collections.OrderedDict()
d['a'] = 'A'
d['b'] = 'B'
d['c'] = 'C'

for k, v in d.items():
    print(k, v)
```

普通的<b>dict</b>并不会记录插入操作的顺序。遍历其内容时，得到的顺序与其如何在哈希表中保存键值有关。为了减少哈希碰撞，生成的随机数会影响哈希表的结构。不同的是，在<b>OrderedDict</b>中，元素的插入顺序会被记录下来，并用来生成遍历器。

```
$ python3 collections_ordereddict_iter.py

Regular dictionary:
c C
b B
a A

OrderedDict:
a A
b B
c C
```

### 等值判断

普通的<b>dict</b>在比较是否相等时，只考虑元素内容是否一致。<b>OrderedDict</b>除了比较内容是否相同时，还会对比两者的插入顺序是否一致。

```python
# collections_ordereddict_equality.py

import collections

print('dict       :', end=' ')
d1 = {}
d1['a'] = 'A'
d1['b'] = 'B'
d1['c'] = 'C'

d2 = {}
d2['c'] = 'C'
d2['b'] = 'B'
d2['a'] = 'A'

print(d1 == d2)

print('OrderedDict:', end=' ')

d1 = collections.OrderedDict()
d1['a'] = 'A'
d1['b'] = 'B'
d1['c'] = 'C'

d2 = collections.OrderedDict()
d2['c'] = 'C'
d2['b'] = 'B'
d2['a'] = 'A'

print(d1 == d2)
```

在上面的例子中，由于两个<b>OrderedDict</b>元素插入顺序不同，即使他们的内容相同，也不会认为两个对象相等。

```
$ python3 collections_ordereddict_equality.py

dict       : True
OrderedDict: False
```

### 排序

可以使用<b>OrderedDict</b>的<b>move_to_end()</b>方法将元素移到序列的头部或尾部。

```python
# collections_ordereddict_move_to_end.py

import collections

d = collections.OrderedDict(
    [('a', 'A'), ('b', 'B'), ('c', 'C')]
)

print('Before:')
for k, v in d.items():
    print(k, v)

d.move_to_end('b')

print('\nmove_to_end():')
for k, v in d.items():
    print(k, v)

d.move_to_end('b', last=False)

print('\nmove_to_end(last=False):')
for k, v in d.items():
    print(k, v)
```

<b>last</b>参数指明<b>move_to_end</b>方法将元素移到序列头部(为False)还是尾部(为True)。

```
$ python3 collections_ordereddict_move_to_end.py

Before:
a A
b B
c C

move_to_end():
a A
c C
b B

move_to_end(last=False):
b B
a A
c C
```

> 拓展<br/>
> [PYTHONHASHSEED](https://docs.python.org/3.5/using/cmdline.html#envvar-PYTHONHASHSEED) - 介绍对哈希算法影响字典键位置的随机数种子环境变量。<br/>
