# Spring Bean 的装配
 依赖注入有三种方式。
 在实际环境中实现IOC容器的方式主要有两种
 - 依赖查找
 - 依赖注入
   - 构造器注入 （主要注入方式）
   - setter注入
   - 接口注入 （主要用于第三方的注入）

Spring 构造器的注入原理 就是采用反射的方式
```xml
    <bean id="role1" class="com.tomi.ioc.pojo.Role">
        <constructor-arg index="0" value="总经理" />
        <constructor-arg index="0" value="公司管理者" />
    </bean>
```
constructor-arg 元素用于定义类构造方法的参数 index 用于定义参数的位置而value 则是设置的值

Spring setter 注入

setter 注入是Spring 中最主流的注入的方式它利用 java Bean 规范所定义的setter 方法来完成注入

```xml
<bean id="role2" class="com.tomi.ioc.pojo.role">
    <property name="roleName" value="高级工程师" />
    <property name="note" value="重要人员" />
</bean>
```
## Bean的装配
Spring 提供了三种方法进行装配 
- XML中显示的配置
- java的接口和类中实现的配置  @ComponentScan
- 隐藏Bean的发现机制和自动装配的原则 @Autowired(required= true | false )
-  @Bean 装备 Bean 因为@Component 只能注解到类上，@Bean 可以注解到方法上面，并且将返回的对象作为Spring 的Bean 存放在IOC 容器当中，此时这个Bean 也能通过@Autowired 和 @ Qualifier等注解 注入到其他的Bean 当中去。

## 注解自定义Bean的初始化和销毁方法
```java
@Bean(name="字符串数组，允许配置多个Bean",autowire="标志是否是一个引用的Bean对象，默认值是Autowire.NO", initMethod="自定义初始化方法",destroyMethod="自定义销毁方法")
```

## 条件话装配Bean
@Conditional 去配置
```java
@Conditional({tomiConditional.calss})
public tomiUseConditional method(){
    .....
}
public tomiConditional implements Condition{
    @Override
    public boolean matches(ConditionContext context,AnnotatedTypeMetadata metadata){

    }
}

```
## Bean的作用域
Spring 默认提供了4种作用域 他会根据情况来决定是否生成新的对象 默认是单例模式
- 单例 只生成一个
- 原型 每次注入 或者通过SpringIoc 容器获取Bean的时候Spring 都会创建一个新的实例
- 会话 session
- 请求 request