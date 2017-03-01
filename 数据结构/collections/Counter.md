## Counter — 计数可哈希对象

Counter是一个用以跟踪相同值被添加次数的计数器。可以用来实现一些在其他语言中使用Bag或者MultiSet数据结构实现的算法。

### 初始化

Counter支持三种初始化形式。
* 使用一个序列对象
* 一个包含键和键的计数值的字典对象
* 使用关键字参数传递字符串型键和其计数值。

```python
# collections_counter_init.py
import collections

print(collections.Counter(['a', 'b', 'c', 'a', 'b', 'b']))
print(collections.Counter({'a': 2, 'b': 3, 'c': 1}))
print(collections.Counter(a=2, b=3, c=1))
```

使用三种初始化形式得到的结果是相同的。

```
$ python3 collections_counter_init.py

Counter({'b': 3, 'a': 2, 'c': 1})
Counter({'b': 3, 'a': 2, 'c': 1})
Counter({'b': 3, 'a': 2, 'c': 1})
```

使用无参构造方法可以生成一个空的Counter对象，并且使用Counter对象的<b>update()</b>方法可以向其填充数据。

```python
# collections_counter_update.py
import collections

c = collections.Counter()
print('Initial :', c)

c.update('abcdaab')
print('Sequence:', c)

c.update({'a': 1, 'd': 5})
print('Dict    :', c)
```

计数值是根据新数据增长的，而非替换原来的计数值。在前面的例子中，对元素<b>a</b>的计数值从3增长为4。

```
$ python3 collections_counter_update.py

Initial : Counter()
Sequence: Counter({'a': 3, 'b': 2, 'c': 1, 'd': 1})
Dict    : Counter({'d': 6, 'a': 4, 'b': 2, 'c': 1})
```

### 访问计数值

一旦Counter被填充了数据，其计数值即可通过与字典对象相同的API获取。

```python
# collections_counter_get_values.py
import collections

c = collections.Counter('abcdaab')

for letter in 'abcde':
    print('{} : {}'.format(letter, c[letter]))
```

Counter对象并不会对不存在的键抛出<b>KeyError</b>异常。如果键不在Counter中（例如字符<b>e</b>），其返回计数值为0。

```
$ python3 collections_counter_get_values.py

a : 3
b : 2
c : 1
d : 1
e : 0
```

<b>elements()</b>方法返回一个Counter对象中包含其所有元素的迭代器。

```python
# collections_counter_elements.py
import collections

c = collections.Counter('extremely')
c['z'] = 0
print(c)
print(list(c.elements()))
```

此方法并不保证元素的排列顺序，并且不包含计数值小于等于0的键。

```
$ python3 collections_counter_elements.py

Counter({'e': 3, 'x': 1, 'm': 1, 't': 1, 'y': 1, 'l': 1, 'r': 1,
'z': 0})
['x', 'm', 't', 'e', 'e', 'e', 'y', 'l', 'r']
```

使用<b>most_common()</b>方法生成最常出现的前<b>n</b>个元素和其计数值。

```python
# collections_counter_most_common.py
import collections

c = collections.Counter()
with open('/usr/share/dict/words', 'rt') as f:
    for line in f:
        c.update(line.rstrip().lower())

print('Most common:')
for letter, count in c.most_common(3):
    print('{}: {:>7}'.format(letter, count))
```

这个例子计算所有出现在系统字典文件中的单词的字母个数，并且输出最常见的前3个字母和其出现次数。如果不传递参数调用<b>most_common()</b>将会得到所有值及其计数。

```
$ python3 collections_counter_most_common.py

Most common:
e:  235331
i:  201032
a:  199554
```

### 算术运算

Counter对象支持算术运算和集合操作，用以聚合结果。下面的例子展示了如何创建一个Counter对象标准流程，而且包含了<b>+=</b>, <b>-=</b>, <b>&=</b>, 和 <b>|=</b>。

```python
# collections_counter_arithmetic.py
import collections

c1 = collections.Counter(['a', 'b', 'c', 'a', 'b', 'b'])
c2 = collections.Counter('alphabet')

print('C1:', c1)
print('C2:', c2)

print('\nCombined counts:')
print(c1 + c2)

print('\nSubtraction:')
print(c1 - c2)

print('\nIntersection (taking positive minimums):')
print(c1 & c2)

print('\nUnion (taking maximums):')
print(c1 | c2)
```

每次通过运算得到的新的Counter对象，都会抛弃计数值为0或负的值。对元素<b>a</b>的计数因为在<b>c1</b>和<b>c2</b>中相等，进行减法运算后，其计数值为0，因此被抛弃。

```
$ python3 collections_counter_arithmetic.py

C1: Counter({'b': 3, 'a': 2, 'c': 1})
C2: Counter({'a': 2, 'b': 1, 'p': 1, 't': 1, 'l': 1, 'e': 1, 'h': 1})

Combined counts:
Counter({'b': 4, 'a': 4, 'p': 1, 't': 1, 'c': 1, 'e': 1, 'l': 1, 'h': 1})

Subtraction:
Counter({'b': 2, 'c': 1})

Intersection (taking positive minimums):
Counter({'a': 2, 'b': 1})

Union (taking maximums):
Counter({'b': 3, 'a': 2, 'p': 1, 't': 1, 'c': 1, 'e': 1, 'l': 1, 'h': 1})
```
