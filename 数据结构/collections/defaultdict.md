## defaultdict — 键不存在时返回默认值

标准字典对象已经包含了<b>setdefault()</b>方法，用以在获取键值时，键不存在的情况下，返回默认值。与之对比，<b>defaultdict</b>允许调用者在初始化时显式地指定默认值。

```python
# collections_defaultdict.py

import collections

def default_factory():
    return 'default value'

d = collections.defaultdict(default_factory, foo='bar')
print('d:', d)
print('foo =>', d['foo'])
print('bar =>', d['bar'])
```

这个方法在需要将所有键的值都设置为相同默认值的情况下十分有用。尤其是在默认值是一种用以做聚合或计数的类型时，如<b>list</b>、<b>set</b>甚至是<b>int</b>。标准库文档包含了数个如此使用<b>defaultdict</b>的例子。

```
$ python3 collections_defaultdict.py

d: defaultdict(<function default_factory at 0x101341950>,
{'foo': 'bar'})
foo => bar
bar => default value
```

> 拓展<br/>
> [defaultdict examples](https://docs.python.org/3.5/library/collections.html#defaultdict-examples) - 标准库文档中使用<b>defaultdict</b>例子。<br/>
> [Evolution of Default Dictionaries in Python](http://jtauber.com/blog/2008/02/27/evolution_of_default_dictionaries_in_python/) - James Tauber关于<b>defaultdict</b>几种初始化字典方法的讨论
