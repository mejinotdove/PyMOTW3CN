## ChainMap — 搜索多个字典对象

ChainMap类管理一系列的字典对象，并且按给定的顺序从中去查找键值。因为其能类似于栈一样，在发生数据更改时入栈、同时在数据被丢弃时出栈，所以ChainMap对象能作为一个优秀的“上下文”容器。

### 访问值

ChainMap使用与普通字典类相同的API来访问值。

```python
# collections_chainmap_read.py

import collections

a = {'a': 'A', 'c': 'C'}
b = {'b': 'B', 'c': 'D'}

m = collections.ChainMap(a, b)

print('Individual Values')
print('a = {}'.format(m['a']))
print('b = {}'.format(m['b']))
print('c = {}'.format(m['c']))
print()

print('Keys = {}'.format(list(m.keys())))
print('Values = {}'.format(list(m.values())))
print()

print('Items:')
for k, v in m.items():
    print('{} = {}'.format(k, v))
print()

print('"d" in m: {}'.format(('d' in m)))
```

由于搜索子字典的顺序是按照他们被传给构造方法的顺序而定的，所以获取键'c'的值是从字典对象a而来的。

```
$ python3 collections_chainmap_read.py

Individual Values
a = A
b = B
c = C

Keys = ['c', 'b', 'a']
Values = ['C', 'B', 'A']

Items:
c = C
b = B
a = A

"d" in m: False
```

### 插入值

ChainMap对象将所有的子字典对象都保存在一个名为maps的列表字段中。这个列表是可变的，所以可以直接向其添加新的子字典对象，或者改变其中子字典对象的顺序，从而控制查找和更新行为。

```python
# collections_chainmap_reorder.py

import collections

a = {'a': 'A', 'c': 'C'}
b = {'b': 'B', 'c': 'D'}

m = collections.ChainMap(a, b)

print(m.maps)
print('c = {}\n'.format(m['c']))

# reverse the list
m.maps = list(reversed(m.maps))

print(m.maps)
print('c = {}'.format(m['c']))
```

当子字典的顺序倒置之后，访问键'c'获得的值就改变了。

```
$ python3 collections_chainmap_reorder.py

[{'c': 'C', 'a': 'A'}, {'c': 'D', 'b': 'B'}]
c = C

[{'c': 'D', 'b': 'B'}, {'c': 'C', 'a': 'A'}]
c = D
```

### 更新值

ChainMap对象本身并不会缓存键值，所以如果子字典的内容被修改了，通过ChainMap对象访问子字典也能直接获取到最新值。

```python
# collections_chainmap_update_behind.py
import collections

a = {'a': 'A', 'c': 'C'}
b = {'b': 'B', 'c': 'D'}

m = collections.ChainMap(a, b)
print('Before: {}'.format(m['c']))
a['c'] = 'E'
print('After : {}'.format(m['c']))
```

修改已存在的键值和插入新键值都和普通字典对象无异。

```
$ python3 collections_chainmap_update_behind.py

Before: C
After : E
```

也可以通过ChainMap对象直接修改子字典的键值，不过只有子字典对象列表中的首个字典对象会被修改。

```python
# collections_chainmap_update_directly.py
import collections

a = {'a': 'A', 'c': 'C'}
b = {'b': 'B', 'c': 'D'}

m = collections.ChainMap(a, b)
print('Before:', m)
m['c'] = 'E'
print('After :', m)
print('a:', a)
```

当新键值通过ChainMap对象m进行保存时，子字典对象a被更新。

```
$ python3 collections_chainmap_update_directly.py

Before: ChainMap({'c': 'C', 'a': 'A'}, {'c': 'D', 'b': 'B'})
After : ChainMap({'c': 'E', 'a': 'A'}, {'c': 'D', 'b': 'B'})
a: {'c': 'E', 'a': 'A'}
```

ChainMap对象提供了一个方便的方法用以创建一个新的ChainMap对象。这个新对象通过创建一个空的字典对象，并存放在子字典列表的首位，来避免使用ChainMap进行修改时，影响原始数据集。

```python
# collections_chainmap_new_child.py
import collections

a = {'a': 'A', 'c': 'C'}
b = {'b': 'B', 'c': 'D'}

m1 = collections.ChainMap(a, b)
m2 = m1.new_child()

print('m1 before:', m1)
print('m2 before:', m2)

m2['c'] = 'E'

print('m1 after:', m1)
print('m2 after:', m2)
```

这种类似于栈的特性，在创建模板或者程序上下文时，使用ChainMap对象会十分方便。

```
$ python3 collections_chainmap_new_child.py

m1 before: ChainMap({'c': 'C', 'a': 'A'}, {'c': 'D', 'b': 'B'})
m2 before: ChainMap({}, {'c': 'C', 'a': 'A'}, {'c': 'D', 'b':
'B'})
m1 after: ChainMap({'c': 'C', 'a': 'A'}, {'c': 'D', 'b': 'B'})
m2 after: ChainMap({'c': 'E'}, {'c': 'C', 'a': 'A'}, {'c': 'D',
'b': 'B'})
```

当新的上下文是已知的，或者将会进一步被创建的情况下，将其传递给new_child()也是可以的。

```python
# collections_chainmap_new_child_explicit.py
import collections

a = {'a': 'A', 'c': 'C'}
b = {'b': 'B', 'c': 'D'}
c = {'c': 'E'}

m1 = collections.ChainMap(a, b)
m2 = m1.new_child(c)

print('m1["c"] = {}'.format(m1['c']))
print('m2["c"] = {}'.format(m2['c']))
```

这相当于

```python
m2 = collections.ChainMap(c, *m1.maps)
```

得到如下结果

```
$ python3 collections_chainmap_new_child_explicit.py

m1["c"] = C
m2["c"] = E
```
