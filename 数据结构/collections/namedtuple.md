## namedtuple — 带名称字段的元组子类

标准元组<b>tuple</b>使用数字下标访问成员。

```python
# collections_tuple.py

bob = ('Bob', 30, 'male')
print('Representation:', bob)

jane = ('Jane', 29, 'female')
print('\nField by index:', jane[0])

print('\nFields by index:')
for p in [bob, jane]:
    print('{} is a {} year old {}'.format(*p))
```

简单情况下使用<b>tuple</b>没什么问题。

```
$ python3 collections_tuple.py

Representation: ('Bob', 30, 'male')

Field by index: Jane

Fields by index:
Bob is a 30 year old male
Jane is a 29 year old female
```

一般来说，通过下标来访问元素常常会带来错误，特别是在元组具有很多元素、被调用的地方离创建的地方很远的情况下。通过使用<b>namedtuple</b>可以有效避免上述情况发生。

### 定义

<b>namedtuple</b>实例的内存效率和普通的元组对象无异，因为它没有使用字典来实现具体功能。每种<b>namedtuple</b>都是独立实现，通过<b>namedtuple()</b>工厂方法创建。第一个参数是新<b>namedtuple</b>的类名，第二个参数是使用空格分割的字段名字符串。

```python
# collections_namedtuple_person.py

import collections

Person = collections.namedtuple('Person', 'name age')

bob = Person(name='Bob', age=30)
print('\nRepresentation:', bob)

jane = Person(name='Jane', age=29)
print('\nField by name:', jane.name)

print('\nFields by index:')
for p in [bob, jane]:
    print('{} is {} years old'.format(*p))
```

如上述例子，除了可以通过字段名（.语法）访问对应元素外，还可以通过下标访问对应位置的元素。

```
$ python3 collections_namedtuple_person.py

Representation: Person(name='Bob', age=30)

Field by name: Jane

Fields by index:
Bob is 30 years old
Jane is 29 years old
```

就像普通元组一样，<b>namedtuple</b>实例也是不可变对象。这个约束特性使得其具有一致性哈希值，能够作为字典的键或者作为集合中的元素。

```python
# collections_namedtuple_immutable.py

import collections

Person = collections.namedtuple('Person', 'name age')

pat = Person(name='Pat', age=12)
print('\nRepresentation:', pat)

pat.age = 21
```

试图改变一个<b>namedtuple</b>中元素的值时，会产生一个<b>AttributeError</b>异常。

```
$ python3 collections_namedtuple_immutable.py

Representation: Person(name='Pat', age=12)
Traceback (most recent call last):
  File "collections_namedtuple_immutable.py", line 17, in
<module>
    pat.age = 21
AttributeError: can't set attribute
```

### 无效的字段名

使用重复字段名或者Python关键字作为<b>namedtuple</b>的字段名是无效的。

```python
# collections_namedtuple_bad_fields.py

import collections

try:
    collections.namedtuple('Person', 'name class age')
except ValueError as err:
    print(err)

try:
    collections.namedtuple('Person', 'name age age')
except ValueError as err:
    print(err)
```

当转换字段名字符串时，无效的字段名值会导致抛出<b>ValueError</b>异常。

```
$ python3 collections_namedtuple_bad_fields.py

Type names and field names cannot be a keyword: 'class'
Encountered duplicate field name: 'age'
```

当<b>namedtuple</b>的创建行为不确定的情况下（比如查询数据库时，返回的是未知表结构的数据时），应该将<b>rename</b>参数设置为<b>True</b>，使得无效字段名能够被自动重命名。

```python
# collections_namedtuple_rename.py

import collections

with_class = collections.namedtuple(
    'Person', 'name class age',
    rename=True)
print(with_class._fields)

two_ages = collections.namedtuple(
    'Person', 'name age age',
    rename=True)
print(two_ages._fields)
```

被重命名的字段名会根据他们所在元组中的位置来决定，所以上述例子中，<b>class</b>被重命名为<b>\_1</b>，重复的<b>age</b>被重命名为<b>\_2</b>。

```
$ python3 collections_namedtuple_rename.py

('name', '_1', 'age')
('name', 'age', '_2')
```

### 特殊属性

<b>namedtuple</b>提供了多种有用的属性和方法来和子类型和实例对象协作。所有的这些內建属性都有一个下划线前缀(\_)，用以标示其为Python程序中的私有属性。但对于<b>namedtuple</b>，这个下划线前缀是为了避免和用户编写的属性冲突。

<b>namedtuple</b>的字段名定义被存放在其<b>\_fields</b>属性中。

```python
# collections_namedtuple_fields.py

import collections

Person = collections.namedtuple('Person', 'name age')

bob = Person(name='Bob', age=30)
print('Representation:', bob)
print('Fields:', bob._fields)
```

传入字段名参数是通过空格分隔每个字段名的，但<b>namedtuple</b>内部是通过一个序列对象存放的。

```
$ python3 collections_namedtuple_fields.py

Representation: Person(name='Bob', age=30)
Fields: ('name', 'age')
```

<b>namedtuple</b>实例可以通过调用<b>\_asdict()</b>来转换成<b>OrderedDict</b>实例。

```python
# collections_namedtuple_asdict.py

import collections

Person = collections.namedtuple('Person', 'name age')

bob = Person(name='Bob', age=30)
print('Representation:', bob)
print('As Dictionary:', bob._asdict())
```

<b>OrderedDict</b>的键和<b>namedtuple</b>的字段名一致，并且顺序相同。

```
$ python3 collections_namedtuple_asdict.py

Representation: Person(name='Bob', age=30)
As Dictionary: OrderedDict([('name', 'Bob'), ('age', 30)])
```

<b>\_replace()</b>方法创建一个新实例，并且替换掉其中的字段值。

```python
# collections_namedtuple_replace.py

import collections

Person = collections.namedtuple('Person', 'name age')

bob = Person(name='Bob', age=30)
print('\nBefore:', bob)
bob2 = bob._replace(name='Robert')
print('After:', bob2)
print('Same?:', bob is bob2)
```

即使这个方法的名称看上去是修改现有的对象，但由于<b>namedtuple</b>是不可变对象，所以实际上返回了一个新实例对象。

```
$ python3 collections_namedtuple_replace.py

Before: Person(name='Bob', age=30)
After: Person(name='Robert', age=30)
Same?: False
```
