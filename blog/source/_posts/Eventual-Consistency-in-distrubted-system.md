layout: blog
title: Eventual Consistency in distrubted system
date: 2016-12-16 22:49:37
tags: Distributed system
---
-	Why is consistency such a big deal in a distributed
system
-	Why can reaching consistency be so slow in a
distributed system
-	What components in a cloud are impacted by
consistency issues 

CAP theorem:
"We can only two of the Consistency, Available and Partition Tolerance" 

Consistency: all nodes see the same data at the same time.
Availability: a guarantee that every request receives a response.
Partition tolerance: system continues to operate despite arbitrary message loss or failure of part of the system.

Example:
1.	single client --> single DB 
	Consistency
	Partition tolerance
	~~Availability~~

2.	Sharding by hashing
 
client
| \
| 	\
DB	DB
	Consistent, P, A 
	Same Partition tolerance as single node but it improves the scalability and the drop of node can only lead service degradation.
Example:
	-	distributed memcached, redis
	-	traditional db sharding
	- 	Bigtable
	-  hypertable
	-  Hbase
	-  MongoDB
	-  Redis

3.	Muti-version RW
Continue..
[reference](https://zhuanlan.zhihu.com/p/20399316)
 