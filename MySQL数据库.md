<a href='#fanshi'>一、数据库三大范式</a>  
<a href='#slowfind'> 二、慢查询</a>  
<a href='#index'>三、索引</a>  
　　<a href='#primaryIndex'>1.主键索引</a>  
　　<a href='#normalIndex'>2.普通索引</a>  
　　<a href='#uniqueIndex'>3.唯一索引</a>  
　　<a href='#goodAndBad'>4.索引的优缺点</a>  
　　<a href='#bTreeAndBPlusTree'>5.B树和B+树做索引的区别</a>  
<a href='#MySQLImprove'>四、MySQL优化技巧</a>  
<a href='#transactionTrait'>五、事物的特性（ACID）</a>  
<a href='#transactionIsolation'>六、事务的隔离级别</a>  
<a href='#MVCC'>七、MVCC（Multi-Version Concurrency Control多版本并发控制）</a>  


<a href='#reference'>reference</a>  

----



<a id='fanshi'></a>
# 一、数据库三大范式

&emsp;&emsp;为了建立冗余较小、结构合理的数据库，设计数据库时必须遵循一定的规则，在关系型数据库中这种规则就称为范式。范式是符合某一种设计要求的总结，要想设计一个结构合理的关系型数据库，必须满足一定的范式。  
　　**三大范式：**  
　　第一范式：1NF是对属性的原子性约束，要求属性(列)具有原子性，不可再分解；(只要是关系型数据库都满足1NF)  
　　第二范式：2NF是对记录的惟一性约束，表中的记录是唯一的, 就满足2NF, 通常我们设计一个主键来实现，主键不能包含业务逻辑。  
　　第三范式：3NF是对字段冗余性的约束，它要求字段没有冗余。 没有冗余的数据库设计可以做到。  
　　但是，没有冗余的数据库未必是最好的数据库，有时为了提高运行效率，就必须降低范式标准，适当保留冗余数据。具体做法是： 在概念数据模型设计时遵守第三范式，降低范式标准的工作放到物理数据模型设计时考虑。降低范式就是增加字段，允许冗余。  



<a id='slowfind'></a>
# 二、慢查询

&emsp;&emsp;MySQL默认10秒内没有响应SQL结果,则为慢查询。可以去修改MySQL慢查询默认时间。修改方式：  

```sql
--显示慢查询次数
show status like 'slow_queries';
--查询慢查询时间
show variables like 'long_query_time';
--修改慢查询时间
set long_query_time=1; ---但是重启mysql之后，long_query_time依然是my.ini中的值
```



<a id='index'></a>
# 三、索引
&emsp;&emsp;数据库索引，是数据库管理系统中一个排序的数据结构，以协助快速查询、更新数据库表中数据。索引的实现通常使用 B 树及其变种 B+ 树。  
　　**注意**：为表设置索引要付出代价的：一是增加了数据库的存储空间，二是在插入和修改数据时要花费较多的时间(因为索引也要随之变动)。  

**查询索引**:  
```sql
SHOW INDEX FROM tablename;
show keys from tablename;
DESC tablename; --不能显示索引名称
```

<a id='primaryIndex'></a>
## 1.主键索引
&emsp;&emsp;主键是一种唯一性索引，但它必须指定为“PRIMARY KEY”。  

```sql
--当一张表，把某个列设为主键的时候，则该列就是主键索引，例如：
create table aaa
(id int unsigned primary key auto_increment ,
name varchar(32) not null default '');

--如果你创建表时，没有指定主键索引，也可以在创建表后在添加指令：
alter table tablename add primary key(列名);
--删除主键索引
alter table tablename drop primary key(列名);
```


<a id='normalIndex'></a>
## 2.普通索引
&emsp;&emsp;普通索引（由关键字KEY或INDEX定义的索引）的唯一任务是加快对数据的访问速度。因此，应该只为那些最经常出现在查询条件（WHERE column =）或排序条件（ORDER BY column）中的数据列创建索引。只要有可能，就应该选择一个数据最整齐、最紧凑的数据列（如一个整数类型的数据列）来创建索引。  

```sql
create table ccc(
id int unsigned,
name varchar(32)
)
create index [索引名] on tablename (列1,列名2);

alter table tablename add index [索引名](列1,列名2);
```


