title: 'Redis: Learning notes 1'
date: 2016-06-06 21:32:15
tags: Redis, Datastructure, I/O Multiplexing
---
##Overview

Redis is a single thread, event loop based persistant cache server. 
why is it single thread? 
1. Avoid the cost of synchronization between threads.
2. It is a in-memory cache. The latency is most important.
Why does it use event loop?
1. Minimiza the cost of switching between task.
2. Try to deal with task as much as possible.
What is event loop?
A list of task. It uses Epoll or Kqueue to moniter the statue of file descripter in kernal space. When successful, epoll_wait will return number of fd ready for read or write I/O, or zero if no df ready during the timeout period.

````c
static int aeApiPoll(aeEventLoop *eventLoop, struct timeval *tvp) {
    aeApiState *state = eventLoop->apidata;
    int retval, numevents = 0;
    retval = epoll_wait(state->epfd,state->events,eventLoop->setsize,
            tvp ? (tvp->tv_sec*1000 + tvp->tv_usec/1000) : -1);
    if (retval > 0) {
        int j;
        numevents = retval;
        for (j = 0; j < numevents; j++) {
            int mask = 0;
            struct epoll_event *e = state->events+j;

            if (e->events & EPOLLIN) mask |= AE_READABLE;
            if (e->events & EPOLLOUT) mask |= AE_WRITABLE;
            if (e->events & EPOLLERR) mask |= AE_WRITABLE;
            if (e->events & EPOLLHUP) mask |= AE_WRITABLE;
            eventLoop->fired[j].fd = e->data.fd;
            eventLoop->fired[j].mask = mask;
        }
    }
    return numevents;
}
```

```
ae.c implements the Redis event loop
```
How to make it consistent? 
1.	RDB snapshot
-	single file for each time interval
con: 
	1.	good for diaster
	2.	maximize performance because it performs by a seperate process. 
	3. Faster recovery 
pron:
	1. Probably less the data between time period
	2. NOT good for minimizing the loss of data
	3. fork() will use a lot of memory, it will probably lead Outofmemory.
2. AOF
-	persist logs every write operation
con:
	1. More durable, it can have different fsync() policy	
	2. Rewrite the AOF in background when it is large.
pron:
	1.	Larger than RDB
	2. slower in different fsync() policy
Reference:
http://redis.io/topics/persistence

How to be fast?
1.	No-blocking set	
2.	All in memory
3.	Tcp keep alive 
4.	Tcp no delay(not need wait the packet to fill the cache)

Some Datastructure in redis
1.	list: double-linked list
adlist.h
```c
typedef struct listNode {
    struct listNode *prev;
    struct listNode *next;
    void *value;
} listNode;

typedef struct listIter {
    listNode *next;
    int direction;
} listIter;

typedef struct list {
    listNode *head;
    listNode *tail;
    void *(*dup)(void *ptr); //duplicate data function
    void (*free)(void *ptr); // free data function
    int (*match)(void *ptr, void *key); // compare data function
    unsigned long len;
} list;
```
2.	skip-list: data stucture enables fast search in ordered linked list. It achives O(log(n)) search and delete.
![alt text](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2c/Skip_list_add_element-en.gif/500px-Skip_list_add_element-en.gif "Skip list")

Redis' implementation can be found 
https://github.com/antirez/redis/blob/unstable/src/t_zset.c
