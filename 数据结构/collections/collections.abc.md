## collections.abc — 容器抽象基类

<b>collections.abc</b>模块包含了定义各种Python提供的容器API的抽象基类。下表展示了相关类和其用途。

类 | 基类 | API 用途
---- | ---- | ----
Container || 基本容器特性，如操作符
Hashable || 为容器实例提供生成哈希值特性
Iterable || 提供生成基于容器内容的迭代器对象特性
Iterator | Iterable | 基于容器内容的迭代器对象
Generator | Iterable | 扩展迭代器为生成器，使其符合PEP 342协议
Sized || 添加返回容器大小的方法
Callable || 使得容器能作为函数被调用
Sequence | Sized, Iterable, Container | 支持下标取值，遍历和改变元素顺序的特性
MutableSequence | Sequence | 支持在容器创建后，添加和删除元素
ByteString | Sequence | 组合<b>bytes</b>和<b>bytearray</b>的API
Set | Sized, Iterable, Container | 支持集合操作，如取交集和并集
MutableSet | Set | 支持集合被创建后操作其元素
Mapping | Sized, Iterable, Container | 定义被<b>dict</b>使用的只读API
MutableMapping | Mapping | 定义支持在创建后操作其元素的Mapping
MappingView | Sized | 定义在迭代器中操作Mapping的视图API
ItemsView | MappingView, Set | 视图API的一部分
KeysView | MappingView, Set | 视图API的一部分
ValuesView | MappingView | 视图API的一部分
Awaitable || 定义能在<b>await</b>表达式中使用的对象特性，如协程
Coroutine | Awaitable | 定义实现协程协议的类API
AsyncIterable || 定义符合PEP 492标准的支持<b>async</b>操作的迭代器特性
AsyncIterator | AsyncIterable | 定义异步迭代器API

为了在不同语义下明确定义容器的API，这些抽象基类能通过调用<b>isinstance()</b>，来检测一个对象是否支持该API。部分抽象基类还提供了方法的实现，可以Mixin模式来创建自定义容器类，而无需实现API中的每一个方法。
