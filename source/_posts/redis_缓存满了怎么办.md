---
title: Redis缓存满了怎么办
date: 2021-11-14
categories:
- redis
tags:
- redis
---

🥡 在高并发场景，缓存被大量使用的情况下，不免会出现一种情况：缓存满了。
下面我们谈一下这样的情况怎么处理。

<!--more-->

### <font color=#11d17b>## </font><font face=黑体>缓存容量</font>

为了避免缓存过早被写满，我们需要根据应用数据访问特征和成本开销来综合考虑缓存容量的大小，一般会把缓存容量设置为总数据量的15%-30%。
> 命令：CONFIG SET maxmemory 4gb (4gb是举个🌰)

### <font color=#11d17b>## </font><font face=黑体>淘汰策略</font>

缓存写满是不可避免的，Redis有自己的缓存淘汰策略，在4.0版本之前有6中淘汰策略：
1. noeviction(默认策略): 若内存大小达到了maxmemory值，并不会淘汰数据，再有写请求时，Redis直接返回错误。
2. allkeys-lru: 所有key都是使用LRU算法进行淘汰。
3. volatile-lru: 所有设置了过期时间的key使用LRU算法进行淘汰。
4. allkeys-random: 所有key使用随机淘汰的方式进行淘汰。
5. volatile-random: 所有设置了过期时间的key使用随机淘汰的方式进行淘汰。
6. volatile-ttl: 所有设置了过期时间的key根据过期时间进行淘汰，越早过期越早被淘汰。

在4.0版本之后又增加了2种:

7. allkeys-lfu: 所有key都是使用LFU算法进行淘汰。
8. volatile-lfu: 所有设置了过期时间的key使用LFU算法进行淘汰。

#### <font color=#11d17b>### </font><font face=黑体>LRU算法</font>

我们先来讲讲LRU算法，LRU全称是 Least Recently Used, 即 按照最近最少使用的原则来筛选数据，最不常使用的数据会被筛选出来进行淘汰。

底层原理：
LRU用一个链表来维护数据，链表头部是MRU端，代表最最近常使用的数据；尾部是LRO端，代表最近不常用的数据。数据访问时根据移动链表来维护数据的最近常用性。

LRU算法有两个问题：
1. 用链表管理所有的缓存数据会带来额外的空间开销。
2. 大量数据访问时，链表的移动操作很耗时，会降低Redis的缓存性能。

在Redis中，对LRU算法做了简化:

Redis默认会记录每个数据最近一次访问的时间戳(由对象来维护{lru:时间戳})，淘汰数据时第一次会随机筛选出N个数据，放到一个候选链表当中，把时间戳最小的淘汰。
> N 用配置参数 maxmemory-samples 来设置

当再次淘汰数据时，Redis会挑选时间戳小于候选链表中最小时间戳的数据放到候选链表中，然后把时间戳最小的淘汰出去。

#### <font color=#11d17b>### </font><font face=黑体>LFU算法</font>

Redis从4.0版本之后增加了LFU策略，是对LRU策略的进一步优化。全称 Least Frequently Used。

核心思想:
根据key最近被访问的频率进行淘汰。即，优先淘汰最近访问频率小的数据，如果频率相同，则淘汰最近最少使用的数据。

底层原理：
把lru策略中的lru字段(24bit)差分成两部分，前16bit为ldt值来表示数据访问的时间戳，后8bit为counter值，表示数据的访问次数。

### <font color=#11d17b>## </font><font face=黑体>解决方案</font>
1. 如果一部分是热点数据，一部分是冷门数据这样的缓存分布情况，建议使用allkeys-lru或者allkeys-lfu策略。
2. 如果业务应用中数据访问频率相差不大，建议用allkeys-random策略。
3. 如果业务中有置顶的需求，可以使用volatile-lru或者volatile-lfu策略，同时不给这些置顶数据设置过期时间。