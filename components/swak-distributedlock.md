## 原理
swak-distributed-lock集成四种不同的lock方式。
通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
             <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-distributed-lock-boot-starter</artifactId>
        </dependency>
```
## 四种种不同lock方式
- LocalSyncLock 本地锁，不支持分布式。
- ZookeeperLock 基于Zookeeper的方式实现的分布式锁。
- RedissonLock 基于Redisson的方式实现的分布式锁。
- RedisLock 基于redis的Lua表达式的方式实现的分布式锁。

## application配置

```xml
  swak.lock.type=zk/redisson/redis/local_only
```
分布式锁的使用例子

```java

	@Autowired
	private DistributedLock distributedLock;
	
	public ApiResponse<TaskResultVo> doTask(RpcParamRequest request) {
		......
		String requestId = UUIDHexGenerator.generator();
		String lockKey = cacheProxy.format(AppConstants.CACHE_USER_TASK_LOCK, pin);
		try {
			if (!distributedLock.acquireLock(lockKey, requestId, TimeUnitConst.SECONDS_30)) {
				// 重复操作
				logger.warn("doTask#做任务重复刷操作！pin:{},itemVo", pin, JSON.toJSONString(itemIdVo));
				return builder.of(SysRestCode.OPER_REPEAT_LIMIT).build();
			}
			......
			
		} finally {
			distributedLock.releaseLock(lockKey, requestId);
		}
	}
	 
```