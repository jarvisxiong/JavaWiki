理解Mycat:
    开发认为是一个个数据库就行了，不过不是3306而是Mycat的8066端口了。    对于分片表尽量用基础的SQL来达到最佳的性能。
架构师认为，是一个强大的中间件， 能够用作读写分离，分库分表，容灾备份， 可用于多租户应用开发，云平台基础设施，让架构更灵活。系统访问瓶颈和热点，还能将不同功能的表映射到不同的存储上。

## Myscat原理
不复杂，
作动词的拦截， 对SQL进行特定的分析： 如分片分析，路由分析，读写分离分析，缓存分析等。  然后讲SQL发给后端的真实数据库，并将返回的结果做适当的处理，最终返回给用户。

 简单的例子： 三个数据库实例，放在两台数据库Server上
DB1@Server1 DB2@Server2 DB1@Server2
然后我们配置好根据字段prov来进行分片，然后发送给Mycat的语句如果有where prov='wuhan'就会发送给其中一个分片得到返回结果，如果是where prov in ()就会发送给多个分片，然后把多个结果做并集。

当有order by 和limit分页的时候，就会涉及二次处理。
最复杂的是Join的问题，Mycat提出了ER分片，全局表，HBT人工智能的Catlet,以及结合Storm/Spark引擎等十八般武艺的解决办法。

###应用场景
    读写分离，主从切换
分片分库，超过1000万的表进行分片
多租户应用，每个应用一个库，但应用程序都只连接Mycat
报表系统， 记住Mycat的分表能力，处理大规模报表的统计
替代Hbase,分析大数据
海量数据实时查询



