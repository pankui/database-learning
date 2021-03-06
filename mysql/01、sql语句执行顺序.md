
## [原文](https://blog.csdn.net/violet_echo_0908/article/details/51125406)

# SQL语句的执行顺序

SQL 不同于与其他编程语言的最明显特征是处理代码的顺序。
在大数编程语言中，代码按编码顺序被处理，但是在SQL语言中，
第一个被处理的子句是FROM子句，尽管SELECT语句第一个出现，但是几乎总是最后被处理。

每个步骤都会产生一个虚拟表，该虚拟表被用作下一个步骤的输入。
这些虚拟表对调用者（客户端应用程序或者外部查询）不可用。只是最后一步生成的表才会返回 给调用者。
如果没有在查询中指定某一子句，将跳过相应的步骤。下
面是对应用于SQL server 2000和SQL Server 2005的各个逻辑步骤的简单描述。

```sql

( 8 ) SELECT  
( 9 ) DISTINCT   
( 11 ) < Top  Num >   < select  list > 
( 1 ) FROM   [ left_table ] 
( 3 ) < join_type >   JOIN   < right_table > 
( 2 ) ON   < join_condition > 
( 4 ) WHERE   < where_condition > 
( 5 ) GROUP   BY   < group_by_list > 
( 6 ) WITH   < CUBE  |  RollUP > 
( 7 ) HAVING   < having_condition > 
( 10 ) ORDER   BY   < order_by_list >
 
```

```
--查询组合字段
(5)select (5-2) distinct(5-3) top(<top_specification>)(5-1)<select_list>
--连表
(1)from (1-J)<left_table><join_type> join <right_table> on <on_predicate>
        (1-A)<left_table><apply_type> apply <right_table_expression> as <alias>
        (1-P)<left_table> pivot (<pivot_specification>) as <alias>
        (1-U)<left_table> unpivot (<unpivot_specification>) as <alias>
--查询条件
(2)where <where_pridicate>
--分组
(3)group by <group_by_specification>
--分组条件
(4)having<having_predicate>
--排序
(6)order by<order_by_list>
(7)limit <n,m>//MySQL支持limit Oracle sqlserver支持top
————————————————
```
## Mysql执行顺序，即在执行时sql按照下面的顺序进行执行：

```sql

from
on
join
where
group by
having
select
distinct
union
order by

```

## 逻辑查询处理阶段简介

- FROM：对FROM子句中的前两个表执行笛卡尔积（Cartesian product)(交叉联接），生成虚拟表VT1

- ON：对VT1应用ON筛选器。只有那些使为真的行才被插入VT2。

- OUTER(JOIN)：如 果指定了OUTER JOIN（相对于CROSS JOIN 或(INNER JOIN),
保留表（preserved table：左外部联接把左表标记为保留表，
右外部联接把右表标记为保留表，完全外部联接把两个表都标记为保留表）
中未找到匹配的行将作为外部行添加到 VT2,生成VT3.如果FROM子句包含两个以上的表，
则对上一个联接生成的结果表和下一个表重复执行步骤1到步骤3，直到处理完所有的表为止。

- WHERE：对VT3应用WHERE筛选器。只有使为true的行才被插入VT4.

- GROUP BY：按GROUP BY子句中的列列表对VT4中的行分组，生成VT5.

- CUBE|ROLLUP：把超组(Suppergroups)插入VT5,生成VT6.

- HAVING：对VT6应用HAVING筛选器。只有使为true的组才会被插入VT7.

- SELECT：处理SELECT列表，产生VT8.

- DISTINCT：将重复的行从VT8中移除，产生VT9.

- ORDER BY：将VT9中的行按ORDER BY 子句中的列列表排序，生成游标（VC10).

- TOP：从VC10的开始处选择指定数量或比例的行，生成表VT11,并返回调用者。

注：步骤10，按ORDER BY子句中的列列表排序上步返回的行，
返回游标VC10.这一步是第一步也是唯一一步可以使用SELECT列表中的列别名的步骤。
这一步不同于其它步骤的 是，它不返回有效的表，而是返回一个游标。SQL是基于集合理论的。
集合不会预先对它的行排序，它只是成员的逻辑集合，成员的顺序无关紧要。
对表进行排序 的查询可以返回一个对象，包含按特定物理顺序组织的行。
ANSI把这种对象称为游标。理解这一步是正确理解SQL的基础。

例子：
```sql
select foo,count(foo)from pokes where foo>10group by foo having count (*)>5 order by foo
```
这条语句的执行顺序是： 

```
FROM->WHERE->GROUP BY->HAVING->SELECT->ORDER BY
```
