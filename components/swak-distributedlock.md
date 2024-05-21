## 原理

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
             <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-distributedlock-boot-starter</artifactId>
        </dependency>
```

## 使用介绍

分布式锁DistributedLock增加续租功能，避免业务执行时间超过锁的时间。

```java

	@Autowired
	private DistributedLock distributedLock;
	
	public ApiResponse<TaskResultVo> doTask(RpcParamRequest request) {
		......
		String requestId = UUIDHexGenerator.generator();
		String lockKey = cacheProxy.format(AppConstants.CACHE_USER_TASK_LOCK, pin);
		try {
			if (!distributedLock.tryLock(lockKey, requestId, TimeUnitConst.SECONDS_30)) {
				// 重复操作
				logger.warn("doTask#做任务重复刷操作！pin:{},itemVo", pin, JSON.toJSONString(itemIdVo));
				return builder.of(SysRestCode.OPER_REPEAT_LIMIT).build();
			}
			
			......
			
		} finally {
			distributedLock.unLock(lockKey, requestId);
		}
	}
	 
```