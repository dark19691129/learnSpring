## Spring AOP

```java
public class ArithmeticCaculatorImpl implements ArithmeticCaculator{

    @Override
    public int add(int i, int j) {
        System.out.println("The method add begins with["+i+","+j+"]");
        int result=i+j;
        System.out.println("The method add ends with "+result);
        return result;
    }

    @Override
    public int sub(int i, int j) {
        System.out.println("The method sub begins with["+i+","+j+"]");
        int result=i-j;
        System.out.println("The method sub ends with "+result);
        return result;
    }

    @Override
    public int mul(int i, int j) {
        System.out.println("The method mul begins with["+i+","+j+"]");
        int result=i*j;
        System.out.println("The method mul ends with "+result);
        return result;
    }

    @Override
    public int div(int i, int j) {
        System.out.println("The method div begins with["+i+","+j+"]");
        int result=i/j;
        System.out.println("The method div ends with "+result);
        return result;
    }
}

```

上述类既要实现相对应的运算功能。又要实现客户额外的日志打印需求，这样就会导致：

代码混乱，代码分散

### 使用动态代理

改造后的Main

```java
public class Main {
    public static void main(String[] args) {


        ArithmeticCaculator target=new ArithmeticCaculatorImpl();
        ArithmeticCaculator proxy=new ArithmeticCaculatorLoggingProxy(target).getLoggingProxy();

        int result=proxy.add(1,2);
        System.out.println("--->"+result);

        result=proxy.sub(4,3);
        System.out.println("--->"+result);

        result=proxy.mul(4,3);
        System.out.println("--->"+result);

        result=proxy.div(4,3);
        System.out.println("--->"+result);

    }
}
```

ArithmeticCaculatorLoggingProxy

```java
public class ArithmeticCaculatorLoggingProxy {

    private ArithmeticCaculator target;

    public ArithmeticCaculatorLoggingProxy(ArithmeticCaculator target) {
        this.target = target;
    }

    public ArithmeticCaculator getLoggingProxy(){
        ArithmeticCaculator proxy=null;

        //代理对象由哪一个类加载器加载
        ClassLoader loader=target.getClass().getClassLoader();
        //代理对象的类型（也就是target的类型），即其中有哪些方法
        Class [] interfaces=new Class[]{ArithmeticCaculator.class};
        //当调用代理对象其中的方法时，该执行的代码
        InvocationHandler h= new InvocationHandler() {
            /**
             *
             * @param proxy 正在返回的那个代理对象，一般情况下，在invoke方法中都不使用该对象
             * @param method 正在被调用的方法
             * @param args 调用方法时，传入的参数
             * @return
             * @throws Throwable
             */
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                String methodName=method.getName();
                //日志打印
                System.out.println("The method "+methodName+" begins with"+ Arrays.asList(args));
                //执行方法
                Object result=method.invoke(target,args);
                //日志打印
                System.out.println("The method "+methodName+" ends wirh "+ result);
                return 0;
            }
        };
        proxy= (ArithmeticCaculator) Proxy.newProxyInstance(loader,interfaces,h);
        return  proxy;
    }

}

```

但由于在工作中使用动态代理对程序员的要求很高，所以出现了Spring的AOP（面向切面编程）

![面向切面编程](C:\Users\蔡梓文\Desktop\捕获.PNG)

AOP术语

![捕获1](C:\Users\蔡梓文\Desktop\捕获1.PNG)



## SpringAOP前置通知（基于注解）

AspectJ：Java社区里最完整最流行的AOP框架

（1）加入jar包：aspectjweaver.jar

（2）在配置文件中加入aop的命名空间：（IDEA会自动配置好）

 （3）基于注解的方式：

- 在配置文件中加入如下配置

```java
 <!--是Aspect注解起作用：自动为匹配的类生成代理对象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

- 把横切关注点的代码抽象到切面的类

​	i：切面首先是一个IOC容器中的bean，即加入@Component注解

​	ii：切面还需加入@Aspect注解

- 在类中声明各种通知：

  @Before	@After	@AfterRunning	@AfterThrowing	@Around

  声明一个方法；在方法前加入@Before注解；

- 可以在通知方法中声明一个类型为Joinpoint的参数，然后就能访问链接细节，如方法名称和参数值

主要任务类：

```java
@Component
public class ArithmeticCaculatorImpl implements ArithmeticCaculator {
    @Override
    public int add(int i, int j) {
        int result=i+j;
        return result;
    }
    @Override
    public int sub(int i, int j) {
        int result=i-j;
        return result;
    }
    @Override
    public int mul(int i, int j) {
        int result=i*j;
        return result;
    }
    @Override
    public int div(int i, int j) {
        int result=i/j;
        return result;
    }
}
```



日志打印类：

```java
@Aspect
@Component
public class LoggingAspect {
    //声明该方法是一个前置通知：在目标方法开始前执行
    @Before("execution(public int aopImpl.ArithmeticCaculator.add(int ,int))")
    public void beforeMethod(JoinPoint joinPoint){
        String methodName=joinPoint.getSignature().getName();
        List<Object> args= Arrays.asList(joinPoint.getArgs());
        System.out.println("The method "+methodName+"begins with "+args);
    }
}
```



## 后置通知

后置通知是在**连接点完成**之后执行的，即连接点返回结果或者抛出异常的时候（无论是否抛出异常，后置通知都会执行）

```java
    //后置通知：在目标方法执行后（无论是否发生异常），执行的通知
    //在后置通知中还不能访问目标方法执行的结果
    @After("execution(public int aopImpl.ArithmeticCaculator.add(int ,int))")
    public void afterMethod(JoinPoint joinPoint){
        String methodName=joinPoint.getSignature().getName();
        System.out.println("The method "+methodName+" ends");
    }
