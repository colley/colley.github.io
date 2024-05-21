## springboot是什么？

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。
SpringBoot是一个框架，一种全新的编程规范，他的产生简化了框架的使用，所谓简化是指简化了Spring众多框架中所需的大量且繁琐的配置文件，所以 SpringBoot是一个服务于框架的框架，服务范围是简化配置文件。

Spring Boot框架的核心思想是：约定大（优）于配置（Convention over Confifiguration），即按约定进行编程，这是一种软件设计范式，旨在减少软件开发人员自主配置的数量。
可以通过两个方面来体现：
- 约定并提供一些推荐的默认配置参数。
- 开发者只需要定义约定以外的配置参数。
## SpringBoot的核心特性
SpringBoot是一个用于简化Spring应用程序开发的框架，它提供了一系列核心特性，使得开发者能够更快速、更简单地构建和部署Spring应用程序。本文将详细介绍SpringBoot的五个核心特性，并为每个特性提供三个子特性的详细解释。

### 1. 独立运行的Spring应用程序
   SpringBoot允许开发者创建独立运行的Spring应用程序，这意味着开发者无需部署到外部应用服务器，就可以直接运行Spring应用程序。
#### 1.1 内嵌Servlet容器
SpringBoot内置了多种Servlet容器（如Tomcat、Jetty和Undertow），开发者可以根据需要选择合适的Servlet容器。内嵌Servlet容器使得开发者无需额外配置和部署应用服务器，大大简化了应用程序的部署过程。
#### 1.2 可执行JAR和WAR文件
SpringBoot支持将应用程序打包成可执行的JAR或WAR文件，这使得开发者可以轻松地将应用程序部署到任何支持Java的环境中。此外，可执行JAR文件还可以包含应用程序的所有依赖，从而简化了应用程序的分发和部署。

#### 1.3 简化的启动和关闭过程
SpringBoot提供了一个名为SpringApplication的类，用于简化应用程序的启动和关闭过程。开发者只需调用SpringApplication.run()方法，即可启动应用程序。此外，SpringBoot还支持优雅地关闭应用程序，确保资源得到正确释放。

### 2. 自动配置
   SpringBoot通过自动配置功能，帮助开发者快速地配置和使用各种Spring组件。自动配置根据应用程序的依赖和配置文件，自动地为开发者创建和配置Bean。

#### 2.1 基于条件的自动配置
SpringBoot的自动配置功能基于条件，即只有当满足特定条件时，才会自动配置某个Bean。这些条件包括类路径中存在特定的类、配置文件中存在特定的属性等。这使得自动配置既灵活又可控。

#### 2.2 自定义自动配置
虽然SpringBoot提供了大量的自动配置，但开发者仍然可以根据需要自定义自动配置。通过创建自定义的自动配置类，并使用@EnableAutoConfiguration注解，开发者可以轻松地实现自定义的自动配置。

#### 2.3 自动配置报告
为了帮助开发者了解自动配置的详细情况，SpringBoot提供了自动配置报告功能。通过--debug选项启动应用程序，开发者可以查看自动配置报告，了解哪些自动配置被启用，哪些被禁用，以及原因。

### 3. 灵活的配置管理
   SpringBoot提供了一套灵活的配置管理机制，使得开发者可以轻松地管理应用程序的配置信息。

#### 3.1 外部化配置
SpringBoot支持将配置信息外部化，即将配置信息存储在外部文件（如application.properties或application.yml）中。这使得开发者可以在不修改代码的情况下，轻松地修改应用程序的配置信息。

#### 3.2 配置文件的优先级
SpringBoot支持多种配置文件，并为这些配置文件定义了优先级。当存在多个配置文件时，高优先级的配置文件会覆盖低优先级的配置文件。这使得开发者可以根据需要灵活地组合和覆盖配置信息。

#### 3.3 环境变量和命令行参数
除了配置文件，SpringBoot还支持通过环境变量和命令行参数设置配置信息。这使得开发者可以在不修改配置文件的情况下，为不同的环境和场景提供不同的配置信息。

### 4. 丰富的生产级功能
   SpringBoot提供了一系列生产级功能，帮助开发者构建可靠、高性能的应用程序。

#### 4.1 健康检查和监控
SpringBoot提供了健康检查和监控功能，使得开发者可以轻松地了解应用程序的运行状况。通过/actuator/health端点，开发者可以查看应用程序的健康状况；通过/actuator/metrics端点，开发者可以查看应用程序的性能指标。

