## 原理
使用jwt的方式集成spring security安全登录框架

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
        <dependency>
            <groupId>io.gitee.mcolley</groupId>
            <artifactId>swak-security-jwt-boot-starter</artifactId>
        </dependency>
```

## 功能特性
- 支持白名单设置，提供登录的扩展等

## 使用方式
```java
@Configuration
@EnableWebSecurity(debug = true)
@EnableGlobalMethodSecurity(prePostEnabled = true,securedEnabled = true)
public class SecurityJwtConfig extends WebSecurityConfigurerAdapter {

    @Resource
    private SwakWebSecurityConfigurer swakWebSecurityConfigurer;


    @Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {
        swakWebSecurityConfigurer.configure(httpSecurity);
     }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        swakWebSecurityConfigurer.configure(auth);
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    @Override
    public void configure(WebSecurity web) {
        swakWebSecurityConfigurer.configure(web);
    }
}
```