```



## 返回通知

若需要一些通知是在**方法正常运行**后执行的，就称为返回通知

```java
    /**
     * 返回通知
     * 在方法正常结束执行后执行的代码
     * 所以返回通知是可以访问到方法的返回值的！！！！！！！！！
     * @AfterReturning(value="execution(public int aopImpl.ArithmeticCaculator.add(int ,int))",returning="result")
     *
     * @param joinPoint
     */
    @AfterReturning(value="execution(public int aopImpl.ArithmeticCaculator.add(int ,int))",returning="result")
    public void afterReturning(JoinPoint joinPoint,Object result){
        String methodName=joinPoint.getSignature().getName();
        System.out.println("The method "+methodName+" ends with "+result);
    }
```

## 异常通知

```java
    /**
     * 在目标方法出现异常时会执行的代码
     * 可以访问到异常对象；且可以在出现特定异常（指定的Exception ex）时执行通知代码
     * @param joinPoint
     * @param ex
     */
    @AfterThrowing(value = "execution(public int aopImpl.ArithmeticCaculator.add(int ,int))",throwing = "ex")
    public void afterThrowing(JoinPoint joinPoint,Exception ex){
        String methodName=joinPoint.getSignature().getName();
        System.out.println("The method "+methodName+" occurs Exception "+ex);
    }
```

##  环绕通知

功能性最强的，但不是最常用的

```java
    /**
     * 环绕通知
     * 环绕通知需要携带
     */
    @Around("execution(public int aopImpl.ArithmeticCaculator.add(int ,int))")
    public Object aroundMethod(ProceedingJoinPoint pjd){
        Object result=null;
        String methodName=pjd.getSignature().getName();

        try {
            //前置通知
            System.out.println("The method "+methodName+"begins with "+Arrays.asList(pjd.getArgs()));
            result=pjd.proceed();
            //返回通知
            System.out.println("The method "+methodName+" ends with "+result);
        } catch (Throwable e) {
            System.out.println("The method "+methodName+" occurs Exception "+e);
            throw new RuntimeException(e);
        }
        //后置通知
        System.out.println("The method "+methodName+" ends");
        return result;
    }
```

### 指定切面的优先级

```java
@Order(1)

@Order(2)	//数字越小优先级越高
```



### 重用切点表达式、

在每个方法前的注解中加入execution(public int aopImpl.ArithmeticCaculator.*(int ,int))不太简洁

可以：

```java
	@Pointcut("execution(public int aopImpl.ArithmeticCaculator.*(..))")
    public void declareJointPointExpression(){}
```

则之后可以写成

```java
	@Before("declareJointPointExpression()")
    public void beforeMethod(JoinPoint joinPoint){
        String methodName=joinPoint.getSignature().getName();
        List<Object> args= Arrays.asList(joinPoint.getArgs());
        System.out.println("The method "+methodName+"begins with "+args);
    }
```

注意：由于aspectJweaver版本过低，又可能在使用@Pointcut的过程中会报错，提高aspectJweaver的版本即可

aspectjweaver-1.8.9.jar



## 基于配置文件的方式配置AOP

```java
<bean id="arithmeticCaculator" class="aopImplXml.ArithmeticCaculatorImpl">
    </bean>

    <!--配置切面的bean-->
    <bean id="loggingAspect" class="aopImplXml.LoggingAspect"></bean>

    <!--配置AOP-->
    <aop:config>
        <!--配置切点表达式-->
        <aop:pointcut id="pointcut" expression="execution(public int aopImplXml.ArithmeticCaculator.*(..))"/>
        <!--配置切面及通知-->
        <aop:aspect ref="loggingAspect">
            <aop:before method="beforeMethod" pointcut-ref="pointcut"/>
            <aop:after method="afterMethod" pointcut-ref="pointcut"/>
            <aop:after-throwing method="afterThrowing" pointcut-ref="pointcut" throwing="ex"/>
            <aop:after-returning method="afterReturning" pointcut-ref="pointcut" returning="result"/>
        </aop:aspect>
    </aop:config>
```

