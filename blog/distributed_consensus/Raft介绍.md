### **一、Raft简介**

1. 共识：

   （1）概念：

   ​		允许一组机器作为一个一致的组工作，这个组可以容忍某些成员的故障，具有很高的可用性和可靠性，是构建容错系统的基础。

   （2）应用：

   ​		共识可以解决的问题是：面对故障，如何在共享状态上达成一致？这个问题出现在需要提供高可用性且不牺牲一致性的各种系统中。因此，共识几乎用于所有确保一致状态的大型存储系统中。

2. Raft 是一种使用复制状态机[二、概念第5点]来管理日志复制的分布式共识（简单来说，多个节点对某个事情达成一致的看法，即使出现了某些节点故障）算法，可在分布式系统中保证多个节点的状态一致，即分布式共识协议的一种。

3. Raft算法的简要过程：

   （1）Raft算法首先选举一个Leader，让Leader完全负责管理复制的日志来实现一致性。Leader从客户端接收日志项，然后复制到其他服务器上，并告诉其他服务器何时可以安全地将日志项提交，并应用于状态机。这就保证了所有节点的状态能够达成一致。

4. Paxos两个缺点：（1）难理解。（2）没有提供一个足够好的用来构建一个现实系统的基础。

5. Raft算法的目标包括：

   （1）在各种条件下保证安全性，典型操作下保证可用性。

   （2）最主要的目标是可理解性，可以为系统构建和教学提供更好的基础。通过对完整的算法进行分解（Raft分离了Leader选举，日志复制和安全性），并且对状态空间进行缩减（Raft减少了不确定性的程度以及服务器彼此之间不一致的行为）来提高可理解性。

6. 实际系统的共识算法需要保证：

   （1）在网络延迟、分区、数据包丢失、重复和重新排序的情况下，不会返回错误的结果。

   （2）只要任何大多数服务器可以正常运行和相互通信，就可以正常工作。

   （3）不依赖于时间来确保日志的一致性。错误的时钟和极端的消息延迟只可能导致可用性的问题。

   （4）通常情况，大多数服务器运行正常就可以完成一个命令。少数慢的服务器不会影响系统整体性能。

7. Raft还解决了基于共识的完整系统所需的所有问题。相比于其他算法更能够满足实际系统的需要，比如提出了集群的动态调整策略以及和客户端的交互等。

   

### **二、概念**

1. 一个Raft集群包含若干个服务器节点，通常是5个，这允许整个系统容忍2个节点失效。即对于2*F+1个节点的集群，能够容忍F个节点同时出现故障。

2.  三种状态：通常情况下，系统中只有一个Leader，其他都是Follower。Follower只是被动的接收并响应请求。

   （1）**Leader（领导者）**（Only one）：响应客户端的请求，同步数据。

   （2）**Candidate（候选者）**：Leader选举时的一个状态，获得大多数选票就可担任领导者。

   （3）**Follower（跟随者）**：初始启动状态，接收日志同步请求并响应。

<img src="https://i.loli.net/2021/09/03/w5soXV1YNHUxFmS.png" style="zoom:50%;" />

​    3. 三种远程调用:

|       RPC种类       |                     作用                      |
| :-----------------: | :-------------------------------------------: |
|   RequestVote RPC   |               Candidate收集选票               |
|  AppendEntries RPC  |       Leader进行日志复制，或者heartbeat       |
| InstallSnapshot RPC | Leader使用该RPC来发送快照给过于落后的Follower |

   4. 任期（Term）：

      （1）在Raft中，时间被分为一个个任意长度的任期，每一个任期都是从Leader选举开始的，选举结束后开始正常处理客户端的请求。也会存在某些选票瓜分的情况，在这种情况下，这一个任期会以没有选出Leader而结束，下一个任期会很快开始。Raft保证了在给定的一个任期内只会有一个Leader。（下图来自[1]）

      <img src="https://i.loli.net/2021/09/03/uilef3VoTgUWDtY.png" alt="img" style="zoom:50%;" />

      （2）任期在Raft中充当逻辑时钟的作用，允许服务器查明一些过期的信息。比如Leader看到更新的任期，就会转变为Follower；一个节点收到了之前任期的请求，会直接拒绝该请求。

5. 复制状态机（Replicated state machines）：

   通常使用复制日志来实现。每个服务器存储一个包含一系列具有相同顺序命令的日志，状态机按顺序执行这些命令，由于状态机是确定的，因此每个状态机都计算相同的状态和相同的输出序列。（下图来自[1]）

   **相同的初状态 + 相同的输入 = 相同的结束状态**

