# Bean 的加载
spring 中Bean 的加载要比 xml的解析 复杂很多
```java
MyBean bean = (MyBean) bf.getBean("MyBean");

public Object getBean(String name){
    return doGetBean(name,null,null,false);
}
```
我们可以看到 在获取bean 的时候实际上是 doGetBean 这个方法来实现的；

## doGetBean bean的加载过程
- 转换对应的beanName
- 尝试从缓存中加载单例

    单例在spring容器中只会被创建一次，后续在获取bean 的时候就会从缓存中获取如果加载不成功，就会尝试从 singletonFactories中加载。 因为在创建单例 bean 的时候 会存在依赖注入的情况，为了避免循环依赖，spring 中创建 bean 的原则是 不等 bean 创建完成就将创建 bean 的ObjectFactory 提早曝光加入到缓存中，一旦下一个bean 创建依赖上一个的时候就直接使用ObjectFactory 来创建依赖

- bean的实例化 通过getObjectForBeanInstance 来实例化 Bean
- 原型模式依赖检查
- 检测 parentBeanFactory 
- 将存储的xml 配置文件 GernericBeanDefinition 转换为 RootBeanDefinition
- 寻找依赖

    在初始化某一个Bean 的时候会先初始化 这个Bean 所对应的依赖
- 类型转换
- 针对不同方的scope 进行 ben的创建

    默认 singleton prototype request session GlobalSession Bean的作用域

- 类型转换

## FactoryBean

```java
public interface FactorBean<T>{
    T getObject() throws Exception; //返回由FactoryBean 创建的 bean 实例
    Class<?> getObjectType(); //返回由 FactoryBean 创建的 bean 实例的作用域是 singleton 还是 prototype;
    boolean isSingleton();//返回FactoryBean 创建的Bean类型
}
```
- 缓存中获取bean
- 从bean的实例中获取对象
- 获取单例
- 准备创建bean

    - 实例化前置处理

        postProcessBeforeInstantiation postProcessAfterInitialization
    - 循环依赖

        spring 中如何解决循环依赖呢？
        
        构造器的循环依赖是无法解决的 只能抛出异常 BeanCurrentlyInCreationException

        对于setter 的循环依赖 可以通过在创建单例的 时候提前暴露一个 ObjectFactory 并将当前创建的 bean 作为一个标识放到创建的bean 池中，在其他bean 需要依赖的时候，就会通过这个 Objectfactory 来创建需要的bean

    - prototype 范围的依赖处理

        对于prototype 作用域bean spring 容器无法完成依赖注入
    
    - 如果是单例则需要首先清除缓存
    - 实例化bean 将 BeanDefinition 转换为 BeanWrapper
    - MergedBeanDefinitionPostProcessor 的应用 bean 合并后的处理，Autowired 类型预解析
    - 依赖处理
    - 属性填充
    - 循环依赖检查
    - 注册 Disposable
    - 完成创建并返回