#### 4.2 日志管理
SpringBoot提供了强大的日志管理功能，支持多种日志框架（如Logback、Log4j2和JDK Logging）。通过配置文件，开发者可以轻松地设置日志级别、日志格式和日志输出目标。

#### 4.3 安全管理
SpringBoot集成了Spring Security，提供了一套完善的安全管理机制。通过简单的配置，开发者可以实现身份认证、权限控制和安全防护等功能。

### 5. 易于扩展
   SpringBoot具有良好的扩展性，使得开发者可以轻松地为其添加新功能。

#### 5.1 自定义Starter
开发者可以创建自定义的Starter，将常用的功能封装成可重用的模块。通过添加自定义Starter的依赖，开发者可以轻松地为应用程序添加新功能。

#### 5.2 事件监听和发布
SpringBoot支持事件监听和发布机制，使得开发者可以在应用程序的生命周期中，监听和发布各种事件。通过实现ApplicationListener接口，开发者可以监听特定类型的事件；通过ApplicationEventPublisher接口，开发者可以发布自定义事件。

#### 5.3 集成第三方库和框架
SpringBoot提供了丰富的集成选项，使得开发者可以轻松地将第三方库和框架集成到应用程序中。通过添加相应的依赖和配置，开发者可以使用各种流行的数据库、缓存、消息队列和搜索引擎等技术。

### 6. Spring 框架与Spring Boot
特性	  | Spring                                                                               | Spring Boot|
-------- |--------------------------------------------------------------------------------------|-----------|
含义是什么？ | 基于 Java 的开放源代码 Web 应用程序框架。                                                           | 基于 Spring 框架构建的扩展或模块。|
有何用途？ | 使用工具和预构建的代码库提供灵活、完全可配置的环境，来创建定制的、松散耦合的 Web 应用。                                       | 提供创建独立 Spring 应用程序的功能，这些应用程序可以立即运行，无需注释、XML 配置或编写大量额外的代码。|
何时使用？ | 希望实现以下目标时可使用 Spring：<br> 灵活性。<br>非强制约束方法。<br>从自定义代码中删除依赖项。<br>实现非常独特的配置。<br>开发企业应用程序。 | 希望实现以下目标时可使用 Spring Boot：<br>易于使用。<br>强制约束方法。<br>快速运行优质应用并缩短开发时间。<br>避免编写样板代码或配置 XML。<br>开发 REST API。|
主要功能是什么？ | 依赖项注入| 自动配置|
是否具有嵌入式服务器？ | 否。在 Spring 中，需要显式设置服务器| 是，Spring Boot 随内置的 HTTP 服务器（如 Tomcat 和 Jetty）一起提供。|
如何配置？ | Spring 框架提供灵活性，但必须手动构建其配置。| Spring Boot 根据默认“约定优于配置”原则自动配置 Spring 和其他第三方框架。|
是否存在用于开发/测试应用的 CLI 工具？ | Spring 框架本身不提供用于开发或测试应用的 CLI 工具。| 作为 Spring 模块，Spring Boot 具有用于开发和测试基于 Spring 的应用的 CLI 工具。|
是否需要知道如何使用 XML？| 在 Spring 中，必须了解 XML 配置。| Spring Boot 不需要 XML 配置。|

### 7. 自定义Spring Boot Starter
开发自己的Spring Boot Starter需要依赖以下两个条件：
- Starter Jar - 包含了用于自动配置的代码和默认配置文件
- Companion Library - 提供其实现所需的额外类和配置文件