<img src="https://i.loli.net/2021/09/03/xD37zGNlp2RXIZo.png" alt="img" style="zoom:50%;" />

6. 算法介绍主要分为六部分:

​	（1）**Leader选举（Leader election）、日志同步（Log replication）、安全性（Safety）**、日志压缩（Log compaction）、成员变更（Membership change）、客户端交互（Client interaction）。

​	（2）前三个部分是主要Raft算法的分解，后面几个是对于构建现实的容错系统提供的完整的算法保证。



### **三、Leader选举：**

#### （一）触发选举：

1. 心跳机制：

   （1）心跳是不含日志项内容的一种AppendEntries RPC。

   （2）Leader周期性地向所有的Follower发送心跳，以此来维护自己的权威。

2. 当集群启动时，每个节点都是Follower的角色。

3. 在集群启动后，或者当前Leader节点失败时，如果一个Follower在一个随机的选举超时时间（election timeout）内没有收到来自Leader的心跳，也就是选举超时，此时他就会认为系统中没有可用的Leader，那么就发起选举，选出新的Leader。

4. 使用随机的方式（随机的选举超时时间）简化了Raft领导者选举。

#### （二）选举步骤

1. Follower选举超时后，增加当前任期，并转换为Candidate状态。

2. 给自己投票，并给其他节点发送RequestVote RPC，请求投票。

3. 投票结果：

​		a.赢得大多数选票，成为Leader。

​		b.被告知有其它节点成为了Leader，转换为Follower状态。

​		c.没有节点获得大多数选票（多个Candidate瓜分选票），等待超时重新选举。(下图来自[3])

<img src="https://i.loli.net/2021/09/03/yQkLxNPe93M8tur.png" alt="img" style="zoom:50%;" />

<img src="https://i.loli.net/2021/09/03/D2FxC3IegB9wERv.png" alt="img" style="zoom:50%;" />

#### （三）其他

1. 一个任期内一个节点只能投一票，按照先来先服务的原则（每个任期内投票总数一定，则获得大多数选票的只能有一个Candidate）。

2. 只能投给比自己新的节点（任期、日志至少和自己相等，或者更新）。Raft确保选举出的新的Leader上一定有最新的已经提交的日志项。

3. 针对上述（二）3c 选票瓜分的情况，通过使用随机的选举超时时间，就可以防止每次都出现选票瓜分的情况，而不能选出Leader的情况。

   

### **四、日志复制**

#### （一）日志结构[1]

<img src="https://i.loli.net/2021/09/03/tDgRv3YMVkulObB.png" alt="img" style="zoom: 50%;" />

<img src="https://i.loli.net/2021/09/03/BDUhqp6XW4fwJ2M.png" alt="img" style="zoom:50%;" />

#### （二）日志复制步骤

1. 一旦Leader被选举出来，就开始为客户端提供服务。客户端发送请求到Leader上。客户端的每个请求都包含一条需要被复制状态机执行的指令。
2. Leader把这条指令作为一条新的日志项附加到本地的日志中，并且并行地发起AppendEntries RPC给其他服务器，复制日志项。
3. 当Leader认为这个日志项已经被安全地复制（下文提到）的时候，就会将这条日志apply到他的状态机中，并返回给客户端。
4. 如果Follower运行缓慢或者崩溃、网络丢包，Leader会不断重试AppendEntries RPC，直到所有的Follower最终都存储了所有的entries。

<img src="https://i.loli.net/2021/09/06/jTadLYnWseJxi7Z.png" style="zoom:50%;" />

#### （三）日志提交

1. Leader决定何时把日志项应用到状态机中是安全的，这种日志项也称为已提交。Raft算法保证所有已提交的日志项都是持久化的并且最终会被所有可用的状态机执行。
2. 在Leader将日志项复制到多数的服务器上时，日志项就会被提交。
3. Leader维护最大的已经提交的日志项的索引，并在AppendEntries RPC中附加这个索引，当其他服务器收到消息时，就会确定Leader的提交位置（这是因为，只要Leader中的某个日志项被提交，那么它之前的所有日志项都会是已提交的状态），从而改变自己状态机中的状态。

#### （四）日志匹配属性

1. 如果不同日志中的两个日志项有着相同的索引和任期号，则它们所存储的命令是相同的。（相同任期内，对于给定的index，只创建一条日志项）

2. 如果不同日志中的两个日志项有着相同的索引和任期号，则它们之前所有的日志项都相同。（通过一致性检查来保证）

#### （五）AppendEntries 一致性检查

