## @Conditional是什么？

@Conditional注解是从spring4.0版本才有的，其是一个条件装配注解，可以用在任何类型或者方法上面，以指定的条件形式限制bean的创建；即当所有条件都满足的时候，被@Conditional标注的目标才会被spring容器处理。 

- @Conditional本身也是一个父注解，从SpringBoot1.0版本开始派生出了大量的子注解；用于Bean的按需加载。
- @Conditional注解和其所有子注解必须依托于被@Component衍生注解标注的类，即Spring要能扫描到@Conditional衍生注解所在的类，才能做进一步判断。
- @Conditional衍生注解可以加在类 或 类的方法上；加在类上表示类的所有方法都做条件装配、加在方法上则表示只有当前方法做条件装配。
### @Conditional源码
```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
	/**
	 * All {@link Condition} classes that must {@linkplain Condition#matches match}
	 * in order for the component to be registered.
	 */
	Class<? extends Condition>[] value();
}
```
@Conditional注解只有一个value参数，类型是：Condition类型的数组；而Condition是一个接口，其表示一个条件判断，内部的matches()方法返回true或false；当所有Condition都成立时，@Conditional的条件判断才成立。
#### Condition接口
```java
@FunctionalInterface
public interface Condition {
	/**
	 * 判断条件是否匹配
	 * @param context 条件判断上下文
	 * @param metadata 注解元数据
	 * @return 如果条件匹配返回TRUE，否者返回FALSE
	 */
	boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);
}
```
 Condition是一个函数式接口，其内部只有一个matches()方法，用来判断条件是否成立的，方法有2个入参：

- context：条件上下文，ConditionContext接口类型，用来获取容器中的信息
- metadata：用来获取被@Conditional标注的对象上的所有注解信息

#### ConditionContext接口
```java
public interface ConditionContext {
	/**
	 * 返回bean定义注册器BeanDefinitionRegistry，通过注册器获取bean定义的各种配置信息
	 */
	BeanDefinitionRegistry getRegistry();
	/**
	 * 返回ConfigurableListableBeanFactory类型的bean工厂，相当于一个ioc容器对象
	 */
	@Nullable
	ConfigurableListableBeanFactory getBeanFactory();
	/**
	 * 返回当前spring容器的环境配置信息对象
	 */
	Environment getEnvironment();
	/**
	 * 返回正在使用的资源加载器
	 */
	ResourceLoader getResourceLoader();
	/**
	 * 返回类加载器
	 */
	@Nullable
	ClassLoader getClassLoader();
}
```
ConditionContext接口中提供了一些常用的方法，用于获取spring容器中的各种信息。
### @Conditional衍生注解使用
从SpringBoot1.0版本开始@Conditional派生出了大量的子注解；用于Bean的按需加载。主要包括六大类：
- Class Conditions
- Bean Conditions
- Property Conditions
- Resource Conditions
- Web Application Conditions
- SpEL Expression Conditions
#### Class Conditions
包含两个注解：@ConditionalOnClass 和 @ConditionalOnMissingClass。
- @ConditionalOnClass
  @ConditionalOnClass注解用于判断其value值中的Class类是否都可以使用类加载器加载到，如果都能，则符合条件装配。
```java
        @Target({ ElementType.TYPE, ElementType.METHOD })
        @Retention(RetentionPolicy.RUNTIME)
        @Documented
        @Conditional(OnClassCondition.class)
        public @interface ConditionalOnClass {
        
            /**
             * The classes that must be present. Since this annotation is parsed by loading class
             * bytecode, it is safe to specify classes here that may ultimately not be on the
             * classpath, only if this annotation is directly on the affected component and
             * <b>not</b> if this annotation is used as a composed, meta-annotation. In order to
             * use this annotation as a meta-annotation, only use the {@link #name} attribute.
             * @return the classes that must be present
             */
            Class<?>[] value() default {};
        
            /**
             * The classes names that must be present.
             * @return the class names that must be present.
             */
            String[] name() default {};
        
        }
```

 @ConditionalOnClass注解中有两个属性：value() 和 name()。
 1. @ConditionalOnClass#value() 属性方法提供“类型安全”的保障，避免在开发过程中出现全类名拼写的低级失误。 
 2. @ConditionalOnClass#name() 多用于三方库或高低版本兼容的场景。 
 3. 使用方式，参考WebTestClientAutoConfiguration.class
 ```java
    @Configuration(proxyBeanMethods = false)
    @ConditionalOnClass({ WebClient.class, WebTestClient.class })
    @AutoConfigureAfter({ CodecsAutoConfiguration.class, WebFluxAutoConfiguration.class })
    @Import(WebTestClientSecurityConfiguration.class)
    @EnableConfigurationProperties
    public class WebTestClientAutoConfiguration {
      ....
    }
```
这里表示，只有当WebClient.WebTestClient.class类都存在时，才会加载WebClientAutoConfiguration到Spring容器。
- @ConditionalOnMissingClass

  @ConditionalOnMissingClass注解用于判断其value值中的Class类是否都不可以使用类加载器加载到，如果都不能，则符合条件装配。

 使用方式:
```java
    @ConditionalOnMissingClass("org.aspectj.weaver.Advice")
    @Configuration
    static class ClassProxyingConfiguration {
        ....
    }
```
这里表示，只有当org.aspectj.weaver.Advice类不存在时，才会加载ClassProxyingConfiguration到Spring容器。

