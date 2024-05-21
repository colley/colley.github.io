## 前言
Spring事务是指在Spring框架中对于数据库操作的一种支持，它通过对一组数据库操作进行整体控制来保证数据的一致性和完整性。Spring事务可以保证在一组数据库操作执行时，要么所有操作都执行成功，要么所有操作都回滚到之前的状态，从而避免了数据不一致的情况。

### 1. Spring事务实现方式
Spring事务可以通过编程式事务和声明式事务两种方式来实现。编程式事务需要在代码中手动控制事务的开始、提交和回滚等操作，而声明式事务则是通过在配置文件中声明事务的切入点和通知等信息来自动控制事务的行为。
    
#### 1.1 Spring编程式事务
Spring编程式事务需要在代码中获取事务管理器，并通过该事务管理器获取事务对象，然后使用该事务对象来控制事务的开始、提交和回滚等操作。
- Spring框架提供两种方式来进行编程式事务管理
    - TransactionTemplate.
    - PlatformTransactionManager 的实现。

Spring团队推荐使用第一种方式进行编程式事务管理。

TransactionTemplate的方式
```java
    public Response<Void> transfer(Account from, Account to, double amount) {
    /**有返回值的操作**/    
    Response<Void> response = transactionTemplate.execute(status -> {
        try {
          from.withDraw(amount);
          to.deposit(amount);
          return Response.success();
        } catch (Exception e) {
            status.setRollbackOnly();
            throw e;
        }});
        /**无返回值的操作**/
        transactionTemplate.executeWithoutResult(status -> {
        try {
        from.withDraw(amount);
        to.deposit(amount);
        } catch (Exception e) {
        status.setRollbackOnly();
        throw e;
        }
        });
```
PlatformTransactionManager的方式
```java
  public void transferMoney(Account from,Account to,double amount) {
    DefaultTransactionDefinition def = new DefaultTransactionDefinition();
    // explicitly setting the transaction name is something that can be done only programmatically
    // 设置传播机制
    def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
    TransactionStatus status = transactionManager.getTransaction(def);
    try {
        from.withDraw(amount);
        to.deposit(amount);
        transactionManager.commit(status);
    }catch (Exception e){
        transactionManager.rollback(status);
    }
  }
```
首先通过transactionManager.getTransaction方法获取事务对象status，然后在try块中执行转账操作。
最后通过transactionManager.commit(status)提交事务。
如果在转账操作中发生了异常，则会通过transactionManager.rollback(status)回滚事务。
编程式事务的优点是灵活性高，可以根据具体的业务需求来灵活控制事务的行为。不过缺点是代码冗长，可读性差，而且容易出现错误。

#### 1.2 Spring声明式事务
Spring声明式事务需要在配置文件中声明事务管理器、事务通知等元素，然后在需要使用事务的方法上添加事务切面的注解即可。
```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {
    @AliasFor("transactionManager")
    String value() default "";
    @AliasFor("value")
    /**可以配置多个事务管理器，可以更具该属性来指定**/
    String transactionManager() default "";
    /**事务的传播行为，默认为REQUIRED**/
    Propagation propagation() default Propagation.REQUIRED;
    /**事务的隔离级别**/
    Isolation isolation() default Isolation.DEFAULT;
    /**事务的超时时间，默认值为-1，如果超过该时间的限制事务还没完成，则自动回滚事务**/
    int timeout() default -1;
    /**指定事务是否为只读事务，默认为false，比如读取数据就可以设置为true**/
    boolean readOnly() default false;
    /**指定触发事务回归的异常类型，默认回滚RuntimeException**/
    Class<? extends Throwable>[] rollbackFor() default {};
    /**指定不回归事务的异常类型，优先级最高**/
    Class<? extends Throwable>[] noRollbackFor() default {};
}
```
##### 1.2.1 @Transactional注解应该写在哪
- @Transactional 可以标记在类上面（当前类所有的方法都运用上了事务）
- @Transactional 标记在方法则只是当前方法运用事务
- 也可以类和方法上面同时都存在， 如果类和方法都存在@Transactional会以方法的为准。如果方法上面没有@Transactional会以类上面的为准

