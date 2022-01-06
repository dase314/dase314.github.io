# 胡卉芪 Huiqi Hu

<img width="120px" src="https://github.com/dase314/dase314.github.io/blob/main/images/dase_logo.PNG?raw=true">
<img width="150px" src="https://github.com/dase314/dase314.github.io/blob/main/images/sch_logo.PNG?raw=true">


##  个人信息


中国 华东师范大学 数据科学与工程学院, 副教授， 主要从事数据库系统研究。 [华师大主页](https://faculty.ecnu.edu.cn/_s37/hhq2/main.psp),[知乎主页](https://www.zhihu.com/people/hq-hu)

School of Data Science and Engineering, East China Normal University(ECNU), Shanghai, China

Email: hqhu@dase.ecnu.edu.cn

Short bio: I got my bachelor's degree at Xidian University and I recieved my Phd's degree at Tsinghua University supervised by Prof Jianhua Feng and Prof. Guoliang Li (<http://dbgroup.cs.tsinghua.edu.cn/ligl/>). Now I'm an associate professor at School of Data Science and Engineering (**DASE**), East China Normal University (**ECNU**). My main research interests are database and distributed systems.

招收博士、学硕与专硕，感兴趣同学请email联系

##  实验室信息 Lab Dase-314 

I run a small lab studying database and distributed systems. Our research area includes: storage, in-memory and parallel computing,  transaction processing and distributed consensus. In the lab, we prefer study open-sourced systems or open-sourced projects. The students are engaged in research/engineering in systems.

Specifically, my recent interests include:

* Distributed transaction and distributed consensus theory
* Distributed system under new environment, including large-scale 
  distributed database, cloud-based database, database system under hierarchical storage(DRAM/PM/SSD/S3), database for new hardware
* Database system for specific domains, include database for AI,  time series database, etc
  

当前主要研究兴趣：

```warning
#### 分布式数据库事务处理与分布式一致性理论

数据库的ACID包括了一些传统的事务理论，包括事务的隔离级别、可恢复行等。
但在分布式数据库中，由于（1）Partition的引入 （2） 副本的引入。 （3）多点写入（Multi-master）（4） 多版本技术 等这使得事务的并发控制技术、日志技术以及分布式共识技术三者融合起来。
很多概念变得更加复杂难懂，有些问题用传统的理论解释乏力或者难以有效解释。数据库结合分布式，云场景，Serverless计算等新的场景，也出现了诸多新型的事务
与分布式一致性问题，这些问题常常困扰对系统架构、技术等诸多方面。我们对这些新型场景与环境下的数据库系统进行探索。
典型的例子如： 分布式数据库中快照隔离到底和分布式系统中的一致性有何关系，
和数据库中的隔离级别又有何关系？分布式系统中研究的如SNOW理论和数据库中的一致性是和关系？Multi Master事务处理在云数据上的挑战等。
Spanner在最高的一致性上作出了标准（可线性化+可串行化），但这仅是冰山一角，冰山下绝大多数实际系统中使用的一致性标准要低很多，而这方面的理论支持比较薄弱。
我们主要研究三个方向的内容，分布式事务处理技术、分布式一致性协议、事务处理与分布式一致性协议的结合。
相关理论有助于我们在系统设计时更容易的定位与思考问题。 
```



```warning
#### 新体系结构下的数据库系统

云数据库、新型硬件环境下的出现使得数据库面临的软硬件环境发生了大量变化。从体系结构角度看：数据库系统的存储层次从简单的内存缓冲区与本地文件变化为多层次的混合Hierarchy，包括内存，持久化内存
、SSD、HDD、云存储等多种混合存储；从计算角度看，新的计算技术层出不穷，如查询编译、向量执行、异构计算(多核CPU、GPU、FPGA)、协程、无锁编程技术等，这些计算在TP系统、OLAP及No-SQL系统中都有应用环境与空间，No-SQL数据库进行研究。PCIE、新型高速网络如RDMA会作为数据通道
存储与计算中间，是更加智能化的资源调度。我们主要针对多层混合介质的存储与并行查询计算技术展开研究。
<img src="https://github.com/dase314/dase314.github.io/blob/main/images/new_db_architecture.PNG?raw=true">
```

```warning
#### 面向垂直领域的新型数据管理系统

面向物流、电力、金融、人工智能等数据与业务特征的新型数据管理系统。
当下数据管理系统缺乏AI应用构建过程中的数据管理语言与方法。
数据库系统承包了应用软件的数据管理功能，虽然数据的收集和管理已经有系统工具，
数据价值发挥还缺少系统工具, 从数据到AI应用这个过程还没有相应的系统支撑。
AI应用构建涉及特征提取、特征管理、模型管理、模型训练、模型部署等环节，
这些过程环环相扣。而且模型之间、模型数据之间还相互关联，
涉及大量的中间数据，包括特征、向量化结果等等，需要系统化的管理。
目前没有系统能对这些过程和中间数据进行有效统一管理。
```



## The license

The theme is available as open source under the terms of the MIT License
