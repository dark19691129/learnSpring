## Spring通过工厂方法配置Bean

### 静态工厂方法

```java
    <!--通过静态工厂方法来配置Bean实例，不是配置静态工厂方法实例，而是配置Bean实例-->
    <!--
        class属性：指向静态工厂方法的全类名
        factory-method：指向静态工厂方法的名字
        constructor-arg：如果工厂方法需要传入参数，则使用constructor-arg来配置参数
    -->
    <bean id="car1" class="BeanFactory.StaticCarFactory"
        factory-method="getCar">
        <constructor-arg value="Audi"></constructor-arg>
    </bean>
```



### 实例工厂方法

并不是通过静态方法返回对象，而是通过非静态方法 

实例工厂的工厂方法

```java
  	<!--实例工厂方法-->
    <!--配置工厂的实例-->
    <bean id="carFactory" class="BeanFactory.InstanceCarFactory"></bean>

    <!--通过实例工厂方法来配置bean-->
    <!--
        factory-bean属性：指向实例工厂方法的bean 
        factory-method：指向静态工厂方法的名字
        constructor-arg：如果工厂方法需要传入参数，则使用constructor-arg来配置参数
    -->

    <bean id="car2" factory-bean="carFactory" factory-method="getCar">
        <constructor-arg value="Ford"></constructor-arg>
    </bean>
```

