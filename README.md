<div class="theme-default-content content__default">

#  介绍

# SWAK-Cloud

基于SpringBoot、Mybatis，Dubbo的微服架构系统

[![](https://img.shields.io/github/license/mashape/apistatus.svg)](https://gitee.com/mcolley/swak)
[![](https://img.shields.io/maven-central/v/io.gitee.mcolley/swak-core.svg)](https://mcolley.gitee.io/swak/)
 <div class="custom-block tip">

提示

一直想做一套通用性封装的基础框架，看了很多优秀的开源项目但是发现没有合适的。于是利用空闲休息时间和在工作中的积累的规范和检验自己集成和沉淀一套不同方案组件后端架构。如此有了swak（swiss-army-knife）。
</div> 

**SWAK-Cloud**基于 Spring Boot2构建微服务架构，集成通用的基础技术模块和业务模块，快速搭建可直接部署。统一的接口出入参和公共规范，支持多环境多机房，支持多业务多场景扩展点，支持容错，限流，隔离，兜底，支持接口监控业务监控。集成通用工具类，支持白名单，穿越预演能力。

### Latest Version
[![Maven Central](https://img.shields.io/maven-central/v/io.gitee.mcolley/swak-core.svg)](https://search.maven.org/search?q=g:io.gitee.mcolley%20a:swak*)

```xml
 <dependency>
    <groupId>io.gitee.mcolley</groupId>
    <artifactId>swak-bom</artifactId>
    <version>Latest Version</version>
    <type>pom</type>
    <scope>import</scope>
 </dependency>

```

### 依赖中间件版本


| 中间件名称    | 版本           | 备注        |
| ------------- | -------------- | ----------- |
| `spring boot` | 2.3.12.RELEASE | spring boot |


</div> 

**在线体验**

**系统需求**
*   JDK >= 1.8
*   MySQL >= 5.7
*   Maven >= 3.6
