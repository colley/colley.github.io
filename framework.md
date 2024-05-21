### 原理

SWAK-Cloud 集成通用的工具类和基础架构，是所有组件的基础架构依赖。

### 架构核心模块

名称 | 功能 | 版本 | 依赖
------ | ---- | ---- | ----
`swak-common` | swak 公共库，包含dto，exception等 | 2.1.2 |无
`swak-core` | swak 核心库 | 2.1.2 |无
`swak-license` | swak license控制 | 2.1.2 |无
`swak-spring-boot-starter` | swak 的springboot autoconfigure | 2.1.2 | 无

###  主要功能
- 基于时间轮的RefreshConfigurable自动刷新缓存解决方案。'
- 增加统一的接口出入参和公共规范 Result<T>和统一的errCode
- 增加monitor监控（内存监控、内存分代监控、系统运行时数据监控、垃圾回收已经nioBuffer等）
- 基 于Eventbus机制的异步处理方案。（分异步和同步等）
- 基于excel的处理工具封装（包含解析和生成等）
- 统一异常封装返回（ApiException等）SwakAssert断言，ApiExceptionUtil等异常处理。
- 基于责任连的封装（FilterChain，Filter，FilterChainFactory）适配SPI模式。
- 增加基于 spring的RestTemplate的封装SwakRestTemplate，默认OKHttpClient 
- 基于服务注解和自动注册单机限流LimitRater解决方案。
- 基于hystrix的隔离，容错，兜底的解决方案。
- 基于spring async异步封装。增加统一的线程池来实现spring async和增加线程池的监控。
- 提供@ExtCacheable多级缓存方案。
- 基于sql的ID生产器方案。
- 基于sso+shrio的权限控制解决方案。
- 提供多场景扩展能力

###  技术选型
分类 |	类别	| 名称 |	版本	| 说明
------ | ---- | ----| ----| ----
开发语言 |	Java |	Java	| 1.8.0_333	|
运行框架	| Web服务器	| Nginx	| 1.21.6 |	
 |	 缓存服务器	|Redis	|6.2.6	|
 |	数据库服务器	|Mysql|	5.7.37	|
 |	注册/配置中心|	Nacos|	1.4.3/2.1.0 |	
开发框架 |	基础框架 |	Spring Boot	|2.3.12.RELEASE |	
 |	框架底座 |	SWAK-Cloud	|2.1.2 |		
微服务框架 |	Dubbo|	2.7.14/3.1.5 |	
 |	spring-cloud-alibaba |	2.2.9.RELEASE |	
持久层框架|	Mybatis-plus|	3.4.2|	
日志|	Logback|	1.2.3|	
 项目管理	|Maven	|3.6.3	
