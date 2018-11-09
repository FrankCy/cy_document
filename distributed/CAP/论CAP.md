# CAP相对论 #
### 原创： 右军 ###
#### 简介 ####
CAP理论，被戏称为【帽子理论】，由```Eric Brewer```在ACM研讨会上提出，而后CAP被奉为```分布式领域的重要理论```。<br/>
分布式系统CAP理论 —— 首先，将分布式系统的三个特性进行归纳：
- （C）一致性<br/>
分布式环境下，所有数据的备份和存储，在同一情况（时间，系统操作）下值是相同的。（例如所有节点访问一份最新的数据副本，结果相同）

- （A）可用性<br/>
集群环境下，某一节点故障后，集群是否还能正常响应所有客户端的读写请求。（对数据更新具备高可用性）

- （P）分区容忍性<br/>
以实际效果而言，分区相当于对通信的时限要求。系统如果不能在时限内达成数据一致性，就意味着发生了分区的情况，必须就当前操作在C和A之间做出选择。

- 概述<br/>
```高可用、数据一致性是很多系统设计的目标，但是分区又是不可避免的事情。```

#### CA、CP、AP ####
- CA without P<br/>
如果不要求P（不允许分区），则C（强一致性）和A（可用性）是可以保证的。但其实分区不是你想不想的问题，而是始终会存在，因此CA的系统更多的是允许分区后各子系统依然保持CA。<br/>
[CA without P](https://mmbiz.qpic.cn/mmbiz/nhlGsolibOWF1lDZBUOHnQ3EKCIwib90nCC3t0cSTtaIc3nY5wh7KU8Z2fnk1e1fTA61ibgAOUPKFf379dY1qVQWQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

- CP without A<br/>
如果不要求A（可用），相当于每个请求都需要在Server之间强一致，而P（分区）会导致同步时间无限延长，如此CP也是可以保证的。很多传统的数据库分布式事务都属于这种模式。<br/>
[CP without A](https://mmbiz.qpic.cn/mmbiz/nhlGsolibOWF1lDZBUOHnQ3EKCIwib90nCpt1vPtnSU6sH3KIbJWd95tLtFKHQUNQ29uD4LicP3mZxK3WtOGqib4Fw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

- AP without C<br/>
要高可用并允许分区，则需放弃一致性。一旦分区发生，节点之间可能会失去联系，为了高可用，每个节点只能用本地数据提供服务，而这样会导致全局数据的不一致性。现在众多的NoSQL都属于此类。<br/>
[AP without C](https://mmbiz.qpic.cn/mmbiz/nhlGsolibOWF1lDZBUOHnQ3EKCIwib90nC4D2NnfnozOTQAh8o9zZxYWezju5MichNX8h88BPNGsUNbia1a88lAbkA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

[以上图片的PDF位置请点击此链接获取](http://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)

#### CAP 理论的证明 ####
该理论由brewer提出，2年后就是2002年，Lynch与其他人证明了Brewer猜想，从而把CAP上升为一个定理。但是，它只是证明了CAP三者不可能同时满足，并没有证明任意二者都可满足的问题，所以，该证明被认为是一个收窄的结果。
Lynch的证明相对比较简单：采用反证法，如果三者可同时满足，则因为允许P的存在，一定存在Server之间的丢包，如此则不能保证C，证明简洁而严谨。<br/>
- 该证明中，对CAP的定义进行了更明确的声明：
  + C: <br/>
  一致性被称为原子对象，任何的读写都应该看起来是“原子“的，或串行的。写后面的读一定能读到前面写的内容。所有的读写请求都好像被全局排序一样。
  + A: <br/>
  对任何非失败节点都应该在有限时间内给出请求的回应。（请求的可终止性）
  + P: <br/>
  允许节点之间丢失任意多的消息，当网络分区发生时，节点之间的消息可能会完全丢失。

#### CAP 理论澄清 ####
[CAP理论十二年回顾："规则"变了]一文首发于  Computer 杂志，后由InfoQ和IEEE联合呈现,非常精彩[2]，文章表达了几个观点。