#### Bean Conditions
包含两个注解：@ConditionalOnBean 和 @ConditionalOnMissingBean。
- @ConditionalOnBean
  @ConditionalOnBean注解用于判断某些Bean是否都加载到了Spring容器BeanFactory中，如果是的，则符合条件装配。
- @ConditionalOnMissingBean
  @ConditionalOnMissingBean注解用于判断某些Bean是否都没有加载到Spring容器BeanFactory中，如果是的，则符合条件装配。

#### Property Conditions（@ConditionalOnProperty）

@ConditionalOnProperty注解依赖于Spring环境参数（Spring Environment property）来做条件装配。
```java
@Conditional(OnPropertyCondition.class)
public @interface ConditionalOnProperty {
	String[] value() default {};
	String prefix() default "";
	String[] name() default {};
	String havingValue() default "";
	boolean matchIfMissing() default false;
```
 1. 其使用prefix 和 name属性表明哪个属性应该被检查。如果prefix()不为空，则属性名称为prefix()+name()，否则属性名称为name()
 2. 默认情况下，匹配存在且不等于false的任何属性。
 3. 此外可以使用havingValue 和 matchIfMissing 属性创建更高级的检查。
    - havingValue() --> 表示期望的配置属性值，并且禁止使用false 
    - matchIfMissing() --> 用于判断当属性值不存在时是否匹配
 
使用方式：
```java
@Configuration
@EnableConfigurationProperties({SwakKnife4jProperties.class})
@ConditionalOnProperty(prefix = SwakConstants.SWAK_SWAGGER, name = "enable", havingValue = "true", matchIfMissing = false)
@EnableWebMvc
@Slf4j
public class SwaggerAutoConfiguration {
    ...
}
```
#### Resource Conditions (@ConditionalOnResource）
@ConditionalOnResource通过判断某些资源是否存在来做条件装配。

使用方式：
```java
@Configuration
@ConditionalOnResource(resources = {"classpath:META-INF/build-info.properties"})
public class BuildInfoAutoConfiguration {
    ....
}
```
这里表示，只有当classpath:META-INF/build-info.properties文件资源存在时，BuildInfoAutoConfiguration才会被加载到Spring容器。

#### Web Application Conditions
包含两个注解：@ConditionalOnWebApplication 和 @ConditionalOnNotWebApplication。
- @ConditionalOnWebApplication
  @ConditionalOnWebApplication用于判断SpringBoot应用的类型是否为指定Web类型（ANY、SERVLET、REACTIVE）
```java
    @Documented
    @Conditional(OnWebApplicationCondition.class)
    public @interface ConditionalOnWebApplication {
    /**
     * The required type of the web application.
     * @return the required web application type
     */
    Type type() default Type.ANY;

    /**
     * Available application types.
     */
    enum Type {

        /**
         * Any web application will match.
         */
        ANY,

        /**
         * Only servlet-based web application will match.
         */
        SERVLET,

        /**
         * Only reactive-based web application will match.
         */
        REACTIVE
    }
    ...
```
使用方式:
```java
    @Configuration
    @ConditionalOnWebApplication(type = Type.SERVLET)
    public class ForMatterAutoConfiguration {
        ....
    }
```
这里表示，只有当SpringBoot应用类型为SERVLET应用类型时，ForMatterAutoConfiguration才会被加载到Spring容器。

- @ConditionalOnNotWebApplication
  @ConditionalOnNotWebApplication用于判断SpringBoot应用的类型是否不是Web应用。

#### SpEL Expression Conditions（ConditionalOnExpression）
@ConditionalOnExpression通过SpEL Expression来做条件装配。

这种方式做条件装配有个坑：

其会导致在上下文刷新处理中很早就初始化了被标注的bean，进而导致bean无法进行后置处理（比如配置属性绑定），其状态可能是不完整的。


### 自定义@Conditional实践
- 自定义EnvironmentConditional
```java

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Conditional(EnvironmentCondition.class) //<- meta annotation condition
public @interface EnvironmentConditional {
    //环境
    String[] env() default {};
    //是否取反
    boolean reversed() default false;
}

```
- 实现Condition接口 EnvironmentCondition.class
```java
public class EnvironmentCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        AnnotationAttributes attributes = AnnotationAttributes
                .fromMap(metadata.getAnnotationAttributes(EnvironmentConditional.class.getName()));
        if (Objects.isNull(attributes)) {
            return false;
        }
        String[] env = attributes.getStringArray("env");
        boolean reversed = attributes.getBoolean("reversed");
        if (ArrayUtils.isEmpty(env)) {
            return true;
        }
        SysEnv sysEnv = null;
        String[] activeProfiles = context.getEnvironment().getActiveProfiles();
        if (ArrayUtils.isNotEmpty(activeProfiles)) {
            sysEnv = SysEnv.getEnv(activeProfiles[0]);
        }
        if (Objects.isNull(sysEnv)) {
            return true;
        }
        boolean isMatches = false;
        for (String envStr : env) {
            if (sysEnv.eq(envStr)) {
                isMatches = true;
                break;
            }
        }
        ...
```
- 使用例子
```java
@Configuration
@EnableConfigurationProperties({SwakKnife4jProperties.class})
@EnvironmentConditional(env = {"prod"},reversed = true)
@EnableWebMvc
@Slf4j
public class SwaggerAutoConfiguration {
    ...
}
```
这里表示，只有非生产环境才会开启Swagger的open api，生产环境不开启。

