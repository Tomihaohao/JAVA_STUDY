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

