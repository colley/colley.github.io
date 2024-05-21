##  使用介绍
通过注解AOP，记录业务日志，包含入参和出参以及请求路径等，可以配置记录失败，或者成功以及全部记录。

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
            <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-operatelog-boot-starter</artifactId>
        </dependency>
```

只需要实现OperateLogService接口用于保存采集的业务日志。

```java
@Service
public class OperateLogServiceImpl implements OperateLogService {

    @Resource
    private UserConverter userConverter;

    @Resource
    private MpUserOperateLogService mpUserOperateLogService;

    @Override
    public Result<Void> addOperationLogs(List<OperateDataLog> list) {
        List<UserOperateLog> userOperateLogs = list.stream().map(item -> {
            UserOperateLog userOperateLog = userConverter.toLogEntity(item);
            userOperateLog.setUserId(RequestContext.getUserId());
            userOperateLog.setCreatorId(RequestContext.getUserId());
            return userOperateLog;
        }).collect(Collectors.toList());
         mpUserOperateLogService.saveBatch(userOperateLogs);
         return Result.success();
    }
}
```
##### 1、参考SQL
```sql
CREATE TABLE `user_operation_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '日志主键',
  `trace_id` varchar(64) NOT NULL DEFAULT '' COMMENT '链路追踪编号',
  `user_id` bigint(20) NOT NULL COMMENT '用户编号',
  `module` varchar(50) NOT NULL COMMENT '模块名称',
  `operate_type` varchar(50) NOT NULL COMMENT '操作类型',
  `content` varchar(1024) NOT NULL DEFAULT '' COMMENT '操作内容',
  `request_method` varchar(16) DEFAULT '' COMMENT '请求方法名',
  `request_url` varchar(255) DEFAULT '' COMMENT '请求地址',
  `user_ip` varchar(50) DEFAULT NULL COMMENT '用户 IP',
  `user_agent` varchar(200) DEFAULT NULL COMMENT '浏览器UA',
  `java_method` varchar(512) NOT NULL DEFAULT '' COMMENT 'Java方法名',
  `java_method_args` varchar(2000) DEFAULT '' COMMENT 'Java 方法的参数',
  `start_time` datetime NOT NULL COMMENT '开始时间',
  `cost_time` int(11) NOT NULL COMMENT '执行时长',
  `result_code` int(11) NOT NULL DEFAULT '0' COMMENT '结果码',
  `result_msg` varchar(512) DEFAULT '' COMMENT '结果提示',
  `result_data` varchar(2000) DEFAULT '' COMMENT '结果数据',
  `creator_id` varchar(64) NOT NULL COMMENT '创建人ID',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COMMENT='操作日志记录';

```
##### 2、例子

例子1：支持controller的方法
```java

    @PutMapping("/edit")
    @OperateLog(module = "用户管理",operateType = "edit",logScope =LogScopeEnum.FAIL,
        content =  "'用户账号【'+#command.loginName+'】修改'")
    public Result<Void> editUser(@RequestBody @Validated UserCommand command) {
        return userService.updateUser(command);
    }
```
例子2：支持其他服务

```java
    @OperateLog(module = "用户管理",operateType = "query",content = "用户查询操作")
    @Override
    public Result<Pagination<UserVo>> queryUserList(UserPageReq query,Integer size) {
        QueryWrapper<SysUser> queryWrapper = new QueryWrapper<>();
   ......
    }

```

```java

@Documented
@Target({ java.lang.annotation.ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
public @interface OperateLog {
	/**
	 * 模块
	 */
	  String module();

	/**
	 * 操作类型
	 */
	String operateType();

	/**
	 * 指定日志内容
	 */
	String content() default "";
	/**
	 * 过滤的字段
	 */
	String[] excludeField() default {};
	/**
	 * 是否记录方法参数
	 */
	boolean logArgs() default true;
	/**
	 * 是否记录方法结果的数据
	 */
	boolean logResult() default true;
	/**
	 * 记录日志范围
	 */
	LogScopeEnum  logScope() default LogScopeEnum.ALL;

 public enum LogScopeEnum {
    SUCCESS,//记录成功
    FAIL, //记录失败
    ALL; //全部记录
```