建议：@Transactional写在方法上面，控制粒度更细， 建议@Transactional写在业务逻辑层上，因为只有业务逻辑层才会有嵌套调用的情况。

### 2. 事务绑定事件
使用@TransactionalEventListener可以在事务提交前后，回滚后等阶段触发某些操作。
主要用于事务提交前后的操作，比如监控，通知等。
```java
//更新履历触发自动计算
 @TransactionalEventListener(value = DetectionCalcResumeEvent.class, fallbackExecution = true, phase = TransactionPhase.AFTER_COMPLETION)
    public void doDetectionCalcResumeEvent(DetectionCalcResumeEvent calcEvent) {
        if (Objects.nonNull(calcEvent.getResumeDataEvent())) {
            autoCalculationDetectionService.calculationByManual(calcEvent.getResumeDataEvent());
        }
    }
```
### 3. 事务注解失效以及事项
- @Transactional事务不要滥用。事务会影响数据库的QPS，另外使用事务的敌方需要考虑各方面的回滚方案，包括缓存回滚、搜索引擎、消息补偿、统计修正等。
- 不要在接口上声明@Transactional，而要在具体类的方法上使用 @Transactional 注解，否则注解可能无效。
- 不要图省事，将@Transactional放置在类级的声明中，放在类声明，会使得所有方法都有事务。故@Transactional应该放在方法级别，不需要使用事务的方法，就不要放置事务，比如查询方法。否则对性能是有影响的。
- 类内部自调用，AOP代理不生效。

  @Transactional的事务开启 ，或者是基于接口的 或者是基于类的代理被创建。所以在同一个类中一个方法调用另一个方法有事务的方法，事务是不会起作用的 。
- 默认设置下，开启事务的方法必须是public。

  @Transactional注解的方法都是被外部其他类调用才有效，故只能是public。道理和上面的有关联。故在 protected、private 或者 package-visible 的方法上使用@Transactional注解，它也不会报错，但事务无效。
- @Transactiona注解rollbackFor的异常类型不包含代码抛出的异常类型

  默认只有RuntimeExcetion会触发回滚.经验证明确实如此，所以rollbackFor可以自行设置如下：rollbackFor = {Exception.class}，当然具体业务具体处理，可能有的业务抛出的某些异常并不需要触发回滚，所以此时应该细化处理异常。
- 多线程导致事务失效。

  spring事务是用threadLocalMap实现事务与当前线程绑定的，如果是不同的线程那事务也是不同的，所以会造成事务的失效。
- 异常被try...catch未再次抛出。

  代码的异常被catch后并未再次被抛出，说明catch只是做了个对异常的处理，程序依旧往下执行，spring事务认为这是正常的业务逻辑，肯定不会回滚，所以事务失效。
- SpringBoot项目中使用多数据源切换时，TransactionManager类中加载的是否是当前数据源。

### 4. Spring事务传播机制
Spring事务传播机制是指在多个事务操作发生时，如何管理这些操作之间的事务关系。Spring事务传播机制可以通过Propagation枚举类中的不同值来指定，共包括七种不同的传播行为。
具体来说，Spring事务传播机制包括以下七种：
- REQUIRED：如果当前没有事务，则创建一个新的事务；如果当前已经存在事务，则加入该事务。这是默认的传播行为。

- SUPPORTS：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式执行。

- MANDATORY：必须在一个已存在的事务中执行，否则就抛出TransactionRequiredException异常。

- REQUIRES_NEW：创建一个新的事务，并在该事务中执行；如果当前存在事务，则将当前事务挂起。

- NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，则将当前事务挂起。

- NEVER：以非事务方式执行操作，如果当前存在事务，则抛出IllegalTransactionStateException异常。

- NESTED：如果当前存在事务，则在嵌套事务中执行；如果当前没有事务，则创建一个新的事务。

在使用Spring事务传播机制时，需要根据具体的业务需求来选择合适的传播行为，以保证事务的正确性和可靠性。同时，需要注意事务的异常处理等问题，以避免数据不一致或丢失的情况发生。

## 思考

- 分布式事务 ?
 - CAP，BASE 原理 ，TCC,SAGA 模型的原理？
 - 数据不一致性产生的原因？
