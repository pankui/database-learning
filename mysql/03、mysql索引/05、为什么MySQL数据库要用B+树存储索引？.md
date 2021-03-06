## [参考](http://www.10tiao.com/html/667/201812/2652080677/1.html)

# 为什么MySQL数据库要用B+树存储索引？

数据库索引采用B+树而不是B树的主要原因：B+树只要遍历叶子节点就可以实现整棵树的遍历，
而且在数据库中基于范围的查询是非常频繁的，而B树只能中序遍历所有节点，效率太低


## 为什么索引结构默认使用B-Tree，而不是hash，二叉树，红黑树？

hash：虽然可以快速定位，但是没有顺序，IO复杂度高。

二叉树：树的高度不均匀，不能自平衡，查找效率跟数据有关（树的高度），并且IO代价高。

红黑树：树的高度随着数据量增加而增加，IO代价高。


## 为什么官方建议使用自增长主键作为索引。

结合B+Tree的特点，自增主键是连续的，在插入过程中尽量减少页分裂，
即使要进行页分裂，也只会分裂很少一部分