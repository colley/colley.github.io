## 原理
通过注解AOP，提供service的方法级别限流，采用本地和分布式限流

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
            <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-ratelimit-boot-starter</artifactId>
        </dependency>
```

## 使用方式

- ### 方式一： application.properties配置方式

```properties

    #限流配置 type=local_token/redis#
    swak.ratelimit.enabled=false  //是否开启限流
    swak.ratelimit.type=local_token //限流类型（local_token/redis 本地和分布式）
    
```


- ### 方式二： @EnableRateLimiting注解

```java

    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Import(RateLimitConfigurationImportSelector.class)
    public @interface EnableRateLimiting {
    
    
    	/**
    	 * 排序 {@link  @Ordered}
    	 * @return
    	 */
    	int order() default RateLimitConstant.ORDER_PRECEDENCE;
    
    	/**
    	 * 限流类型 {@link com.swak.frame.ratelimit.LimitType}
    	 * 
    	 * @return
    	 */
    	LimitType limitType() default LimitType.LOCAL_TOKEN;
    
    	/**
    	 * 是否支持动态限流
    	 * 
    	 * @return
    	 */
    	boolean dynamic() default false;
    }

```


如果采用配置中心配置限流策略需要实现限流的扩展点,实现RateLimitProvider接口：
例子：

```java

	@Extension(bizId = "rateLimit",useCase="dal")
        public class DalLimitQpsProvider implements RateLimitProvider {

        @Override
        public boolean enable(String resource) {
            int qps = ConfigClient.getInt(resource, -1);
            return qps > 1;
        }

        @Override
        public SwakLimitConfig getConfig(String resource) {
            SwakLimitConfig config = new SwakLimitConfig();
            config.setQps(ConfigClient.getInt(resource, -1));
            config.setResource(resource);
            return config;
        }
    }

```

## 使用介绍
1、在需要处理的Service类的方法上面加上@RateLimit注解，支持自定义兜底和返回码

```java

    @Documented
    @Target({ java.lang.annotation.ElementType.METHOD })
    @Retention(RetentionPolicy.RUNTIME)
    @SwakFallback
    public @interface RateLimit {
        String key() default ""; //为空默认是 ratelimit_className_methodName
        @AliasFor("value")
        int qps() default -1;
        @AliasFor("qps")
        int value() default -1;
        String retCode() default "";
        String desc() default "";
        
        /**限流兜底方法  {@link SwakFallback#fallbackMethod} **/
        @AliasFor(annotation = SwakFallback.class)
        String fallbackMethod() default "";

        /** 限流类型 {@link SwakFallback#async} **/
        @AliasFor(annotation = SwakFallback.class)
        abstract SwakExecutionType async() default SwakExecutionType.SYNCHRONOUS;
        abstract Class<? extends RateAlarmEvent>[] event() default {RateLimitAlarmEvent.class}; //限流报警
}

```


例子：

```java

	public class StarRankHandler implements SyncHandler<RpcParamRequest, List<RankItemVo>> {

	    @RateLimit(qps=300)
	    @Override
	    public List<RankItemVo> invoke(RpcParamRequest input) {
	        return starShopPageService.getAllStarRank(input);
	    }
	}

```