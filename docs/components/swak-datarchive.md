## 原理

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
             <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-datarchive-boot-starter</artifactId>
        </dependency>
```

## application配置

```properties
	##数据归档以及历史数据删除###
	swak.frame.archive.enabled=true
	swak.frame.archive.config.dataSoruceName=dataSoruceBeanName
	swak.frame.archive.config.email.host=xxxx
	swak.frame.archive.config.email.port=25
	swak.frame.archive.config.email.defaultEncoding=UTF-8
	swak.frame.archive.config.email.username=xxx
	swak.frame.archive.config.email.password=xxxxx
	swak.frame.archive.config.email.senderName=xxx
	swak.frame.archive.config.email.sender=xxxx
	swak.frame.archive.config.email.smtpAuth=true
	swak.frame.archive.config.email.debug=true

```

## 使用介绍

```java
	就可以使用
	@Resource
	private ArchiverDataEngine archiverDataEngine;
	实现数据归档或数据删除
	archiverDataEngine.archive(ArchiveConfig);
```

- ArchiveConfig的介绍

```java

	/** where条件*/
	private String where = "1=1";

	/** 每次limit取行数据归档处理）*/
	private Integer limit;

	/** 设置一个事务提交一次的数量. 单条插入和单条删除*/
	private Integer txnSize;

	/** 是否删除source数据库的相关匹配记录*/
	private boolean purge = Boolean.TRUE;

	/***是否归档 false=定时删除**/
	private boolean archive = Boolean.TRUE;

	/** progressSize每次归档限制总条数）*/
	private Integer progressSize = Integer.MAX_VALUE;

	/**尝试次数**/
	private Integer retries = 2;

	/** 每次归档了limit个行记录后的休眠1秒（单位为毫秒）*/
	private Long sleep = 5 * 1000L;

	/**归档的表名***/
	private String srcTblName;

	/**归档目标表名**/
	private String desTblName;

	/**是否批次执行**/
	private boolean bulk = true;

	/**是否发送邮件**/
	private boolean sendEmail;

	/**邮件接收人,逗号分隔**/
	private String recipients;
    
```