# 胡卉芪 Huiqi Hu

<img width="120px" src="https://github.com/dase314/dase314.github.io/blob/main/images/dase_logo.PNG?raw=true">
<img width="150px" src="https://github.com/dase314/dase314.github.io/blob/main/images/sch_logo.PNG?raw=true">


##  个人信息


中国 华东师范大学 数据科学与工程学院, 副教授， 主要从事数据库系统研究。 [知乎主页](https://www.zhihu.com/people/hq-hu)

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
但在分布式数据库中，由于（1）Partition的引入 （2） 副本的引入。 （3） 多版本技术 等这使得事务的并发控制技术、日志技术以及分布式共识技术三者融合起来。
很多概念变得更加复杂难懂，有些问题用传统的理论解释乏力或者难以有效解释。数据库结合分布式，云场景，Serverless计算等新的场景，也出现了诸多新型的事务
与分布式一致性问题，这些问题常常困扰对系统架构、技术等诸多方面。我们对这些新型场景与环境下的数据库系统进行探索。
典型的例子如： 分布式数据库中快照隔离到底和分布式系统中的一致性有何关系，
和数据库中的隔离级别又有何关系？分布式系统中研究的如SNOW理论和数据库中的一致性是和关系？Multi Master事务处理在云数据上的挑战等。
Spanner在最高的一致性上作出了标准（可线性化+可串行化），但这仅是冰山一角，冰山下绝大多数实际系统中使用的一致性标准要低很多，而这方面的理论支持比较薄弱。
我们主要研究三个方向的内容，分布式事务处理技术、分布式一致性协议、事务处理与分布式一致性协议的结合。
相关理论有助于我们在系统设计时更容易的定位与思考问题。 


```warning
#### 新型存储、计算环境下数据系统

云数据库，新型硬件环境下的数据库的都使得数据库面临新的设计与优化。我们主要针对集中新型硬件与设施，包括持久化内存
高速网络(RDMA)与云存储，针对云环境下TP系统，混合存储介质下OLAP及No-SQL存储系统，No-SQL数据库进行研究
，也基于RDMA和NVM新硬件特性实施具有新型架构与特征的数据库系统。
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
目前没有系统能对这些过程和中间数据进行有效统一管理，
AI应用的构建和维护都是碎片化的，开发成本居高不下。
```



## The license

The theme is available as open source under the terms of the MIT License
