学习笔记

#### 数组，链表，跳表

#### 基本实现和特性

数组

1. 数组的物理结构：一块连续的内存地址，元素相邻存储。当需要随机访问元素时，cpu使用寻址公式计算出元素位置进行随机元素读取。

2. 数组的各种操作时间复杂度

   其中插入，删除时为了保证内存存储的连续性，需要将元素进行移位操作

   | 操作名称     | 时间复杂度  |
   | ------------ | ----------- |
   | 插入         | O(n)        |
   | 删除         | O(n)        |
   | 访问特定元素 | O(1)        |
   | 有序查找     | 二分O(logn) |
   | 无序查找     | O(n)        |

3. 数组适用场景：插入删除操作少，主要用于随机访问时使用数组。若当元素有序且变化不频繁，使用二分查找能快速找到目标元素

链表

1. 链表的物理结构：内存存储上不需要连续存储（离散化存储），每个结点维护额外变量指针，用来指向当前结点的后继结点，实现元素的逻辑连续性

2. 链表的各种操作时间复杂度

   因为链表在内存上不连续存储，无法使用寻址公式进行元素的随机访问操作。需要从头指针开始迭代查找特定元素。

   | 操作名称           | 时间复杂度 |
   | ------------------ | ---------- |
   | 插入               | O(1)       |
   | 删除               | O(1)       |
   | 访问，查找特定元素 | O(n)       |

3. 链表适用场景：当需要对元素进行频繁插入和删除时，用链表合适。可以添加辅助结构哈希表，来实现在特定位置进行插入删除的操作

跳表

1. 跳表的物理结构：在链表的基础上添加辅助结构索引链表，将一维结构升至多维。用来改进链表查询慢的缺点

2. 跳表的各种操作时间复杂度

   跳表需要维护索引结构，因此在插入和删除元素时，需要同时对索引进行修改，修改复杂度为O(logn)

   | 操作名称 | 时间复杂度 |
   | -------- | ---------- |
   | 插入     | O(logn)    |
   | 删除     | O(logn)    |
   | 查找     | O(logn）   |

总结

1. 区分数组，链表，跳表的物理实现和各种操作的时间复杂度，学会针对特定的业务场景分析应该选用哪种类型存储数据
2. 谨记一个重要的思想：空间换时间，在内存空间充足的情况下，可以添加额外的辅助数据结构，使得算法操作的时间复杂度减少到最优



#### 队列，栈

#### 基本实现和特性

队列

1. 底层实现：数组或链表都可以，根据实际场景选择实现方式
2. 主要类别

| 类别     | 主要特性                                         |
| -------- | ------------------------------------------------ |
| 普通队列 | 队尾进，队头出，先进先出                         |
| 双端队列 | 头和尾都可进出元素，先进先出                     |
| 优先队列 | 队列中的元素按照关键字排序，出队时按照优先级出队 |

3.时间复杂度

其中优先队列在插入删除元素时，会对队列中元素进行一次优先级调整，调整时间复杂度为O(logn)。在优先队列中取出元素为O(1)

| 类别     | 添加    | 删除    |
| -------- | ------- | ------- |
| 普通队列 | O(1)    | O(1)    |
| 双端队列 | O(1)    | O(1)    |
| 优先队列 | O(logn) | O(logn) |



栈

1. 底层实现：数组或链表
2. 基本特性：先入后出
3. 操作时间复杂度

| 添加 | 删除 |
| ---- | ---- |
| O(1) | O(1) |

总结

+ 栈的特性为先入后出，队列为先进先出。根据实际场景中的需求选择正确的数据结构存储数据