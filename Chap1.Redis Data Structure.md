# Chap1 Redis数据结构

## 1、List

除了Redis String外，List几乎是Redis最常用的数据结构了。它可以用来存储列表结构，一般列表中的每个元素都是具有同类性质，比如Linux系统的用户对文件的读、写和执行权限就可以用list来存储。

代码示例如下：

```java
Jedis jedis = null;
try {
	jedis = new Jedis("127.0.0.1");
	String key = "1000";
	
	// 存入
	jedis.lpush(key, "read");
	jedis.lpush(key, "write");
	jedis.lpush(key, "execute");
	
	// 读取
	List<String> userPermissionNameList = jedis.lrange(key, 0/*start，包含start位置处元素*/, 
			-1/*end,-1表示最后一个元素*/);
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