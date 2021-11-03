---
title: 缓存雪崩、击穿、穿透
date: 2021-11-02
categories:
- redis
tags:
- redis
---

💣 生产环境中经常面临缓存异常的三个问题，分别是缓存雪崩、缓存击穿和缓存穿透。问题一旦产生，大量请求积压到数据库层，甚至会产生宕机。

<!--more-->

下面我们分别谈一下：

### <font color=#11d17b>## </font><font face=黑体>缓存雪崩</font>

缓存雪崩是指大量的应用请求无法在Redis缓存中进行处理，紧接着，应用将大量请求发送到数据库层，导致数据库层的压力激增。关键点：大量key失效。

通常有两个原因：

#### <font color=#11d17b>### </font><font face=黑体>原因一</font>

缓存中有大量的数据同时过期，导致大量请求无法得到处理。

#### <font color=#11d17b>### </font><font face=黑体>解决方案</font>

1. 避免给大量的数据设置相同的过期时间。如果业务层要求某些数据同时失效，可以设置失效时间时进行微调，比如失效时间上添加随机数。
2. 服务降级，针对不同的数据采取不同的处理方式
    - 访问非核心数据时，暂时停止访问缓存，直接返回预定义信息或错误信息。
    - 访问核心数据时，仍然查询缓存，如果缓存失效则查询数据库。

#### <font color=#11d17b>### </font><font face=黑体>原因二</font>

Redis缓存实例发生故障宕机了，无法处理请求

#### <font color=#11d17b>### </font><font face=黑体>解决方案</font>

1. 在业务系统中实现服务熔断或请求限流机制。
    - 服务熔断机制是指暂停对缓存系统和数据库的访问，直接return。
    - 限流是指在前端限制每秒请求系统的次数。
2. 事先预防。
    - 通过主从节点的方式构建Redis缓存高可靠集群。如果Redis缓存的主节点宕机了，从节点可以切换为主节点，继续提供缓存服务。

### <font color=#11d17b>## </font><font face=黑体>缓存击穿</font>

缓存击穿是指，针对某个访问非常频繁的热点数据的请求，一旦这个key失效了，访问会堆积到数据库层，导致数据库压力激增。

#### <font color=#11d17b>### </font><font face=黑体>解决方案</font>

1. 对访问特别频繁的热点数据不设置过期时间。
2. 使用互斥锁。
    - 在缓存失效的时候，不是立即查询数据库，而是先使用Redis设置互斥锁(setnx设置一个mutex key)，当设置成功时，再查询数据库并设置缓存，否则就sleep之后再请求。

    ```java
    public static String getProductDescById(String id) {
        String desc = redis.get(id);
        // 缓存为空，过期了
        if (desc == null) {
            // 互斥锁，只有一个请求可以成功
            if (redis.setnx(lock_id, 1, 60) == 1) {
                try {
                    // 从数据库取出数据
                    desc = getFromDB(id);
                    redis.set(id, desc, 60 * 60 * 24);
                } catch (Exception ex) {
                    LogHelper.error(ex);
                } finally {
                    // 确保最后删除，释放锁
                    redis.del(lock_id);
                    return desc;
                }
            } else {
                // 否则睡眠1000ms，接着获取锁
                Thread.sleep(1000);
                return getProductDescById(id);
            }
        }
    }
    ```

### <font color=#11d17b>## </font><font face=黑体>缓存穿透</font>

缓存穿透是指要访问的数据既不在Redis缓存中，也不在数据库中，大量请求给缓存和数据库带来巨大压力。

#### <font color=#11d17b>### </font><font face=黑体>原因一</font>

业务层误操作，缓存中的数据和数据库中的数据被删除了。

#### <font color=#11d17b>### </font><font face=黑体>原因二</font>

恶意攻击，专门访问数据库中没有的数据。

#### <font color=#11d17b>### </font><font face=黑体>解决方案</font>

1. 缓存空值或协商好的缺省值。这样直接返回，就不用访问数据库了。
2. 使用布隆过滤器快速判断数据是否存在，避免查询数据库。
    - 原理是将数据的多个哈希值存放到一个足够大的bit数组中，查询时只要有一个哈希值不存在则返回，完成快速检测。
3. 在请求入口的前端进行请求检测。
    - 把请求参数不合理、请求参数是非法值、请求字段不存在等恶意请求直接过滤掉。





