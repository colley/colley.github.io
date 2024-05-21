## 介绍
swak-spring-cache中的@ExtCacheable 是对spring的 @Cacheable 的扩展， @ExtCacheable 功能更丰富、用起来更舒心。

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
            <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-extspringcache-boot-starter</artifactId>
        </dependency>
```

## 前置条件 

依赖redis以及ehcache配置

## 功能特性
- @ExtCacheable支持指定redis操作缓存
- @ExtCacheable支持对具体的key设置过期时间（对比：@Cacheable只支持对命名空间级别设置过期时间）
- @ExtCacheable支持redis缓存、支持caffeine缓存、ehcache，支持多级缓存（对比：@Cacheable不全支持）
- @ExtCacheable对@Cacheable保持兼容

## 使用介绍

### 示例一
默认支持ehcache缓存，需要ehcache.xml中配置对应的cache

```java
	@ExtCacheable(cacheNames = "riskUserCache", key = "'riskUser:'+ #paramRequest.userId")
	public boolean isRiskUser(SwakRequest paramRequest) {
	  ......
	 }
	 
```
### 示例二
multiLevel=true表示使用多级缓存，一级缓存默认ehcache，第二级缓存是redis

```java
	@ExtCacheable(cacheNames = "riskUserCache",multiLevel = true, key = "'riskUser:'+ #paramRequest.userId")
	public boolean isRiskUser(SwakRequest paramRequest) {
	  ......
	 }
	 
```
### 示例三
multiLevel=true表示支持多级缓存，设置了caffeine表示一级缓存是caffeine，二级缓存是redis

```java
	@ExtCacheable(cacheNames = "riskUserCache",multiLevel = true, key = "'riskUser:'+ #paramRequest.userId",
	caffeine = @Caffeine(expireTime = 300, maximumSize = 3, expireStrategy = CaffeineExpireStrategyEnum.EXPIRE_AFTER_ACCESS))
	public boolean isRiskUser(SwakRequest paramRequest) {
	  ......
	 }
	 
```
### 示例四
未设置multiLevel，如果设置了caffeine表示只支持caffeine作为缓存

```java
	@ExtCacheable(cacheNames = "riskUserCache",key = "'riskUser:'+ #paramRequest.userId",
	caffeine = @Caffeine(expireTime = 300, maximumSize = 3, expireStrategy = CaffeineExpireStrategyEnum.EXPIRE_AFTER_ACCESS))
	public boolean isRiskUser(SwakRequest paramRequest) {
	  ......
	 }
	 
```
### 示例五
未设置multiLevel，如果设置了caffeine和redis表示支持多级缓存，第一级缓存是caffeine，第二级缓存是redis

```java
	@ExtCacheable(cacheNames = "riskUserCache",key = "'riskUser:'+ #paramRequest.userId",
	redis = @Redis(expireTime = 100,timeUnit = ChronoUnit.MINUTES),
	caffeine = @Caffeine(expireTime = 300, maximumSize = 3, expireStrategy = CaffeineExpireStrategyEnum.EXPIRE_AFTER_ACCESS))
	public boolean isRiskUser(SwakRequest paramRequest) {
	  ......
	 }
	 
```
### 示例六
未设置multiLevel，只设置redis表示只支持redis作为缓存

```java
	@ExtCacheable(cacheNames = "riskUserCache",key = "'riskUser:'+ #paramRequest.userId",
	redis = @Redis(expireTime = 100,timeUnit = ChronoUnit.MINUTES))
	public boolean isRiskUser(SwakRequest paramRequest) {
	  ......
	 }
	 
```
### 示例七
使用spring的@Cacheable 表示只支持ehcache缓存

```java
	@Cacheable(cacheNames = "riskUserCache",key = "'riskUser:'+ #paramRequest.userId")
	public boolean isRiskUser(SwakRequest paramRequest) {
	  ......
	 }
	 
```