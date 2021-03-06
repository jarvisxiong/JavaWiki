数据库的作用： 数据仓库，内容索引和部署软件，高可用的冗余系统，在线事务处理系统OLTP

mqsql的架构特色：
    基于存储引擎架构， 体现了分离的概念，存储引擎只负责实现存储api, 会有其他层负责其他事情。
    



数据库的并发控制。

靠的是锁
共享锁和排他锁   +共享锁后可以再加共享锁，共享锁只读。不能加排他锁
            加排他锁后可以读写，不能再加其他的锁。  （多线程适用）
                    避免死锁： 1. 一次性锁所有数据，  2. 规定所得顺序，所有事务按照同样的事务锁 ， 3允许死锁，然后撤回代价小的锁

锁策略
    锁最少的数据，会获得最大的并发性。但是会带来锁维护的开销，影响性能，要综合考虑。通常有两种：
表锁：  for update   有索引的情况下是行锁..   这个什么sql用什么锁，还要再确认一下。

行锁：

页锁：


事务：
ACID
    A : 原子性    要么全提交成功，要么全失败回滚
    C：一致性    是从一个一致性状态到另外一个一致性状态。  意思就是说能量守恒。
        还有的人理解其是外键的定义，即对于有班级的学生，不能创建一个不指定班级的学生
        还有人认为一致性和原子性共同满足了全好全坏的限定。
    I：隔离性    跟隔离级别相关，通常来说，事物看不到其他未提交的事务的状态
    D：持久性   事务一旦提交，所做的修改就是永久保存的

隔离级别
READ UNCOMMITTED 可以读到没有提交的数据。 会造成脏读。   通常不会使用
READ COMMITTED     提交读    只能看到已经提交事务所做的更改， 会引入不可重复读。  事务中，在其他事务修改数据后，同样的sql可能获得不一样的结果。  大多数数据库的默认级别都是他。
REPEATABLE READ 可重复读。 避免不可重复读。  但是会造成幻读。  幻读是其他事务插入或者删除，范围查询可能会发现多了或者少了行。 
        不可重复读是修改， 幻读是插入或者删除.  解决的方式是，加区间锁，锁定一个范围内的数据。   mysql的默认的
        mvcc ???
SERIALIZABLE 可串行化。  强制所有事务串行执行。 实际很少使用这个级别

事务日志：
        使用事务日志，存储引擎在修改数据时只修改内存拷贝，再把修改行为记录到事务日志中(事务日志是一个块固定的磁盘区域，使用追加的方式，因此比直接回写数据需要不同的移动磁头的方式快)。这样可以慢慢再刷回磁盘，提高性能。 这种通常称之为预写式日志。  修改数据要写两次磁盘。
        
mysql中的事务
    show variables like 'autocommit';   用set autocommit = 1 or 0来开关
    set session transaction isolation level read committed;
    混合使用存储引擎的时候回滚会有问题，不支持事务的存储引擎回滚是无效的
    
隐式和显示锁定
    事务执行过程中的加锁都是隐形的。InnoDB会根据隔离级别在需要的时候自动加锁
    也可以显示锁定：
    SELECT... LOCK IN SHARE MODE
    SELECT ... FOR UPDATE. 不是标准的sql语句。


多版本并发控制MVCC   比单纯的行级锁更高效 。 在REPEATEABLE READ的隔离级别下
    InnoDB的MVCC，
            有一个系统版本号，每次事物开始的时候递增。
            每行后面保存两个隐藏列实现的。  一个是创建时间，一个是删除时间。
            INSERT   新增的行保存当前系统版本号作为行版本号
            DELETE 为删除的行保存当前系统版本号作为行删除标识
            update 插入一行新的保存当前系统版本作为行版本，同时保存当前系统版本号到原来行作为删除标识。
            SELECT的时候
                        只查创建版本<=当前事物版本的数据行。 确保事物读取的行要么开始时已存在，要么是自己写入的。
                        行的删除版本要么为定义，要么大于当前事物版本号，可以确保事物读取到的行，在事务开始之前未被删除。

show table status like 'message';
  查看表的定义。 

InnoDB 存储引擎
    大量短期事务， 大部分成功少量回滚。   5.5才真正流行起来
    
    数据存储在表空间中，表空间是黑盒的，由一系列文件组成。
    用MVCC支持高并发，并且实现了四个标准的隔离级别，默认是REPEATABLE READ可重复读，用间隙锁策略防止幻读
    存储格式是平台独立的，可以将数据和索引文件复制
    基于聚簇索引建立，  对主键查询有很高的性能，二级索引必须包含主键列，所以主键应该尽可能的小
    内部优化
    支持热备份。

MyISAM引擎
    全文索引、压缩、GIS等特性，但是不支持事务和行级锁。  崩溃后无法安全修复。  对于只读及表比较小依然可以使用MyISAM
    存储使用两个文件：.myd数据文件，及.myi索引文件。
    可以设置MAX_ROWS设置指针
    一些特性：
        加锁与并发：表锁，
        修复：  可能会丢失数据
        索引， 支持BLOB等长字段建立索引。    还支持基于分词创建全文索引。

其他：
    CSV 引擎
    Memory引擎，  丢失没关系，快速访问数据。  现在的内存数据库
        读取快，表锁，写入慢，不支持大字段，长度固定。  mysql内部如果需要使用临时表来保存数据，也是用的它。
    NDB集群引擎，  一个分布式的，share-nothing的、容灾的、高可用的NDB数据库的组合。

如何抉择：
        InnoDB首选， 全文索引用InnoDB加上Sphinx。因素：
        事务。   InnoDB支持
        备份。 InnoDB热备。
        崩溃恢复。  MyISAM比InnoDB几率高，恢复慢。因此即使不需要事务InnoDB也是非常重要的因素。
特有特性。
        MyISAM支持空间搜索。

应用类型及选择：
    1. 日志型， 插入性能要求高。 可以用MyISAM, 但是如果要进行分析的时候就要考虑拷贝一份备库去做分析了。
    2. 读远多于写，   使用索引的InnoDB可能是很好的选择
    3. 订单处理            支持事务
    4. 电子公告牌和讨论论坛，    是简单的几个表读写极为频繁的情况，  大数据的数据仓库的解决方案可能用Infobright比较适合

转换表引擎
    ALTER TABLE 命令，会执行很久
    导入导出，使用mysqldump工具
    先create一张表，然后使用insert select语句来导数据
    
        



顺序io代替随机io
缓存代替磁盘
多次写入，一次刷新


    

    