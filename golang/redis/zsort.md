# zset的各种用法

## 1、zset用于定时任务
### 简述：zset key作为任务集合，members做个各个任务，每个member的score为任务触发时间
### 用法：
~~~golang
func zset() {
	// 写入zset,zadd命令，key不存在时会新建，mem存在时会更新score
	_, err := redis.Conn.Do("ZADD", "key", "score", "mem")
	
	// 读取一个任务，ZRANGE命令，默认按score低的顺序返回，闭区间，索引从0开始，想要获取0为 0 0,
	// 避免并发消费重复查询的问题，ZRANGE需要后先ZREM命令取出数据
	// 5.0版本有ZPOPMIN命令，避免了预防并发消费重复取出的问题
    obj, err := redis.Conn.Do("ZRANGE", "key", 0, 0, "WITHSCORES")
	if len(obj) == 0 {
        // 为空不报错，len(obj) == 0
    }
	
    _, err = redis.Conn.Do("ZREM", "key", mem) 
}
~~~

## 2、zset用于排行榜