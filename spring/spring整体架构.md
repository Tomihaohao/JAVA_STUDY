# spring整体架构

- Core Container 核心容器

    包含有Core,Beans,Context,和 Expression Language 模块

    Core和Bean 模块是框架的基础部分 提供IOC 和 DI，基础概念是 BeanFactory 它提供了Factory模式的经典实现，来消除对程序性单例模式的需要，允许你从程序逻辑中分离出依赖关系配置。

- Data Access Integration  数据访问模块
- Web

    web模块提供了基础面向web的特性，例如多文件上传，使用servlet listenners 初始化IOC容器，以及 一个面向web的应用上下文

- AOP 

    aop模块提供了一个符合AOP联盟标准的面向切面编程的实现