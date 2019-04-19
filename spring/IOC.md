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
