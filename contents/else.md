---
sort: 4
---

# Else


## 推荐阅读



### 实验室整理经典论文阅读



By | Dase314-2020年9月 | 请勿分享 | Content
---|---|---|---
数据库系统 | 存储| LSM-tree 存储| 我上课视频(不再讲了)， [LSM-tree存储优化](https://arxiv.org/abs/1812.07527)
-- | --| 索引| [B+-tree技术](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.219.7269&rep=rep1&type=pdf)
-- | 查询| 数据库查询执行引擎| [火山模型,MPP](https://zhuanlan.zhihu.com/p/100949808 ) 
-- | |列存，向量执行|  [Apache Parquet](https://parquet.apache.org/),  [Apache Kudu](kudu.apache.org/kudu.pdf), [TIFlash列存](https://zhuanlan.zhihu.com/p/164490310)，  [SIMD向量化查询](http://www.cs.columbia.edu/~orestis/sigmod15.pdf)
-- ||NewSQL中数据库查询引擎| [Presto](https://prestosql.io/Presto_SQL_on_Everything.pdf), [Google F1](https://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/41344.pdf), [Snowflake](http://pages.cs.wisc.edu/~yxy/cs839-s20/papers/snowflake.pdf)
-- | 事务| 并发控制| [2PL, OCC, Basic Timestamp Ordering](https://www.guru99.com/dbms-concurrency-control.html), [MVCC](http://www.vldb.org/pvldb/vol10/p781-Wu.pdf)
-- | --| 日志| 集中式日志，[日志与缓冲区策略](http://www.cs.washington.edu/education/courses/cse544/11wi/papers/franklin97.pdf), [ARIES](https://dl.acm.org/doi/10.1145/128765.128770)
--|分布式事务 |--|[2PC](https://documentation.progress.com/output/ua/OpenEdge_latest/index.html#page/dmadm/how-the-database-engine-implements-two-phase-com.html), [Pecolator](https://research.google/pubs/pub36726/), [Vector Timestamps](https://www.cs.princeton.edu/courses/archive/fall18/cos418/docs/L4-vc.pdf), [Spanner](https://research.google/pubs/pub39966/), [非数据库层面解决方法： 消息队列， TCC](https://medium.com/@Alibaba_Cloud/breaking-the-limits-of-relational-databases-an-analysis-of-cloud-native-database-middleware-2-d3e790de0673) , [Bailis的文章](http://www.vldb.org/pvldb/vol7/p181-bailis.pdf)
--|Benchmark|--| Sysbench, [TPC-C, TPC-E, TPC-H, TPC-DS](http://www.tpc.org/tpcc/), 
-- |NewSQL数据库架构|云数据库|[Megastore](https://research.google/pubs/pub36971/), [Aurora](https://awsmedia.awsstatic-china.com/blog/2017/aurora-design-considerations-paper.pdf), [PolarDB（阿里)](https://zhuanlan.zhihu.com/p/87934090),
--  |--|HTAP|[TiDB](http://www.vldb.org/pvldb/vol13/p3072-huang.pdf)
分布式系统 | 分布式存储| 文件存储 |  [GFS](https://research.google.com/archive/gfs-sosp2003.pdf)
--|--| 分布式KV扩展| Dynamo,Bigtable,  Cassandra , Consistent Hashing(不再讲了)
--|分布式共识| Raft, ZAB| [Raft论文](https://raft.github.io/raft.pdf)(不再讲了), [Zookeeper](https://www.usenix.org/legacy/events/atc10/tech/full_papers/Hunt.pdf)
--|--| Paxos | Basic Paxos, Multi-Paxos(==这个请自己找资料==)
--|--| E-Paxos| [E-Paxos](https://www.cs.cmu.edu/~dga/papers/epaxos-sosp2013.pdf)
--|分布式锁| Chubby | [Chubby](https://www.cs.cmu.edu/~dga/papers/epaxos-sosp2013.pdf))
--|缓存|--|[Memcache at facebook](https://pdos.csail.mit.edu/6.824/papers/memcache-fb.pdf)
--|共享内存|平台|   [Sinfonia](http://www.sosp2007.org/papers/sosp064-aguilera.pdf), [Grappa](https://www.usenix.org/conference/atc15/technical-session/presentation/nelson) 
--|--|内存一致性模型| [一致性模型](https://zhuanlan.zhihu.com/p/48157076)， [内存计算核心技术](https://zhuanlan.zhihu.com/p/35668651)
实现技术|网络| RPC | [gRPC](https://grpc.io/)
--||IO复用|[一文看懂IO多路复用](https://zhuanlan.zhihu.com/p/115220699)，[事件驱动与协程](https://zhuanlan.zhihu.com/p/31410589) 
--|--|Lease| [Lease实现缓存一致性](http://duanple.com/?p=158)
--|多线程编程|竞争与等待|（这个请自己找资料看）
--||无锁技术| [lock-free data structure](https://www.cnblogs.com/lucifer1982/archive/2009/04/08/1431992.html)
--|--|锁实现| [Rethinking Lock](https://zhuanlan.zhihu.com/p/179245291)
--|新硬件|NVM|[PMDK](https://pmem.io/pmdk/)
--|--|RDMA|[RDMA技术](https://zhuanlan.zhihu.com/p/55142557)


### PingCAP 整理论文