1. 内容：

   ​		在发送AppendEntries RPC时，Leader会把新日志项之前日志项的索引和任期号包含在里面。如果Follower发现在它的日志中找不到对应的日志项，那么就拒绝接收这个新日志项。

   ​		而Leader发现拒绝接收的消息，就会逐一向前查找日志项，并发送AppendEntries RPC，直到最终Follower找到与Leader对应的相同位置，并将其不对应的日志项全部用Leader对应的日志项覆盖。(下图来自[1])

   <img src="https://i.loli.net/2021/09/03/tL5VEvyKs3USHNB.png" alt="img" style="zoom:50%;" />

2. 归纳步骤：

   ​		一开始空的日志状态一定满足日志匹配的特性，然后通过一致性检查在日志扩展的时候维护了日志匹配的特性，所以当Leader收到AppendEntries RPC返回成功的消息后，就意识到Follower与自己的日志匹配成功了。

3. Follower日志可能会出现的情况：(下图来自[1])

   <img src="https://i.loli.net/2021/09/04/WJ5spvRMIkH69LD.png" style="zoom:50%;" />

4. Leader通过强制Followers复制它的日志来处理日志不一致的情况。

#### （六）优化匹配

<img src="https://i.loli.net/2021/09/06/mwx8Gpu9Fdi2vYI.png" style="zoom:50%;" />

​		算法可以通过减少被拒绝的AppendEntries RPC的次数来优化。在AppendEntries RPC被拒绝的时候，Follower可以直接把冲突任期最早的索引值发送给Leader。将原本的每个entry冲突发送一次 RPC 变成每个任期发送一个 RPC。大大降低了发生冲突时系统通讯的RPC 次数。 (6>3)



### **五、安全性**

#### （一）提出五种安全性原则

| 性质                                       | 描述                                                         | 问题                        | 解决                   |
| ------------------------------------------ | ------------------------------------------------------------ | --------------------------- | ---------------------- |
| 选举安全原则（Election Safety）            | 一个任期（term）内最多允许有一个领导人被选上                 | Split Vote                  | 随机选举时间，多次选举 |
| 领导人只增加原则（Leader Append-Only）     | Leader永远不会覆盖或者删除自己的日志，它只会增加日志项       | 日志不一致                  | 强制Follower与其一致   |
| 日志匹配原则（Log Matching）               | 如果两个日志在相同的索引位置上的日志项的任期号相同，那么我们就认为这个日志从头到这个索引位置之间的日志项完全相同 | 日志不一致                  | 日志复制，一致性检验   |
| 领导人完全原则（Leader Completeness)       | 如果一个日志项在一个给定任期内被提交，那么这个日志项一定会出现在所有任期号更大的领导人中 | 无法判断某个entry是否被提交 | 选举限制+推迟提交      |
| **状态机安全原则（State Machine Safety）** | **如果一个服务器已经将给定索引位置的日志项应用到状态机中，则所有其他服务器不会在该索引位置应用不同的日志项** | 反证法                      |                        |

#### （二）选举限制

1. 问题：为什么不能直接提交之前任期的日志项？(下图来自[1])

<img src="https://i.loli.net/2021/09/03/JhdbC54U3RX8jso.png" alt="img" style="zoom:50%;" />

​		新的Leader不能通过某条日志项被保存到大多数的服务器上来断定它是否是已提交状态。

​	（1）选举限制：选举至少比自己新的节点成为Leader。

​	（2）延迟提交：Raft 从来不会通过计算复制的数目来提交之前任期的日志条目。只有Leader当前任期的日志项才能通过计算数目来进行提交。

​	（3）比如，上图中出现了一个比较复杂的情况。在（a）中， Leader S1在Term2时只写日志到了S1和S2。在（b）中, S5 成为了Term3的Leader，而日志只存储在自己的节点上就宕机了。后来，在（c）中，S1 成为了Term4的Leader，并把Index = 2的日志复制到了S3，但是没有完成committed。在（d）中，S1宕机了，S5重新当选，并把Term3的日志复制到所有节点。这就可以看出，被复制到大多数节点上的日志项也有可能被覆盖。

#### （三）时间和可用性

1. Leader选举对时间的要求：

   > 广播时间  <<  选举超时时间  <<  平均故障时间间隔
   >
   > 广播时间：发送RPC往返时间
   >
   > 平均故障时间：一台服务器两次故障之间的时间间隔 

2. 说明：

   （1）第一个<<，保证Leader能够通过发送心跳来阻止Follower开始新的选举。

   （2）第二个<<，保证系统稳定运行。

#### （四）宕机异常

