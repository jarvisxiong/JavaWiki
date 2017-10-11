数据模型  
    怎样访问和解释数据，关系型数据用使用的是ER图模型，行列结构，某一个可以是其他表的字段来表示关系
    Nosql抛弃了关系模型，每种nosql数据库解决模型的方式都不同，可以笼统分为了键值，文档，列族和图。且前三类统称为面向聚合。
    

2.1 聚合
    关系型数据库以元组为基础，标示一列，没有复杂的结构，元组不能包含元组，R数据就是靠这个支撑的。
    而nosql中的这些结构成为Aggregate 聚合。这是个领域驱动设计的概念表示，表示我们希望一致性操作和管理的一组对象的集合，定义为一个单元。通常按照聚合来复制和分片也比较自然。

2.1.1 关系模型和聚合模型示例。
    比如订单，用户，商品这样额结构，如果是关系型的话，我们需要很多的表：
    用户表， 单品表， 地址表， 付款方式表，还会有多对多的关系表。
    而如果使用聚合模型，就是两个JSON。 用户json和订单json(付款方式，用户id, 地址信息)， 对于聚合模型的设计主要在于划分聚合边界，比如这种两json的模式就会把商品名称放到订单模型中，这样不够常规。但是这样可以减少每次查询所需要关联的聚合模型。
    很难判断聚合边界，特别是当不同场景使用同一份数据的时候。比如如果我们基于上面的聚合要分析过去几个月的销量情况，这个时候一个聚合结构反而麻烦了，聚合结构可能会阻碍另一些结构的交互。
    在集群的时候，使用聚合可以很容易把需要访问的各种数据放在一起，而不是需要查找多个节点来得到数据。
    nosql数据库不支持ACID，但是保证操作一个聚合的操作是原子的。

2.2 键值模型与文档数据模型
        键值的聚合不透明，可以任意的放任何东西。文档数据库定义了其允许的结构和类型。
        键值数据库只能通过key来查找，文档数据库可以根据聚合中的字段来查找，还能根据内容创建索引。
        界限有些模糊，比如redis也是有一些结构的，比如列表和集合。
        若想支持查询功能，可以把solr这样的搜索工具集成进去。

2.3 列族存储
        先说列存储。 关系型的是行存储，一般都是查出哪些行(这种特别适合写入多的场合)。有些写比较少，但是经常需要一次读取若干行中的很多列，这个时候将某一列的所有行作为基本数据单元效果更好。
         最好按照两级聚合结构来理解。

第一级跟R数据库一致代表行标识符，可以用来取一个想要的聚合。 
第二级 是指向列的索引。这些二级值成为列族，我们可以操作指定的列，比如：get('1234','name')或者是所有行的name.  一行可以有多个列族。
        每个列族都表示了一种类型的记录，就想我们的接口文档中的flag等，数据的大行认为是列族中每个短行的拼接。
        列族中的行有宽行和瘦行的概念， 瘦行就是很多行，每行列差不多。 宽行就是很多列，每行列差别很大。
        
2.4 总结
分为，键值，文档，列族数据库。
聚合是关键，是分片的基础，是更新操作的最小单位。
键值数据库结构不透明
文档是透明的，因此能够查一个字段
列族数据库把聚合分成列族，结构会有限制，但是能够获得易访问性。
如果数据交互大多在同一个聚合内执行，则应该使用面向聚合的数据库， 如果交互需要使用多种不同格式的数据，就是左连，右连很多的情况应该选择关系型数据库。
