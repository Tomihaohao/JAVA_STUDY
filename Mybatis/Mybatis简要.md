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

## typeAliases 别名

## typeHandler 类型转换器

```java
public interface TypeHandler<T>{
    void setParameter(PreparedStatement ps,int i,T parameter,jdbcType jdbcType) throws SQLException
    T getResult(ResultSet rs,String columnName) throws  SQLException
    T getResult(ResultSet rs,int columnIndex) throws SQLException
    T getResult(CallableStatement cs,int columnIndex) throws SQLException
}
//其中 T 是泛型 专指javaType 例如 这样 implements TypeHandler<String>
//setParameter 方法是使用 typeHandler 通过PreparedStatement 对象进行设置SQL参数的时候使用的具体方法，其中I是参数在SQL的下标，parameter 是参数，jdbcTyoe是数据库类型

//自定义 typeHandler
public class MyTypeHandler implements TypeHandler<String>{
    @Override
    public void setParameter(PreparedStatement ps,int i,String parameter,jdbctype jdbcType) throws SQLException{
        logger.info("设置string 参数");
        ps.setString(i,parameter);
    }
    @Override
    public String getResult(ResultSet rs,String columnName) throws SQLException{
        String result = rs.getString(columnName);
        logger.info("读取string 参数1")
        return result
    }
    @Override
    public String getResult(ResultSet rs,String columnName) throws SQLException{
        String result = rs.getString(columnIndex);
        logger.info("读取string 参数2")
        return result
    }
    @Override
    public String getResult(ResultSet rs,String columnName) throws SQLException{
        String result = rs.getString(columnIndex);
        logger.info("读取string 参数3")
        return result
    }
    
}
```
```xml
<typeHandlers>
    <typeHandler jdbcType="VARCHAR" javaType="string" handler="MyTypeHandler">
</typeHandlers>
```

## 插件

 ### 事务管理器
 ```java
 public interface Transaction{
     Connection getConnection() throws SQLException;
     void commit() throws SQLException;
     void rollback() throws SQLException;
     void close() throws SQLException;
     Integer getTimeout() throws SQLException;
 }

 //自定义事务
 public class MyTransactionFactory implements TransactionFactory{
     @Override
     public void setProperties(Properties props){
     }
     @Override
     public Transaction newTransaction(Connection conn){
         return new MyTransaction(conn);
     }
     @Override
     public Transaction newTransaction(DataSource dataSource,TransactionIsolationLevel level,boolean autoCommit){
         return new MyTransaction(dataSource,level,autoCommit);
     }
 }

 public class MyTransaction extends JdbcTransaction implements Transaction{
     public MyTransaction(DataSource ds,TransactionIsolatinLevel desiredLevel,boolean desiredAutoCommit){
         super(ds,desiredLevel,desiredAutoCommit);
     }
     public MyTransaction(Connection connection){
         super(connection)
     }
     @Override
     public Connection getConnection() throws SQLException{
         return super.getConnection();
     }
     @Override
     public void commit() throws SQLException{
         super.commit();
     }
     @Override
     public void rollback() throws SQLException{
         return super.rollback();
     }
     @Override
     public void close() throws SQLException{
         return super.close();
     }
     @Override
     public Integer getTimeout() throws SQLException{
         return super.getTimeout();
     }
 }
 ```

 ## 映射器
  ```java
  public class Pageparams{
      private int start;
      private int limit;
      /**setter and getter**/
  }
  public List<Role> findByMix(@Param("params") RoleParams roleParams,@Param("page") PageParam PageParam)
  ```
  ```xml
  <select id="findByMix" resultType="role">
    select id,role_name as RoleName,note from t_role where role_name like concat('%',#{params.roleName},'%') and note like concat('%',#{params.note},'%')
    limit #{page.start},#{page.limit}
  </select>
  ```
  ### 传递多个参数
  ```java
  public List<Role> findRoleMap(Map<String,Object parameterMap);
  public List<Role> findRoleMapAnnotation(@Param("roleName") String rolename)

  ```

  ### resultMap
  ### 动态SQL
  - if set where bind foreach

  ## MyBatis解析和运行原理

  - 第一步 读取配置文件缓存到Configuration对象，用来创建SqlSessionFactory
  - 第二步 SqlSession 的执行过程

  ### 构建SqlSessionFactory
  - 通过XMLConfigBuilder 解析配置XML
  - 用 Confinguration 对象去创建SqlSessionFactory, 默认实现类 DefaultSqlSessionFactory

 ### 构建映射器的内部组成
 - MappedStatement 保存一个映射器节点 (select,insert,delete,update)
 - SqlSource 是提供BoundSql对象的一个地方 它的作用就是根据上下文和参数解析生成需要的SQL
 - BoundSql 是一个结果对象，就是 SqlSource 通过对SQL和参数的联合解析得到的SQL和参数，它是建立SQL和参数的地方
 
 ### SqlSession 下的四大对象
  - Executor 代表执行器 由它来调度 StatementHandler ParameterHandler ResultSetHandler 等来执行对应的SQL
  - StatementHandler 的作用是使用数据库的Statement 来执行操作，它是核心 许多插件都是通过拦截它来实现的
  - ParameterHandler 是用来处理SQL参数的
  - ResultSetHandler 是进行数据集的封装返回处理

  