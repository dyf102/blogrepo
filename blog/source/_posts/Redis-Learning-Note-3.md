title: Redis Learning Note 3
date: 2016-08-16 11:16:26
tags: Redis, Data structure
---

## Data Structure in Redis

1.	List (Double-linked list)
	Some commends: RPUSH, LPUSH
	common use: 
	1.	Remember the latestes Posts
	2. Communicate between processes(Queue)
	implementation: t_list.c
	E.g. LRANGE 0 9 to get the ten items from left side
	ltrim 0 9 remove elements outside the given range 
	
2.	Set(Unordered collections of string)  
	Some Commends: SADD,SMEMBER, SISMEMBER
	implementaion: t_set.c

3.	Sorted Set (Skip list and hash table)
	Every element in a sorted set is associated with a floating point value.
	E.g. zadd hackers 1940 'Alen Kag'
		zrange hackers 0 -1
		zrangebysome hackers -inf 1950
	Implementation: t_zset.c
4. HyperhyperLogs
	Data structure is used to count unique string using fixed amount memory(12 bytes at most), which is tradeoff bwteen  memory and precious (< 1 %)
	Some Commends: 
	PFADD hll a b c d
	PFCOUNT hll