1. 从节点失效：主节点同时兼顾读写操作，集群具有一致性和可用性。集群没有出现故障问题，对于失效后恢复的从节点，接收来自Leader的心跳信息，恢复数据，保证同步。

2. 主节点失效：其余节点超时重新选举，选出新的Leader。当主节点重新进入集群，接收心跳信息成为Follower。

#### （五）网络分区

1. AB | CDE发生网络分区

   （1）假设有ABCDE  五个节点。开始时A为Leader。

   （2）发生了网络分区。AB不能与CDE通信。

   （3）C被选为Leader，出现两个Leader。（brain split）

   （4）此时，A不能获得大多数回复，不会commit。

   （5）故障恢复后，AB变为Follower。      

    <img src="https://i.loli.net/2021/09/03/EfC6NOvPRVZ4Xnz.png" style="zoom:50%;" />

   ​               **！！！注意**：可能读到过时的数据。

   ​                考虑：（1）验证数据，获得大多数节点确认。

   ​                           （2）多数节点不可达，自动转为Follower。

2. Pre-Vote 预投票算法解决了分区服务器在重新加入群集时破坏群集的问题。每次发起选举之前，先发起 pre-vote， 如果获得大多数 pre-vote 选票，再增大 term 发起选举 vote 投票。（博士论文[11]的优化）

### **六、日志压缩**

​       在实际的系统中，不能让日志无限增长，否则会影响性能，系统的可用性也会下降。而通过snapshot来解决是最简单也是最常用的方式。(下图来自[1])

<img src="https://i.loli.net/2021/09/03/MHX1RjpaftGOlEV.png" alt="img" style="zoom:50%;" />

1. 所有的节点都可以对已经提交的日志记录进行snapshot。

2. 如果某个Follower的日志落后太多,发送的log entry被丢弃，或者新添加了节点，Leader会使用InstalledSnapshot RPC发送snapshot。

3. 可以在日志达到某个固定的大小时做一次snapshot。

4. 可以通过使用写时复制（copy-on-write）技术避免snapshot过程影响正常日志同步。

   

### **七、成员变更**

​      在实际中，我们会经常增加、删除节点、更改配置，例如，替换掉那些崩溃的机器等。因此采用自动改变配置并且把这部分加入到了 Raft 一致性算法中。

#### （一）双多数派问题

​	成员变更过程的某一时刻，可能出现在Cold和Cnew中同时存在两个不相交的多数派，进而可能选出两个Leader，形成不同的决议，破坏安全性。(下图来自[1])

<img src="https://i.loli.net/2021/09/03/iNXPm1EZB87WlIV.png" alt="img" style="zoom:50%;" />

#### （二）两阶段的成员变更方法

​	集群先从旧成员配置Cold切换到一个过渡成员配置（Cold U Cnew），称为联合一致（joint consensus）。(下图来自[1])

<img src="https://i.loli.net/2021/09/03/HVqkb6KcXdfNw5R.png" alt="img" style="zoom:50%;" />

1. Raft两阶段成员变更过程如下：

   （1）Leader收到成员变更请求。

   （2）Leader在本地生成并写入一个新的log entry（Cold∪Cnew），然后将其复制到所有服务器中。在此之后新的日志同步需要保证得到Cold和Cnew两个多数派的确认。

   （3）Follower收到Cold∪Cnew的log entry后更新本地日志，并且此时就以该配置作为自己的成员配置。

   （4）如果Cold和Cnew中的两个多数派确认了Cold U Cnew这条日志，Leader就提交这条log entry。

   （5）接下来Leader生成一条新的log entry，其内容是新成员配置Cnew，同样将该log entry写入本地日志，同时复制到Follower上。

   （6）Follower收到新成员配置Cnew后，将其写入日志，并且从此刻起，就以该配置作为自己的成员配置。

   （7）Leader收到Cnew的多数派确认后，表示成员变更成功，后续的日志只要得到Cnew多数派确认即可。Leader给客户端回复成员变更执行成功。

#### （三）一阶段成员变更：

​	（1）成员变更限制每次只能增加或删除一个成员。(下图来自[11])

<img src="https://i.loli.net/2021/09/03/VZCyzBAxFU4vkpq.png" alt="img" style="zoom:50%;" />

​	（2）存在的问题：对于Raft来说，如果一个投票或者commit是在一个Quorum中做出的，那么后续的Quorum必须包含至少一个知道该决定的服务器。但是可能会存在两个并发并且相互竞争的更改，具有彼此不重叠的决定，从而导致了脑裂现象。反例见讨论[10]。

