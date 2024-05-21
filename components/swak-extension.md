## 作用
该组件继承了老swak Framework中的扩展点功能，旨在通过统一的扩展形式来支撑业务的变化。

## 原理

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
             <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-spring-boot-starter</artifactId>
        </dependency>
```

根据多场景的情况增加扩展点：扩展点表示一块逻辑在不同的业务有不同的实现，使用扩展点做接口申明,
扩展点声明接口：ExtensionPoint 使用@Extension设置不容实现的优先级和多场景标签 扩展的服务需要继承扩展点声明接口ExtensionPoint。


```java

	@Inherited
    @Retention(RetentionPolicy.RUNTIME)
    @Target({ ElementType.TYPE })
    @Component
    public @interface Extension {
        //业务编码
        String bizId() default BizScenario.DEFAULT_BIZ_ID;
        //case
        String useCase() default BizScenario.DEFAULT_USE_CASE;
        //场景
        String scenario() default BizScenario.DEFAULT_SCENARIO;
    }
 ```
 
## 使用介绍

通过ExtensionExecutor执行扩展点服务：

例子：

```java
      @Extension(bizId=WhiteListProviderExPt.WHITE_LIST_BIZ_ID)
      public class DalWhiteListProvider implements WhiteListProviderExPt {
          private static String WHITE_LIST = "whiteList";

          @Override
          public Set<String> getConfig(BoundContext resource) {
              String whiteList = ConfigClient.get(WHITE_LIST);
              if (StringUtils.isEmpty(whiteList)) {
                  return EmptyObject.emptySet();
              }
              return Sets.newHashSet(Splitter.on(',').splitToList(whiteList));
          }
      }

	
	Set<String> whiteList = extensionExecutor.execute(WhiteListProviderExPt.class,
			BizScenario.valueOf(WhiteListProviderExPt.WRISK_BIZ_ID),
			extension -> extension.getConfig(resource));


 ```
