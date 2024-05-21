# 版本说明 

### 新功能和优化进行中
- 增加分布式锁组件（基于redis和zk） 开发中......
- 组件easyJob支持动态配置cron以及开关 开发中......
- 组件easyJob支持zk和数据库来实现分分布式  开发中......

## 2.2.0版本说明(2023-10-20)
- 2.2.0版本包名有很大的调整，从2.1.2升级到2.2.0版本代码需要调整和更改包名。
- 模块从新定义
    - com.swak.frame包名更改为com.swak
- 从新定义模块名称
    - swak-common 中的包名从com.swak.frame调整为com.swak.common
    - swak-core 中的包名从com.swak.frame -> com.swak.core
    - 组件archiver 中的包名从com.swak.frame.archiver调整为com.swak.archiver
    - 组件excel中的包名从com.swak.frame.excel调整为[com.swak.excel](https://mcolley.gitee.io/swak/#/components/swak-excel)
    - 组件easyJob中的包名从com.swak.frame.job调整为[com.swak.easyjob](https://mcolley.gitee.io/swak/#/components/swak-job)
    - 增加业务日志组件operatelog，包名从com.swak.frame.operatelog调整为[com.swak.operatelog](https://mcolley.gitee.io/swak/#/components/swak-operatelog) 
    - [增加hystrix熔断和线程隔离组件](https://mcolley.gitee.io/swak/#/components/swak-hystrix)
    - 增加swagger接口文档组件
    - 增加授权license组件，通过公钥私钥的方式实现，保留私钥，提供公钥的方式防止篡改。
- 部分bug修复和优化
    - 统一提供本地枚举支持数据字典接口，并隐藏前端不展示的字典数据。
    - 修复业务日志组件中返回数据是字符串问题。
- SNAPSHOT版本（2.2.0-SNAPSHOT）需要pom中配置SNAPSHOT的仓库地址
```
  <repositories>
        <repository>
            <id>sonatype</id>
            <name>sonatype</name>
            <url>https://s01.oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
            </snapshots>
        </repository>
        <repository>
     ..........
 <repositories>
```
    

## [2.1.2版本 ](https://gitee.com/mcolley/swak/tree/2.1.2/)
  新增了业务日志组件，保留com.swak.frame包名

## 1.0.1版本说明(2023-02-06)
- 新增数据归档以及历史数据删除，支持动态批量归档，动态批量删除，归档以及删除ump报警以及邮件报警。
- 新增支持excel导入导出，支持多个sheet

## 1.0.0版本说明(2021-4-02)
- 新增脚手架1.0，支持限流，熔断，扩展等功能点。

## 版本说明2021-4-02  
 - 更改为swak，全部支持springboot版本。