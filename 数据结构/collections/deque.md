## deque — 双端队列

双端队列，或者称为<b>deque</b>，支持在队首或队尾添加或删除元素。常用的栈和队列，这些被限制为数据只能从单端进出的数据结构，就是从<b>deque</b>退化而成的。

```python
# collections_deque.py

import collections

d = collections.deque('abcdefg')
print('Deque:', d)
print('Length:', len(d))
print('Left end:', d[0])
print('Right end:', d[-1])

d.remove('c')
print('remove(c):', d)
```

由于<b>deque</b>是一种序列容器，其支持部分类似于<b>list</b>的操作，如下标访问<b>\__getitem__()</b>、获取长度、从队列中根据下标移除元素等。

```
$ python3 collections_deque.py

Deque: deque(['a', 'b', 'c', 'd', 'e', 'f', 'g'])
Length: 7
Left end: a
Right end: g
remove(c): deque(['a', 'b', 'd', 'e', 'f', 'g'])
```

### 入队

<b>deque</b>对象支持从队首或队尾执行数据入队，队首和队尾在Python中称为左端和右端。

```python
# collections_deque_populating.py

import collections

# Add to the right
d1 = collections.deque()
d1.extend('abcdefg')
print('extend    :', d1)
d1.append('h')
print('append    :', d1)

# Add to the left
d2 = collections.deque()
d2.extendleft(range(6))
print('extendleft:', d2)
d2.appendleft(6)
print('appendleft:', d2)
```

<b>extendleft()</b>方法迭代并对每个元素执行<b>appendleft()</b>方法。最终结果是<b>deque</b>对象包含了倒置的输入序列。

```
$ python3 collections_deque_populating.py

extend    : deque(['a', 'b', 'c', 'd', 'e', 'f', 'g'])
append    : deque(['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h'])
extendleft: deque([5, 4, 3, 2, 1, 0])
appendleft: deque([6, 5, 4, 3, 2, 1, 0])
```

### 出队

与入队相似，对<b>deque</b>对象调用不同的方法，其中的元素可以从队首或队尾出队。

```python
# collections_deque_consuming.py

import collections

print('From the right:')
d = collections.deque('abcdefg')
while True:
    try:
        print(d.pop(), end='')
    except IndexError:
        break
print

print('\nFrom the left:')
d = collections.deque(range(6))
while True:
    try:
        print(d.popleft(), end='')
    except IndexError:
        break
print
```

调用<b>pop()</b>方法从队列右端移除一个元素，调用<b>popleft()</b>方法可从队列左端移除一个元素。

```
$ python3 collections_deque_consuming.py

From the right:
gfedcba
From the left:
012345
```

由于<b>deque</b>对象是线程安全的，其元素甚至可以同时、在不同的线程中，从队列的双端被移除。

```python
# collections_deque_both_ends.py

import collections
import threading
import time

candle = collections.deque(range(5))


def burn(direction, nextSource):
    while True:
        try:
            next = nextSource()
        except IndexError:
            break
        else:
            print('{:>8}: {}'.format(direction, next))
            time.sleep(0.1)
    print('{:>8} done'.format(direction))
    return

left = threading.Thread(target=burn,
                        args=('Left', candle.popleft))
right = threading.Thread(target=burn,
                         args=('Right', candle.pop))

left.start()
right.start()

left.join()
right.join()
```

在这个例子中，两个线程在队列双端交替地移除元素，直到队列为空。

```
$ python3 collections_deque_both_ends.py

 Left: 0
Right: 4
Right: 3
 Left: 1
Right: 2
 Left done
Right done
```

### 循环移位

<b>deque</b>的另一个有用的功能是可以执行双向移位，达到跳过若干元素的目的。

```python
# collections_deque_rotate.py
import collections

d = collections.deque(range(10))
print('Normal        :', d)

d = collections.deque(range(10))
d.rotate(2)
print('Right rotation:', d)

d = collections.deque(range(10))
d.rotate(-2)
print('Left rotation :', d)
```

队列右移(正向移位)将元素从右端移除，并放入左端。队列左移(反向移位)将元素从左端移除，并放入右端。将<b>deque</b>的元素想象成轮式电话的拨盘，可以加深对其理解。

```
$ python3 collections_deque_rotate.py

Normal        : deque([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
Right rotation: deque([8, 9, 0, 1, 2, 3, 4, 5, 6, 7])
Left rotation : deque([2, 3, 4, 5, 6, 7, 8, 9, 0, 1])
```

### 限制队列长度

<b>deque</b>对象可以通过配置其长度为最大值，从而使其永远不会超过该大小。当队列长度达到了最大值，若继续将元素入队，队内的元素将被丢弃。这种特性在查找不定长流对象的最后<b>n</b>个元素时特别有用。

```python
# collections_deque_maxlen.py

import collections
import random

# Set the random seed so we see the same output each time
# the script is run.
random.seed(1)

d1 = collections.deque(maxlen=3)
d2 = collections.deque(maxlen=3)

for i in range(5):
    n = random.randint(0, 100)
    print('n =', n)
    d1.append(n)
    d2.appendleft(n)
    print('D1:', d1)
    print('D2:', d2)
```

即使在队列已满的情况下继续入队元素，队列的最大长度仍会被维持在设定值。

```
$ python3 collections_deque_maxlen.py

n = 17
D1: deque([17], maxlen=3)
D2: deque([17], maxlen=3)
n = 72
D1: deque([17, 72], maxlen=3)
D2: deque([72, 17], maxlen=3)
n = 97
D1: deque([17, 72, 97], maxlen=3)
D2: deque([97, 72, 17], maxlen=3)
n = 8
D1: deque([72, 97, 8], maxlen=3)
D2: deque([8, 97, 72], maxlen=3)
n = 32
D1: deque([97, 8, 32], maxlen=3)
D2: deque([32, 8, 97], maxlen=3)
```
