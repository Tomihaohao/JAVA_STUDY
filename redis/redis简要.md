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

