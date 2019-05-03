# redis 简要
一般而言redis 在JAVA WEB中主要是两个应用场景
- 缓存常用数据
- 在需要告诉读写的场景下 快速读写

## java 中使用redis
```java
Jedis jedis = new Jedis("localhost",6379);
int i=0;
try{
    long start = System.currentTimeMillis();
    while(true){
        long end = System.currentTimeMillis();
        if(end-start>=1000){
            break
        }
        i++
        jedis.set("test"+i,i+"");
    }
}finally{
    jedis.close()
}


//jedis 连接池
JedisPoolConfig poolCfg = new JedisPoolConfig();
poolCfg.setMaxIdle(50) // 最大空闲数
poolCfg.setMaxTotal(100) //最大连接数
poolCfg.setMaxWaitMillis(20000) //最大等待毫秒数
```
## redis六种数据类型
- string 字符串 整数 浮点型
- list 双向链表
- SET 集合 无序的 独一无二的
- HASH 类似于map
- ZSET 集合有序的
-HyperLogLog 基数 计算重复值

### 字符串 基本命令
- set key value 设置键值对
- get key 通过建获取值
- del key = 通过建删除值
- strlen key 求key指向的字符串长度
- getset key value 修改原来key的对应值，并将旧值返回
- get range key start end 获取子串
- append key value 将新的字符串 value 加入到原来 key 指向的字符串末尾

spring 中 使用 
- redisTemplate.opsForValue().set(key,value)
 - get(key)
 - size(key)
 - getAndSet(key)
 - delete(key)
 - append(key,value)

### 哈希 hash 基本命令
- hdel key filed1 ...[filed2] 删除hash 结构中的某些字段
- hexists key filed 判断hash 结构中是否存在filed 字段 return 0 || 1
- hgetall key获取所有hash结构的值
- hincrby key filed increment 指定某一字段加上一个整数
- hincrbyfloat key filed increment 指定某一字段加上一个浮点数
- hkeys key 返回hash中所有的建
- hlen key 返回hash中的键值对的数量
- hmget key filed1 ...[filed2] 返回hash中指定键的值
- hmset key filed1 ...[filed2] hash结构设置多个键值对
- hset key filed value 在hash结构中设置键值对
- hsetnx key filed value 当hash结构中不存在对应的键 才设置值
- hvals key 获取我所有hash结构中的值

spring 中使用
redisTemplate 需要 序列化器 

- redisTemplate.opsForHash().putAll(key,map); =hmset
- put(key,filed1,filed2) = hset
- hasKey(key,filed) = hexists key filed
- entries(key) = hgetall
- increment(key,filed,int value) = hincrby
- increment(key,filed,float value) = hincrbyfloat
- values(key) = hvals
- keys(key)  = hkeys
- multiGet(key,List key) = hmget
- putIfAbsent(key,filed1,filed2) = hsetnx
- delete(key) = hdel

### 链表 Link 基本命令
redis 中的链表是双向链表 可以分为左右操作
- lpush key node1 ...[node2] 把节点node1 加入到链表的最左边
- rpush key node1 ...[node2] 把节点node2  加入到链表的最右边
- lindex key index 读取下标为index 的节点
- llen key 求链表的长度
- lpop key 删除左边第一个节点并将其返回
- rpop key 删除右边第一个节点并将其返回
- linsert key before|after pivot node 插入一个节点node,并且可以指定在值为pivot 的节点的前面或者后面
- lpushx list node 插入节点node并且作为从左到右的第一个节点
- rpush list node 插入节点node 并且作为从右到左的第一个节点
- lrange list start end 获取从start 到end 的节点值
- lrem list count value 如果count 为 0 则删除所有值等于value 的节点，如果count 不为0 ，则先对count 取绝对值 ，然后从左到右删除不大于 count绝对值 个等于value 的节点
- lset key index node 设置列表下标为index 的节点值为node
- ltrim key start stop 修剪列表只保留 strat 到stop的区间节点其余的都删除掉
- blpop key timeout 移除并获取列表的第一个元素，如果列表没有元素会阻塞
- brpop key timeout 移除并获取列表的最后一个元素，如果列表没有元素会阻塞
- rpoplpush key src dest 从左到右 的顺序 将一个链表的最后一个元素移除，并插入到目标链表的最左边

spring中使用

- redisTemplate.opsForList().leftPush(list,node)
- opsForList().leftPushAll(list,nodelist)
- opsForList().rightPush(list,node)
- opsForList().rightPushAll(list,nodelist)
- opsForList().index(list,0) 获取下标为0 的节点
- opsForList().size(list) 获取链表长度
- opsForList().leftPop(list) 从左边弹出一个节点
- opsForList().rightPop(list) 从右边弹出一个节点
- opsForList().set(list,0,value) 给链表下标为0的节点设置新值

### 集合 ZSET 基本命令