#### 7.1 定义自定义Starter
首先，我们需要定义一个maven项目，作为自定义Starter的主要代码库。在此示例中，我们定义了一个名为swak-ratelimit-boot-starter的maven项目。
pom主要如下：
```xml
    <groupId>io.gitee.mcolley</groupId>
    <artifactId>swak-ratelimit-boot-starter</artifactId>
    <version>1.0.0-SNAPSHOT</version>
```
#### 7.2 添加依赖项
```xml
<dependencies>
  <!-- 开发 Spring Boot Starter 所需依赖 -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>2.2.5.RELEASE</version>
  </dependency>

  <!-- 使用 starter-parent 继承来自定义 Starter 需要的依赖 -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
    <type>pom</type>
  </dependency>
</dependencies>
```
#### 7.3 创建ConfigurationProperties
```java
@ConfigurationProperties(prefix = SwakConstants.SWAK_RATE_LIMIT)
public class RateLimitProperties {
	/**
	 * 排序 {@link @Ordered}
	 */
	private int order = SwakConstants.ORDER_PRECEDENCE+1;
	/**
	 * 限流类型 {@link com.swak.ratelimit.LimitType}
	 */
	private LimitType type;
	/**
	 * 是否支持动态限流
	 */
	private boolean dynamic;
    .... 忽略
```
#### 7.4 创建AutoConfiguration
```java
    @Configuration
    @Import({ RateLimitConfigurationImportSelector.class })
    @ConditionalOnProperty(prefix = SwakConstants.SWAK_RATE_LIMIT, name = "enabled", havingValue = "true", matchIfMissing = false)
    public class RateLimitAutoConfiguration {
    }
    
    public abstract class AbstractRateLimitConfiguration extends SwakAbstractConfiguration implements ImportAware {

        protected RateLimitProperties rateLimitPropertie;
    
        @Override
        public void setImportMetadata(AnnotationMetadata importMetadata) {
            AnnotationAttributes enableRateLimiting = AnnotationAttributes
                    .fromMap(importMetadata.getAnnotationAttributes(EnableRateLimiting.class.getName()));
            if (enableRateLimiting != null) {
                RateLimitProperties rateLimitConfig = new RateLimitProperties();
                rateLimitConfig.setDynamic(enableRateLimiting.getBoolean("dynamic"));
                ....忽略
                this.rateLimitPropertie = rateLimitConfig;
            }
            if (enableRateLimiting == null) {
                this.rateLimitPropertie = Binder.get(systemConfig.getEnvironment())
                        .bind(SwakConstants.SWAK_RATE_LIMIT, RateLimitProperties.class).get();
                if (this.rateLimitPropertie.getType() == null) {
                    throw new IllegalArgumentException(
                            "@EnableRateLimiting is not present on importing class " + importMetadata.getClassName());
                }
            }
    }
```
#### 7.5 配置spring.factories
```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration =\
com.swak.ratelimit.spring.configuration.RateLimitAutoConfiguration
```
#### 7.6 使用spring-boot-starter
打包之后，maven引入swak-ratelimit-boot-starter的jar包
```xml
<dependency>
    <groupId>io.gitee.mcolley</groupId>
    <artifactId>swak-ratelimit-boot-starter</artifactId>
 </dependency>
```
在 resources/application.properties文件添加自定义的配置
```properties
   #限流配置 type=local_token/redis#
    swak.ratelimit.enabled=false  //是否开启限流
    swak.ratelimit.type=local_token //限流类型（local_token/redis 本地和分布式）
```
支持注解的方式自动配置限流组件 SringBootApplication上增加@EnableRateLimiting来开启限流。

在需要处理的Service类的方法上面加上@RateLimit注解，支持自定义兜底和返回码
```java
	 //限流增加兜底
	@RateLimit(qps = 1, fallbackMethod = "fallback")
	@Override
	public RpcResponse<String> rateLimitDemoFallBack(SwakRequest request) {
		return Responses.buildResponse("ok");
	}
```
### 8. 自动配置流程原理
我们引入starter的依赖，Springboot采用自己的SPI机制会将自动配置的类的jar引入和实例化。主要的逻辑如下：

- 在SpringBoot的启动类会加上@SpringBootApplication注解。这个注解默认会引入@EnableAutoConfiguration注解。
- @EnableAutoConfiguration注解使用@Import引入了AutoConfigurationImportSelector类。
- AutoConfigurationImportSelector的selectImports方法最终会通过SpringFactoriesLoader.loadFactoryNames加载META-INF/spring.factories文件。
- spring.factories包含了所有需要装配的XXXConfiguration类的全限定名。其中就有我们自己定义的RateLimitAutoConfiguration。
  这些configuration类定义的bean根据配置信息进行初始化，并加入到spring到容器中。

#### 8.1 什么是SPI机制？
SPI（Service Provider Interface）机制是一种服务发现和加载机制。它允许开发者编写一个服务接口，然后通过在项目中使用服务提供者实现该接口的方式，实现对应的服务功能。

#### 8.1.1 JDK中的SPI
JDK的SPI机制通过在Classpath中的META-INF/services目录下，创建以服务接口全限定名命名的文件，
文件的内容为实现该接口的具体实现类。当应用程序需要使用该服务时，JDK会自动加载并实例化配置文件中列出的实现类，并提供给应用程序使用。

#### 8.1.2 Springboot中的SPI
在Spring Boot中，SPI机制允许开发者通过定义接口和实现类的方式，实现对应的功能扩展。通过在META-INF/spring.factories配置文件中列出实现类，
Spring Boot能够自动加载并使用这些扩展点，提供了灵活的定制和扩展能力。

## 思考

- SpringBoot的缺点是什么？

