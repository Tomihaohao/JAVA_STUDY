# 类型信息 RTTI
- Class对象仅仅在需要的时候才被加载，static 初始化是在类加载时进行的
- 类的准备工作 加载-》链接-》初始化
- newInstance()
- instancof isInstanceof
- 反射 
- 动态代理

    ```java
    import java.lang.reflect.*;
    import static net.mindview.util.Print.*;

    class MethodSelector implements InvocationHandler{
        private Object proxied;
        public MethodSelector(Object proxied){
            this.proxied=proxied;
        }
        public Object invoke(Object proxy,Method method,Object[] args) throws Throwable{
            if(method.getName().equals("interesting")){
                print("Proxy detected the interesting method");
            }
            return method.invoke(proxied,args);
        } 
    }
    interface SomeMethods{
        void boring1();
        void boring2();
        void interesting(String arg);
        void boring3();
    }
    class Implementation implements SomeMethods{
        public void boring1(){ print("boring1") }
        public void boring2(){ print("boring2") }
        public void boring3(){ print("boring3") }
        public void interesting(String arg){
            print("interesting"+arg)
        }
    }
    class SelectingMethods{
        public static void main(String[] args){
            SomeMethods proxy=(SomeMethods)Proxy.newProxyInstance(
                SomeMethods.class.getClassLoader(),
                new Class[]{ SomeMethods.class},
                new MethodSelector(new Implementation()));
            proxy.boring1();
            proxy.boring2();
            proxy.interesting("bonobo");
            proxy.boring3();
        }
    }
    ```
