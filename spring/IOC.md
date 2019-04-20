# IOC

## 基本阐述
控制反转是一种通过描述（可以是XML 和 注解）并通过第三方去产生或获取特定对象的方式。

在Spring中实现控制反转的是IOC容器，其实现的方法是依赖注入

## IOC容器

IOC 就是个大水桶，里面装着各种bean.

spring ioc 容器的设计主要是基于 BeanFactory 和 ApplicationContext 两个接口。

```java
public interface BeanFactory{
    String FACTORY_BEAN_PREFIX="&";
    Object getBean(String name) throws BeansException;
    <T> T getBean(String name,Object ...args) throws BeansException;
    <T> T getBean(Class<T> requiredType,Object... args) throws BeansException
    boolean containsBean(String name);
    boolean isSingleton(String name);
    boolean isPrototype(String name);
    // ......     .........        .......
    boolean isTypeMatch(String name)
}

```

## IOC 容器的初始化和依赖注入

Bean的定义和初始化在SpringIoc容器中是两大步骤，它是先定义然后初始化和依赖注入的

## Bean的定义

 - Resource 定位 一般通过注解或者XML的方式 SpringIoC 容器根据开发者的配置 进行资源的定位
 - BeanDefinition的载入 这个时候只是将Resource定位到的信息，保存到Bean定义的BeanDefinition中，此时并不会创建Bean
 - BeanDefinition的注册 这一步就是将BeanDefinition 的信息发布到Spring IoC容器中 但是对应的Bean实例 依旧没有创建
 - 最后SpringIoC 会自动的初始化 完成依赖注入

## Bean的生命周期

- 初始化
- 依赖注入
- setBeanName 接口BeanNameAware
- setBeanFactory 接口 BeanFactoryAware
- setApplicationContext方法 接口ApplicationContextAware
- postProcessBeforeInitialization方法 BeanPostProcessor的预初始化方法 针对所有的Bean
- afterPropertiesSet方法 接口InitializingBean 
- 自定义初始化方法 init-method
- postProcessAfterInitialization BeanPostProcessor的后初始化方法
- 生存期
- destory 方法
- 自定义销毁方法


