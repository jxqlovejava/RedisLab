# Chap1 Redis数据结构

## 1、 List
### 1.1、简单介绍
除了Redis String外，List几乎是Redis最常用的数据结构了。它可以用来存储链表（有的地方也称作链表）结构，一般列表中的每个元素都是具有同类性质，比如Linux系统的用户对文件的读、写和执行权限就可以用list来存储。List支持的操作包括lpush、rpush、lpop、rpop、lindex（按索引获取元素）、lrange（区段查询）、llen（获取链表的长度）、lset（设置链表指定索引处为某个元素）、lrem、ltrim等。完整命令列表查看<a href="http://www.redis.cn/commands.html#list" target="_blank">Redis List</a>

其中lpush会使得先插入的元素排在链表最右端，从而先插入的元素的索引也比后插入的更大，可以想象始终从链表左部插入元素，如果左部头位置已有元素，则将链表中的已有元素都向右挪以腾出空位给待插入的新元素。


### 1.2、Java操作示例
Java代码示例如下：

```java
Jedis jedis = null;
try {
	jedis = new Jedis("127.0.0.1");
	String key = "1000";
	
	// 存入
	jedis.lpush(key, "read");   // 返回值是long，表示插入后list中元素的数目
	jedis.lpush(key, "write");
	jedis.lpush(key, "execute");
	
	// 读取
	List<String> userPermissionNameList = jedis.lrange(key, 0/*start，包含start位置处元素*/, 
			-1/*end,-1表示最后一个元素*/);   // 取出list的所有元素
	System.out.println(userPermissionNameList);   // 输出list：[execute, write, read]
}
catch(Exception ex) {
	ex.printStackTrace();
}
finally {
	if(jedis != null) {
		jedis.close();
	}
}

```

### 1.3、实现方式
Redis List的底层实现是一个双链表，这样不但支持正向查找和遍历，还支持反向查找和遍历。

### 1.4、应用场景
有时我们需要处理一些顺序任务或者顺序消息，那么我们可以利用lpush和rpop来分别存和取任务或消息，然后进行处理（当然也可以使用rpush和lpop）。

此外，类似上面代码提到的一些比较稳定的列表类配置信息，比如开放平台中的用户默认最小权限列表信息，也很适合用Redis List来存储，而每次使用都需要取出整个列表的数据可以通过lrange key 0 -1来完成。
