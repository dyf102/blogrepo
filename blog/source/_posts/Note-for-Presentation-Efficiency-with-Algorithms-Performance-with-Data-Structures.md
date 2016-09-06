layout: blog
title: 'Note for Presentation: Efficiency with Algorithms, Performance with Data Structures'
date: 2016-08-31 14:02:33
tags: C++, Algorithm, Data Structure
---

###[The Link of the presentation](https://channel9.msdn.com/Events/CPP/C-PP-Con-2014/Efficiency-with-Algorithms-Performance-with-Data-Structures)
Speakers: Chandler Carruth

This presentation involves some efficieny or perfomance issue in C++ programming when people use some patterns including misusing data structure like Map, unordered_map. 

---
Why performace is important?
Save battery, reduce energy consumption, reduce cost

---
####C++ does not give you performance, but the control of the performance
Efficiency through Algorithms
Example: sub-string search

1. N^2 algorithm
2. [KMP](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm)(skip-table)
3. [Boyer-moore](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_string_search_algorithm) 
To finish the task, use less work.

Always do less work

``` c++
vector<x> f(int n){
	vector<x> result;
	result.reserve(n); // to avoid more memory allocation
	for(int i = 0; i < n; i ++){
		result.push_back(x(i);
	}
	return result;
}
```
``` c++
X *getX(string key, unordered_map<string,unique_ptr<x>> &cache) {
	unique_ptr<X> &entry = cache[key]; // to avoid multiple time to acess the hash table
	if(entry){
		return entry.get();
	}
	entry = make_unique<X>(...);
	return entry.get();
}
```
Performance through Data structure
1. Just not use linked list
Issue: the cost of cache miss. 100 times more acess time than retrive from L1 or l2 cache.
std:: LIST 
Each step is a cache miss since next node is retrived by pointer.
Only use in the very rarely travese the list, but very frequently update.

---
std::map 
It's just linked list, oriented as a binary tree
All the downside of linkedlist

---
std::unordeded_map
buckets of key value pairs of each hash entry
These bucket are linked list
Essentially have some pointer chasing

A good hash table design

1.	No bucket, just open addressing
2. table stored as contiguous range of memory
3. use local probing on collisions to find an open slot in the same cache line
4. Keep both key and values small 

Data structure and algorithm
They are tightly coupled
Algorithms can also influence the data access pattern regardless of the data structure used.
Worse is better: bubble sort and cuckoo hashing
Bubble sort is quickest the sorting algorithm in small dataset.
cuckoo hashing :the basic idea of cuckoo hashing is to resolve collisions by using two hash functions instead of only one.
[Google Dense Hash map](http://goog-sparsehash.sourceforge.net/doc/dense_hash_map.html)