# immutable.js

> 普通js中我们实现深拷贝，当数据结构层数非常多的时候，很慢。

### 原理

使用了Structure Sharing的方法，当子节点修改时，只修改这个节点和它受影响的父节点，其他节点共享。

> 持久化数据结构(Persistent Data Structure)，使用旧数据创建新数据时，保持旧数据原有的数据结构不变

因此如何两个immutable data值相等时，比较的结果也是true