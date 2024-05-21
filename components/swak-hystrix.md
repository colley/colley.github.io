## 原理
通过注解AOP，提供service的方法级别熔断，隔离和兜底，增加了熔断报警等。

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml

        <dependency>
             <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-hystrix-boot-starter</artifactId>
        </dependency>
        
```


## 使用介绍
1、@SwakHystrix注解定义

```java
    @Documented
    @Target({ java.lang.annotation.ElementType.METHOD })
    @Retention(RetentionPolicy.RUNTIME)
    @SwakFallback
    public @interface SwakHystrix {
    	abstract int coreSize() default 10;
    	abstract int maximumSize() default 15;
    	abstract int maxQueueSize() default 100;
    	abstract int queueSizeRejectionThreshold() default 5;
    	abstract String threadPoolKey() default ""; //默认classNane_methodName
    	/**
    	 * 超时时间 default 3s
    	 * 
    	 * @return milliseconds
    	 */
    	abstract int timeout() default 3000;
    	/**
    	 * fallback最大请求数
    	 * 
    	 * @return
    	 */
    	abstract int fallbackMaxRequest() default Integer.MAX_VALUE;
    	/**
    	 * 是否开启超时，默认开启
    	 * 
    	 * @return
    	 */
    	abstract boolean timeoutEnabled() default true;

        /***熔断方法，不指定方法，默认抛SwakFallbackException*/
        @AliasFor(annotation = SwakFallback.class)
        String fallbackMethod() default ""; 
    	@AliasFor(annotation = SwakFallback.class)
    	abstract SwakExecutionType async() default SwakExecutionType.SYNCHRONOUS;

    	abstract Class<? extends FallbackEvent>[] event() default {HystrixAlarmEvent.class};//熔断报警
  
    }
    
    ```

2、在需要处理的Service类的方法上面加上@SwakHystrix注解 HystrixAlarmEvent是熔断报警事件
```java
	public class StarRankHandler implements SyncHandler<RpcParamRequest, List<RankItemVo>> {
    @Autowired
    private StarShopPageService starShopPageService;

    @SwakHystrix(timeout = 600, fallbackMethod = "fallback", event = HystrixAlarmEvent.class)
    @Override
    public List<RankItemVo> invoke(RpcParamRequest input) {
        return starShopPageService.getAllStarRank(input);
    }

    @Override
    public List<RankItemVo> fallback(RpcParamRequest input) {
        return EmptyObject.emptyList();
    }

 ```
