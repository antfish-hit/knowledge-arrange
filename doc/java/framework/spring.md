# Spring

## 静态代理

### 定义
指手动编写代理类以实现代理模式的方式。

### 三方关系梳理
代理类和委托类**实现同一个接口**，代理类和委托类是**组合**关系，只向调用类暴露代理方的方法。
在调用类和委托类(有多个)之间加入代理类，调用类调用代理类方法，代理类再调用委托类方法处理业务

### 有什么作用？
解除调用类和委托类的耦合关系，统一管理**同一种类型**的委托类。

### 优点和缺点
- 优点：
    - 调用类和委托类之间**解耦**，结构简单。
- 缺点：
    - 代理类和委托类实现了相同的接口，修改接口会导致代码成倍增加，增加维护难度。
    - 代理类只能管理**一种类型**的委托类，无法应对需要管理多种类型委托类的场景。

### 应用场景
扩展**同一种类型**的业务，以实现不同的功能。

## 动态代理
### 定义
在运行时创建指定类或接口的代理实例以实现代理模式的方式。

### 实现方式
- JDK动态代理：实现`InvocationHandler`接口并编写方法代理的逻辑，然后通过`java.lang.reflect`包中的`Proxy`类的`newProxyInstance`方法生成代理类实例。
    - 优点：简单高效
    - 缺点：被代理的类必须实现某一个接口

```java
public class DynamicProxyAgent {
    static interface ProxyInterface {
        void show();
    }

    static class ProxyClass implements ProxyInterface {
        @Override
        public void show() {
            System.out.println(String.format("%s runs real method: show()", this.getClass().getName()));
        }
    }

    static class MyProxyHandler implements InvocationHandler {
        private Object proxy;

        MyProxyHandler(Object proxy){
            this.proxy = proxy;
        }
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println(String.format("%s runs logic before real method.", this.getClass().getName()));
            Object ret = method.invoke(this.proxy, args);
            System.out.println(String.format("%s runs logic after real method.", this.getClass().getName()));
            return ret;
        }
    }

    static Object agentWithJavaImplementation(Class interfaceClass, Object proxy) {
        return Proxy.newProxyInstance(interfaceClass.getClassLoader(), new Class[]{interfaceClass}, new MyProxyHandler(proxy));
    }

    public static void main(String[] args) {
        ProxyInterface proxy = (ProxyInterface) DynamicProxyAgent.agentWithJavaImplementation(ProxyInterface.class, new ProxyClass());
        proxy.show();
    }
}
```
- CGlib：实现`MethodInterceptor`接口并编写方法代理的逻辑，然后通过`Enhancer`类的`create()`方法生成代理类实例。
    - 优点：无需实现接口，任意类皆可
    - 缺点：内部实现复杂，效率比JDK要低

```java
public class DynamicProxyAgent {
    static class CGlibAgent implements MethodInterceptor{
        private Class proxy;

        CGlibAgent(Class proxy){
            this.proxy = proxy;
        }

        public Object getInstance(){
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(proxy);
            enhancer.setCallback(this);
            return enhancer.create();
        }

        @Override
        public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
            System.out.println("before invoking...");
            Object ret = proxy.invokeSuper(obj, args);
            System.out.println("after invoking...");
            return ret;
        }
    }

    public static void main(String[] args) {
        CGlibAgent cGlibAgent = new CGlibAgent(ProxyClass.class);
        ProxyClass cgProxy = (ProxyClass)cGlibAgent.getInstance();
        cgProxy.show();
    }
}
```

### 优点和缺点
- 优点：调用类和委托类之间**解耦**，且可以用于多种类型委托类的场景，实现复杂程度随业务而定。
- 缺点：结构较复杂。

### 应用场景
- 日志系统、权限管理等通用业务场景。

## AOP

### 定义
**Aspect Oriented Programming**，即**面向切面编程**，指将**业务逻辑**和**系统服务**分离开来的一种软件开发方式。核心是**动态代理**。

### 基本概念
- 通知(Adivce)(5种)，声明代理方法以及执行时机
    - Before：在方法执行前调用
    - After：在方法执行后调用，无论方法是否执行成功
    - After-returning：在方法成功执行之后调用
    - After-throwing：在方法抛出异常后调用
    - Around：方法执行前和执行后都调用
- 切点(Pointcut): 目标方法
- 连接点(Join point)：目标方法的信息集合类
- 切面(Aspect)：代理方法信息集合类
- 引入(Introduction)：创建类代理对象
- 织入(Weaving)：创建方法代理对象

## IOC和DI

### 定义
`Inversion of Control`，控制反转，框架控制对象的创建；`Dependency Injection`，依赖注入，给标记的类字段赋值。

### 联系
DI依赖于IOC实现操作。

