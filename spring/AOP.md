# AOP
AOP 底层技术动态代理
```java
//interceptor 接口
public interface Interceptor{
    public void before(Object obj);
    public void after(Object obj);
    public void afterReturning(Object obj);
    public void afterThrowing(Object obj);
}

//代理工厂
public class ProxyBeanFactory{
    public static <T> T getBean(T obj,Interceptor interceptor){
        return (T) ProxyBeanUtil.getBean(obj,interceptor)
    }
}

//服务角色 接口
public interface RoleService{
    public void printRole(Role role);
}

//实现类
public class RoleServiceImpl implements RoleService{
    @override
    public void printRole(Role role){
        System.out.println("hahahhah");
    }
}

//拦截器实现
public class RoleInterceptor implements Interceptor{
    @override
    public void before(Object obj){
        System.out.println("before");
    }

    @override
    public void after(Object obj){
            System.out.println("after")
    }

    @override
    public void afterReturning(Object obj){
        System.out.println("after returning");
    }

    @override
    public void afterThrowing(Object obj){
        System.out.println("after throwing");
    }
}

//主函数 测试
public class GameMain{
    public static void main(String[] args){
        RoleService rs = new RoleServiceImpl();
        Interceptor interceptor = new RoleInterceptor();
        RoleService proxy = ProxyBeanFactory.getBean(rs,interceptor);
        proxy.printRole();

    }
}

//ProxyBeanUtil 的实现 一个简单的AOP
public class ProxyBeanUtil implements InvocationHandler{
    //被代理的对象
    private Object obj;
    //拦截器
    private Interceptor interceptor=null;

    //动态代理get Bean
    //obj 被代理的对象
    //interceptor 拦截器
    // return 动态代理对象
    public static Object getBean(Object obj,Interceptor interceptor){
        ProxyBeanUtil _this = new ProxyBeanUtil(); //初始化构造函数
        _this.obj = obj;
        _this.interceptor=interceptor;
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(),obj.getClass().getInterfaces(),_this);
    }

    public Object invoke(Object proxy,Method method,Object[] args) throws Throwable{
        Object retobj =null;
        boolean exceptionFlag = false;
        //before 方法
        interceptor.before(obj);
        try{
            retobj=method.invoke(obj,args);
        }catch(Exception ex){
            exceptionFlag=true
        } finally{
            interceptor.after(obj);
        }
        if(exceptionFlag){
            //after throwing;
            interceptor.afterThrowing(obj)
        }else{
            interceptor.afterReturning(obj)
        }
        return retobj;
    }
}
```

Spring Aop 是一种基于方法的拦截AOP。

@AspectJ 注解开发AOP

在spring中只要使用了@Aspect 注解一个类。SpringIoc 容器就会认为这是一个切面。

```java
@Aspect
public class RoleAspect{
    @Before("execution(*com.tomi.aop.RoleServiceImpl.printRole(..))")
    public void before(){
        System.out.println("before ...");
    }

    @After("execution(*com.tomi.aop.RoleServiceImpl.printRole(..))")
    public void after(){
        System.out.println("after");
    }

    @AfterReturning("execution(*com.tomi.aop.RoleServiceImpl.printRole(..))")
    public void afterReturning(){
        System.out.println("afterReturning");
    }

    @AfterThrowing()
    public void afterThrowing(){
        System.out.println("afterThrowing");
    }

    //execution: 代表执行方法的时候会触发
    // * 代表任意返回类型的方法
    // com.tomi.aop.RoleServiceImpl 代表类的全限定名
    // printRole 被拦截的方法名称
    // （...） 任意的参数

    //定义一个切点 可以简洁编码
    @Pointcut("execution(*com.tomi.aop.RoleServiceImpl.printRole(..))")
    public void print(){}
    @Before("print()")
    public void before(){
        System.out.println("before....");
    }
    //...... 类似上面这种
}

//通过注解方式调用 AOP
@Configuration
@EnableAspectJAutoProxy
//启用AspectJ 框架的自动代理
@ComponentScan("com.tomi.aop")
public class AopConfig{
    @Bean
    public RoleAspect getRoleAspect(){//生成一个切面实例
        return new RoleAspect();
    }
}
public class Main{
    public static void main(String[] args){
        ApplicationContext ctx = new AnnotationConfigApplicationContext(AopConfig.class);

        RoleService roleService = (RoleService) ctx.getBean(RoleService.class);
        roleService.printRole();
        //AOP已经通过AOP织入约定的流程当中了
    }
}


```
spring 可以定义多个切面 通过 @Order 可以规定执行的顺序
