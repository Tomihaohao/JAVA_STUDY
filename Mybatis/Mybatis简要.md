# MyBatis
MyBatis 核心组件 分为下面4个部分
- SqlSessionFactoryBuilder 构造器
- SqlSessionFactory 工厂接口
- SqlSession 会话
- SqlMapper 映射器

## MyBatis 配置
```xml
<configuration>
    <properties /> 属性
    <settings /> 设置
    <typeAliases /> 类型命名
    <typeHandlers /> 类型处理器
    <objectFactory /> 对象工厂
    <plugins /> 插件
    <environments> 配置环境
        <enviroment>
            <transactionManager />
            <dataSource />
        </enviroment>
    </environments>
    <databaseIdProvider /> 数据库厂商标识
    <mappers /> 映射器    
</configuration>
```

## setting
- cacheEnabled 映射器中配置缓存全局开关
- lazyLoadingEnable 延迟加载全局开关
- aggressiveLazyLoading 延迟加载
- autoMappingBehavior None 取消自动映射 Partial 表示只会自动映射 FULL 完全映射
- defaultExecutorType Simple 普通执行器 Reuse 重用预处理器 Batch将重用语句并执行批量更新
- mapUnderscoreToCamelCase 是否开启自动驼峰映射

