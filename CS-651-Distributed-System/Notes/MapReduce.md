# Map Reduce


![alt text](./images/mapreduce_work.jpg)

看视频

Input 

Mapper

Combiner

Partitioning

Reducer

一台机器可以有多个Mapper，Mapper跟Reducer不会共存于同一台机器，Combiner是Mappper后的mini-Reducer，与Mapper存在于同一机器，一个Combiner可以对应多个Mapper.

Partitioning把相同的key都分配到同一个Reducer机器上，例如使用简单的hash function: key mod k

为什么 one mapper per node is not good?
为什么这样某个node死了，整个计算速度就会下降。
因为，别的node也在计算，如果是一个node多个mapers的话，我就能把这些mappers分配给不同的nodes，这样计算速度就能跟上原来没死的node.

How to do partitionning function?