<a id='uniqueIndex'></a>
## 3.唯一索引
&emsp;&emsp;唯一索引列的所有值都只能出现一次，即必须唯一。唯一性索引可以用以下几种方式创建：  
```sql
--创建索引
CREATE UNIQUE INDEX [索引名] ON tablename (列名);
--修改表
ALTER TABLE tablename ADD UNIQUE [索引名] (列名);
--创建表的时候指定索引
CREATE TABLE tablename ( [...], UNIQUE [索引名] (列名));
--例：
create table tablename(id int primary key auto_increment , name varchar(32) unique);
```
**注意**：unique字段可以为NULL，并可以有多NULL，但是如果是具体内容，则不能重复，但是不能存有重复的空字符串' '


<a id='goodAndBad'></a>
## 4.索引的优缺点
**优点**：创建索引可以大大提高系统的性能。  
第一，通过创建唯一性索引，可以保证数据库表中每一行数据的唯一性。  
第二，可以大大加快数据的检索速度，这也是创建索引的最主要的原因。  
第三，可以加速表和表之间的连接，特别是在实现数据的参考完整性方面特别有意义。  
第四，在使用分组和排序子句进行数据检索时，同样可以显著减少查询中分组和排序的时间。  
第五，通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。  

**缺点**：
第一，创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加。  
第二，索引需要占物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间，如果要建立聚簇索引，那么需要的空间就会更大。  
第三，当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，这样就降低了数据的维护速度。  


<a id='bTreeAndBPlusTree'></a>
## 5.B树和B+树做索引的区别

**B树查找方式**：
&emsp;&emsp;B树中每个节点包含了键值和键值对于的数据对象存放地址指针，所以成功搜索一个对象可以不用到达树的叶节点。  
　　在 B 树中查找给定关键字的方法是：首先把根结点取来，在根结点所包含的关键字 K1,…,kj 查找给定的关键字（可用顺序查找或二分查找法），若找到等于给定值的关键字，则查找成功；否则，一定可以确定要查的关键字在某个 Ki 或 Ki+1 之间，于是取Pi所指的下一层索引节点块继续查找，直到找到，或指针 Pi 为空时查找失败。  


**B＋树查找方式**：
&emsp;&emsp;B+树非叶节点中存放的关键字并不指示数据对象的地址指针，非叶节点只是索引部分。所有的叶节点在同一层上，包含了全部关键字和相应数据对象的存放地址指针，且叶节点按关键字从小到大顺序链接。  
　　B+树有2个头指针，一个是树的根节点，一个是最小关键码的叶节点。所以 B+ 树有两种搜索方法：一种是按叶节点自己拉起的链表顺序搜索。一种是从根节点开始搜索，和 B 树类似，不过如果非叶节点的关键码等于给定值，搜索并不停止，而是继续沿右指针，一直查到叶节点上的关键码。所以无论搜索是否成功，都将走完树的所有层。B+树中，数据对象的插入和删除仅在叶节点上进行。  


**B树和B＋树处理索引的不同之处**：  
1. B树中同一关键字不会出现多次，它有可能出现在叶结点，也有可能出现在非叶结点中。而B+树的关键字一定会出现在叶结点中，并且有可能在非叶结点中重复出现，以维持 B+ 树的平衡。  
2. 因为B树键位置不定，且在整个树结构中只出现一次，虽然可以节省存储空间，但使得在插入、删除操作复杂度明显增加。B+树相比来说是一种较好的折中。  
3. B树的查询效率与键在树中的位置有关，最大时间复杂度与B+树相同(在叶结点的时候)，最小时间复杂度为 1(在根结点的时候)。而B+树的复杂度对某建成的树是固定的，一定会查找到叶结点。  


<a id='MySQLImprove'></a>
# 四、MySQL优化技巧
1. 当某列建立过组合索引或者唯一索引，使用like时，如果使用 like '%abc%' ,会全表扫描，而使用  like 'abc%'则会通过索引查找。  
2. 在使用or连接2个条件时，如果有一个条件没有添加索引，就不会通过索引查找。  
3. 在判断某个字段是否为null是，通过  IS NULL 判断，不要通过 =NULL判断。  
4. 在使用group by分组时，会进行排序，可能会降低速度。可以通过在 group by 后面增加 order by null 禁止排序，从而提升速度。  
5. in 和 not in 也要慎用，因为导致全表扫描。  
6. 尽量避免NULL：应该指定列为NOT NULL，除非你想存储NULL。在MySQL中，含有空值的列很难进行查询优化，因为它们使得索引、索引的统计信息以及比较运算更加复杂。你应该用0、一个特殊的值或者一个空串代替空值。  



<a id='transactionTrait'></a>
# 五、事物的特性（ACID）
&emsp;&emsp;原子性（Atomicity）：指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。  
　　一致性（Consistency）：事务开始前和结束后，数据库的完整性约束没有被破坏 。比如A向B转账，不可能A扣了钱，B却没收到。  
　　隔离性（Isolation）：事务的隔离性是多个用户**并发**访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。  
　　持久性（Isolation）：指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响。  