​	（3）解决方案：在Leader提交当前任期的日志项之前不能附加新的配置日志项。这个更改意味着需要拒绝或者延迟成员变更，直到提交no-op。



### **八、客户端交互**

1. 发送command到Leader: 

   - 如果客户端不知道当前系统的Leader，首先任意发送到其中一个节点。
   - 如果此节点不是Leader，就会重定向到Leader。
   - \##(守护线程、网关)观察者、ZooKeeper等

2. 如果Leader在执行Command之后，在返回响应之前崩溃:

   - Client再次发送Command。

   - 每一次操作立即执行，在它调用和收到回复之间只执行一次。（线性化语义Linearizable semantics，通过Command中唯一的id保证）

     

### **九、Multi-Raft**

#### （一）什么是multi-raft?

1. 了解了raft算法以后，在将其应用到企业应用中，发现raft算法在实现中存在一些弊端，比如，系统的性能受限于单机的性能（读写请求都由leader处理,存在性能瓶颈）等。

2. 而multi-raft，在整个系统中，把所管理的数据按照一定的方式切片，每一个切片的数据都有自己的副本，这些副本之间的数据使用Raft来保证数据的一致性，在全局来看整个系统中同时存在多个Raft-Group，如图所示。

3. Region:对数据的一个分区，被复制到多个节点上。

4. Raft Group:相同Region的一个集合，一个group里，就相当于是一个单个的raft算法，可以选择leader。（下图来自[8]）

   <img src="https://i.loli.net/2021/09/06/2UbXumgDpyP64vs.png" alt="TiKV region" style="zoom:50%;" />

5. Region的分区主要有两种策略：Hash（key的哈希函数） & range(按照key的顺序划分)

​    （1）range:（下图来自[8]）

​					优点:简单、支持range scan

​					缺点：顺序写热点问题

<img src="https://i.loli.net/2021/09/03/cTUGqoirHCNkK6S.png" alt="img" style="zoom:50%;" />

​	（2）Hash：(下图来自[8])

​					优点：对写压力大、随机读较友好

​					缺点：不能提供range scan

<img src="https://i.loli.net/2021/09/03/YKzpgiFTsVR1S2n.png" alt="img" style="zoom:50%;" />

​		！！！Think：可以考虑随机组合？

#### （二）multi-raft需要解决的一些核心问题

<img src="https://i.loli.net/2021/09/03/2HzNAqksfLy9a4u.png" alt="img" style="zoom:50%;" />

<img src="https://i.loli.net/2021/09/03/IXWmL9w75uUcfs3.png" alt="img" style="zoom:50%;" />

​		（上图来自于[12]）

​	（1）数据何如分片（hash & range ）

​	（2）分片中的数据越来越大 (需要分裂产生更多的分片，组成更多Raft-Group)

​	（3）分片的调度，让负载在系统中更平均（分片副本的迁移，补全，Leader切换等等）

​	（4）一个节点上，所有的Raft-Group复用链接（上面两图来自，batch up. 否则Raft副本之间两两建链，链接爆炸了）

​	（5）如何处理stale的请求（例如Proposal和Apply的时候，当前的副本不是Leader、分裂了、被销毁了等等）

​	（6）Snapshot如何管理（限制Snapshot，避免带宽、CPU、IO资源被过度占用）



### **十、参考资料：**

- 1. In Search of an Understandable Consensus Algorithm(Extended Version)  https://raft.github.io/raft.pdf
- 2. 动画Raft Understandable Distributed Consensus：http://thesecretlivesofdata.com/raft/
- 3. Raft详细描述动画：Raft Visualization:https://raft.github.io/
- 4. Raft算法详解：https://zhuanlan.zhihu.com/p/32052223
- 5. Raft一致性算法：https://lotabout.me/2019/Raft-Consensus-Algorithm/

- 6. Elasticell-Multi-Raft实现 https://zhuanlan.zhihu.com/p/33047950 
- 7. TIDB 架构及分布式协议Paxos和Raft对比 https://blog.51cto.com/liuminkun/2377029 
- 8. 基于 Raft 构建弹性伸缩的存储系统 https://pingcap.com/blog-cn/building-distributed-db-with-raft/ 
- 9. 什么是multi-Raft https://www.pianshen.com/article/12371692155/

- 10. 单服务器成员资格更改 https://groups.google.com/g/raft-dev/c/t4xj6dJTP6E

- 11. CONSENSUS: BRIDGING THEORY AND PRACTICE https://web.stanford.edu/~ouster/cgi-bin/papers/OngaroPhD.pdf

- 12. Scaling Raft https://www.cockroachlabs.com/blog/scaling-raft/
