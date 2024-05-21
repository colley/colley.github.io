## 原理
通过注解AOP，提供service的方法级别ump监控，监控全部采用异步方式上报，减少对业务接口的弱依赖。

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
            <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-monitor-boot-starter</artifactId>
        </dependency>
```


## 使用介绍

### 1、ump接口监控

1、在需要处理的Service类的方法上面加上@UmpTag注解
	
```java

	public @interface UmpTag {
		@AliasFor("value")
		String tag() default "service";
	
		@AliasFor("tag")
		String value() default "service";
	
		boolean enableHeartbeat() default false;
	
		boolean enableTP() default true;
	}
	
```

例子： ump的key 动态生成（bizName_tag_className_methodName）

如果应用的bizName=brandcity，
	
```java

	那ump的key：bizName_task_TaskRemoteServiceImpl_queryTaskList
	
	public class TaskRemoteServiceImpl ...{

		@UmpTag("task")
		@Override
		public RemoteRespond<TaskListInfo> queryTaskList(ComponentQryTaskRequest param, Predicate<BaseTaskVo> predicate) {
		....
		}
	}
```

### 2、ump自定义监控和业务监控

使用BizAlarmAdapter来实现自定义监控和业务监控
例子：
	
	bizAlarmAdapter.alarmTag("job", "题库信息未配置或返回为空，xx组ID:{}", xxx); 

	对应的umpKey：xx_job_bizcus_alarm（bizName_xx_bizcus_alarm）

业务监控调用 bizAlarmAdapter.bizDataAlarm来实现。

```java

	//发豆
	bizAlarmAdapter.bizDataAlarm("sendBean", amount,"fail");
	
	key=bizName_bizTag_bizdata_alarm
	
```