<a id='transactionIsolation'></a>
# 六、事务的隔离级别
&emsp;&emsp;事物的隔离级别主要是**为了保证事物的隔离性**，当有多个事物同时操作某个表的数据时，会有以下几个情况发生：  
　　脏读：指一个事务读取了另一个事务未提交的数据。例如在数据库访问中，事务T1将某一值修改，然后事务T2读取该值，此后T1因为某种原因撤销对该值的修改，这就导致了T2所读取到的数据是无效的。  
　　不可重复读：指在数据库访问中，一个事务范围内两个相同的查询却返回了不同数据。比如事务T1读取某一数据，事务T2读取并修改了该数据，T1为了对读取值进行检验而再次读取该数据，便得到了不同的结果。（update）  
　　虚读（幻读）：是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。(insert)  

**事物有如下四个隔离级别**：
- READ UNCOMMITTED: 赃读、不可重复读、虚读都有可能发生。  
- READ COMMITTED: 避免赃读。不可重复读、虚读都有可能发生。（oracle默认）  
- REPEATABLE READ:避免赃读、不可重复读。虚读有可能发生。（mysql默认）  
- SERIALIZABLE: 避免赃读、不可重复读、虚读。  


**不同隔离级别可能发生的问题**：
| 事物隔离级别 | 脏读 | 不可重复读 | 幻读 |
| --- | --- | --- | --- |
| READ UNCOMMITTED | 是 | 是 | 是 |
| READ COMMITTED | 否 | 是 | 是 |
| REPEATABLE READ | 否 | 	否 | 是 |
| SERIALIZABLE | 否 | 否 | 否 |


查看mysql默认的隔离级别，输入命令：
```
SELECT @@TRANSACTION_ISOLATION;
```
![collection](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/transaction.jpg?raw=true)



<a id='MVCC'></a>
# 七、MVCC（Multi-Version Concurrency Control多版本并发控制）
**注**：本节参考[轻松理解MYSQL MVCC 实现机制](https://blog.csdn.net/whoamiyang/article/details/51901888)，详细了解请参考原文  

&emsp;&emsp;大多数的MySQL事务型存储引擎，如InnoDB都使用一种简单的行锁机制。事实上，他们都和另外一种用来增加并发性的被称为“多版本并发控制（MVCC）”的机制来一起使用。你可将MVCC看成行级别锁的一种妥协，它在许多情况下避免了使用锁，同时可以提供更小的开销。
　　InnoDB引擎有当前读和快照读两种模式。当前读即加锁读，读取记录的最新版本号，会加锁保证其他并发事物不能修改当前记录，直至释放锁。插入、更新、删除操作默认使用当前读，显示的为select语句加lock in share mode或for update的查询也采用当前读模式。 快照读不加锁，读取记录的快照版本，而非最新版本，使用MVCC机制，最大的好处是读取不需要加锁，读写不冲突，用于读操作多于写操作的应用，因此在不显示加lock in share mode或for update的select语句，即普通的一条select语句默认都是使用快照读MVCC实现模式。
　　InnoDB通过为每一行记录添加两个额外的隐藏的值来实现MVCC，这两个值一个记录这行数据何时被创建（这里记为”创建版本ID“），另外一个记录这行数据何时过期或删除（这里记为”删除版本ID“）。InnoDB并不存储这些事件发生时的实际时间，它只存储这些事件发生时的系统版本号。这是一个随着事务的创建而不断增长的数字。每开始一个新的事务，系统版本号就会自动递增，事务开始时刻的系统版本号会作为事务的ID。下面来看看当隔离级别是REPEATABLE READ时这种策略是如何应用到特定的操作的：  



| CRUD | 操作方式 |
| --- | --- |
| SELECT | 只会查找同时满足：<br> A.创建版本ID<=当前事务ID（确保事务读取的行要么在事务开始前已经存在，要么是事务自身插入或者修改过的） <br> B.删除版本ID>当前事务ID或未定义（确保事务读取到的行，在事务开始之前未被删除） |
| INSERT | 将新行记录的创建版本ID设置为当前事务ID |
| DELETE | 将该行的删除版本ID设置为当前事务ID |
| UPDATE | 写一个这行数据的新拷贝，拷贝的创建版本ID设置为当前事务ID，同时将旧行的删除版本ID设置为当前事务ID |












<a id='reference'></a>
# reference
[轻松理解MYSQL MVCC 实现机制](https://blog.csdn.net/whoamiyang/article/details/51901888)  

