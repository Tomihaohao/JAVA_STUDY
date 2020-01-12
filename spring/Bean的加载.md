# Bean的加载时序
loadBeanDifinitions(resource)->new EncodedResource(resource)->encodedResource:EncodedResource->loadBeanDefinitions(encodedResource),getResource->resource:Resource->getInputStream()->inputStream:InputStream->new InputSource(InputStream)->inputSource:InputSource->doLoadBeanDefinitions(inputSource,encodedResource.getResource())->loadedBeanDefinitionNum:int

# Bean的资源加载

- 首先封装资源文件 当进入XmlBeanDefinitionReader后首先对参数Resource使用EncodedResource类进行封装
- 获取输入流，从Resource 中获取对应InputStream并构造InputSource
- 通过构造的InputSource实例和Resource实例继续调用函数doLoadBeanDefinitions
- doLoadBeanDefinitions 是真正的核心处理部分主要做了 是三件事情
获取对XML文件的验证模式，加载XML文件，并得到对应的Document,根据返回的Document注册Bean的信息。

# 解析和注册BeanDefinitions
当把文件转换成Document后，接下来的提取以及注册Bean,会使用registerBeanDefinitions(Document doc,Resource resource) 这个方法将逻辑处理委托给BeanDefinitionDocumentReader,它的重要目的就是提取root，以便于将root作为参数继续BeanDefinition的注册

doRegisterBeanDefinitions(root) 才是真正的进行解析。

首先是对profile处理，profile的作用就是可以让我们同时配置两套不同的环境
```java
<beans profile="dev" />
<beans profile="production" />
```
处理了profile后就可以进行对XML的解析了，进入parseBeanDefinition(root,this.delegate);这个方法就是判断，如果用户采用了spring默认支持的解析配置就采用parseDefaultElement否则就自己执行delegate.parseCustomElement进行解析 。

# spring默认标签的解析

默认标签的解析是在 parseDefaultElement 函数中进行的，分别对 import,alias,bean,beans 4种标签做了不同的处理。

## Bean的标签和解析
首先是委托BeanDefinitionDelegate类的parseBeanDefinitionElement方法进行元素的解析，返回BeanDefinitionHolder类型的实例bdHolder,经过这个方法以后bdHolder实例已经包含我们配置文件中的各种属性了例如 class name id alias之类的属性。随后当返回的bdHolder不为空的情况下若存在默认标签的子节点下再有自定义属性，还需要对自定义的标签再次解析，解析完成以后需要进行注册，这个操作委托给了BeanDefinitionReaderUtils的registerBeanDefinition方法。最后发出响应事件通知相关的监听器，这个Bean已经加载完成了。

解析BeanDefinition:

- 提取元素中的ID 和 name属性
-  进一步解析其他所有属性并统一封装至GenericBeanDefinition类型的实例中
- 如果检测到Bean没有指定beanName 那么默认规则为此Bean生成一个Beanname
- 将获取到的信息封装到BeanDefinitionHolder的实例中。

BeanDefinition在spring中存在三种实现形式，RootBeanDefinition,ChildBeanDefinition 以及GenericBeanDefinition三种实现均继承了AbstractBeanDefinition,其中BeanDefiniton是配置文件bean元素标签在容器中的内部表现形式，bean元素拥有的class,scope,lazy-init 等配置的属性，BeanDefinition则提供了相应的beanClas sscope,lazyInit属性。

spring通过BeanDefiniton将配置文件中的Bean配置信息转换为容器内部的表示，并将这些BeanDefiniton注册到BeanDefinitionRegistery中，spring容器中的BeanDefinitionRegister就好比是spring的内存数据库。

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


