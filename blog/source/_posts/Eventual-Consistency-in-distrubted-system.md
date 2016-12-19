layout: blog
title: Eventual Consistency in distrubted system
date: 2016-12-16 22:49:37
tags: Distributed system
---
-	Why is consistency such a big deal in a distributed system
-	Why can reaching consistency be so slow in a distributed system
-	What components in a cloud are impacted by consistency issues 

#### CAP theorem:
```
"We can only two of the Consistency, Available and Partition Tolerance"
```
**Consistency**: all nodes see the same data at the same time.
**Availability**: a guarantee that every request receives a response.
**Partition tolerance**: system continues to operate despite arbitrary message loss or failure of part of the system.

### Sharding
Example:
1.	single client --> single DB 
	
	Consistency
	Partition tolerance
 	Availability

2.	Sharding by hashing
 
client
|  \
| 	 \
DB	  DB

	Same Partition tolerance as single node but it improves the scalability and the drop of node can only lead service degradation.
Example:
```
	distributed memcached, redis
	traditional db sharding
	Bigtable
	hypertable
	Hbase
	MongoDB
	Redis
```
3.	Muti-version read and write

clients have to wait for write operation in mutiple databases finished and the write operation success only when all the replicas are wroten successfully. Therefore the availability of write operation decreases but increase under read operation, which means suitable for more read and less write circumstance. 

### Clustering
__ BASE: Basically Availability Soft-state Eventual Consistency. __
The eventually consistency means as the data is replicated, the latest version of something is on some nodes in the cluster but the older version are still out there on other nodes but eventually all the nodes will see the same version.

More specifically: R=read replica count W=write replica count N=replication factor Q=QUORUM (Q = N / 2 + 1)

If W + R > N, you will have consistency
W=1, R=N
W=N, R=1
W=Q, R=Q where Q = N / 2 + 1

[cassandra document](https://wiki.apache.org/cassandra/ArchitectureOverview)
[reference](https://zhuanlan.zhihu.com/p/20399316)
 