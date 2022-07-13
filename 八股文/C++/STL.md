# STL模板

---

### 顺序容器

1. vector：动态数组。支持尾部push和任意位置insert
2. list：双向链表
3. deque：循环队列
4. array：C++/11。大小固定的数组，不能添加和删除
5. forward_list：C++/11.前向链表，不能在尾部插入和删除，不能从尾部开始遍历

### 关联容器

1. map
2. multimap：与map的区别是——可以同时存储**多个键相同**的键值对
3. set
4. multiset：可以存储多个值相同的元素

### 无序关联容器（C++/11）

1. unordered_map
2. unordered_multimap
3. unordered_set
4. unordered_multiset

### 容器适配器

1. queue：标准FIFO
2. priority_queue：优先队列。元素类型要定义operator <
3. stack：标准FIFO
4. bitset：固定长度位序列的抽象