---
title: Jedis Usage
date: 2021-03-04 10:56:22 +0800
categories: [bigdata, jedis]
tags: [jedis]
---

[Jedis uses the full version of the tutorial](https://programmer.help/blogs/jedis-uses-the-full-version-of-the-tutorial.html)
[JedisPool optimization](https://partners-intl.aliyun.com/help/doc-detail/98726.htm)
Jedis Redis is the official recommendation of Java connection development tools.
# Basic Use
```
Jedis jedis = new Jedis("localhost", 6379);  //Specify Redis service Host and port
jedis.auth("xxxx"); //If the Redis service connection requires a password, make a password
String value = jedis.get("key"); //Accessing Redis Service
jedis.close(); //Close the connection after use
```
After the Jedis object is built, the Jedis underlying layer opens a Socket channel to connect to the Redis service. So after using the Jedis object, you need to call the Jedis.close() method to close the connection, rather than occupying system resources.

# Jedis connection pool
## non-distributed pool---JedisPool
When building connection pool objects, you need to provide configuration objects for pool objects and JedisPool Config (inherited from Generic Object Pool Config).
```
JedisPoolConfig config = new JedisPoolConfig();
config.setMaxIdle(8);
config.setMaxTotal(18);
// parameters : config, redishost, redisport, timeout, redispassword
JedisPool pool = new JedisPool(config, "127.0.0.1", 6379, 2000, "password");
Jedis jedis = pool.getResource();
String value = jedis.get("key");
......
jedis.close();
pool.close();
```
After using the Jedis connection pool, you must remember to return the connection to the connection pool every time you use the connection object. Jedis modified the close method.

## distributed pool---shardedJedisPool
```
private void initialShardedPool() {

	JedisPoolConfig config = new JedisPoolConfig();
	config.setMaxActive(20);
	config.setMaxIdle(5);
	config.setMaxWait(1000l);
	config.setTestOnBorrow(false);

	List<JedisShardInfo> shards = new ArrayList<JedisShardInfo>();
	JedisShardInfo infoA = new JedisShardInfo(HOST, PORT);
	infoA.setPassword("redis");
	shards.add(infoA);

	shardedJedisPool = new ShardedJedisPool(config, shards,
			Hashing.MURMUR_HASH, ShardedJedis.DEFAULT_KEY_TAG_PATTERN);
}
```

## Encapsulation
```java
public class RedisUtil{

    private static String REDIS_MAX_IDLE_DEFAULT = "5";
    private static String REDIS_MAX_TOTAL_DEFAULT = "10";
    private static String REDIS_MAX_WAIT_MILLIS_DEFAULT = "2000";
    private static String REDIS_TEST_ON_BORROW_DEFAULT = "true";

    private static volatile JedisPool jedisPool = null;
    private static volatile ShardedJedisPool shardedJedisPool = null;
     /**
     * 初始化非切片池(非分布式)
     */
    private static JedisPoolConfig initialPool() {
        // 池基本配置
        JedisPoolConfig config = new JedisPoolConfig();
        //指定连接池中最大空闲连接数
        config.setMaxIdle(Integer.valueOf(properties.getProperty(RedisConfig.REDIS_MAX_IDLE,
                REDIS_MAX_IDLE_DEFAULT)));
        //链接池中创建的最大连接数
        config.setMaxTotal(Integer.valueOf(properties.getProperty(RedisConfig.REDIS_MAX_TOTAL,REDIS_MAX_TOTAL_DEFAULT)));
        //设置创建链接的超时时间
        config.setMaxWaitMillis(Integer.valueOf(properties.getProperty(RedisConfig.REDIS_MAX_WAIT_MILLIS,REDIS_MAX_WAIT_MILLIS_DEFAULT)));
        //表示连接池在创建链接的时候会先测试一下链接是否可用，这样可以保证连接池中的链接都可用的
        config.setTestOnBorrow(Boolean.valueOf(properties.getProperty(RedisConfig.REDIS_TEST_ON_BORROW,REDIS_TEST_ON_BORROW_DEFAULT)));
        return config;
    }
    /**
     * 非切片池(非分布式)
     */
    public static Jedis getJedis() {
        if (null == jedisPool) {
            synchronized (RedisUtil.class) {
                if (null == jedisPool) {
                    JedisPoolConfig config = initialPool();
                    jedisPool = new JedisPool(config, "127.0.0.1", 6379, 2000, "password");
                }
            }
        }
        return jedisPool.getResource();
    }
    /**
     * 切片池(分布池)
     */
    public static ShardedJedis getShardedJedis() {
        if (null == shardedJedisPool) {
            synchronized (RedisUtil.class) {
                if (null == shardedJedisPool) {
                    JedisPoolConfig config = initialPool();
                    // slave链接
                    List<JedisShardInfo> shards = new ArrayList<>();
                    String[] hosts = properties.getProperty(RedisConfig.REDIS_HOST).split(",");
                    for (String host : hosts) {
                        JedisShardInfo shardInfo = new JedisShardInfo(host, port, password);
                        shardInfo.setPassword(properties.getProperty(RedisConfig.REDIS_PASSWORD + "." + host));
                        shards.add(shardInfo);
                    }
                    // 构造池
                    shardedJedisPool = new ShardedJedisPool(config,shards);
                }
            }
        }
        return shardedJedisPool.getResource();
    }
}
```

# Jedis API
[](https://www.cnblogs.com/kingsonfu/p/10422037.html)

