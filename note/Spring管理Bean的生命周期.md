## Spring管理Bean的生命周期

Spring IOC容器对Bean的生命周期进行管理的过程：

1. 创建Bean的实例
2. 为Bean的属性设置值和对其他Bean的引用
3. 调用Bean的初始化方法（init方法）
4. Bean可以使用了
5. 当容器关闭时，调用Bean的销毁方法（destroy方法）

### Bean后置处理器

Bean后置处理器允许在调用初始化方法前后对Bean进行额外的处理

Bean后置处理器对IOC人容器里的所有Bean实例逐一处理，而非单一实例

对Bean后置处理来说，我们需要实现Interface BeanPostProcessor，在初始化方法被调用前后，Spring将把每一个Bean实例分别传递给上述接口的以下两个方法

```java
public  Object postProcessBeforeInitialization(Object bean, String beanName)
```

```java
public  Object postProcessBeforeInitialization(Object bean, String beanName)
```

在配置文件里使用Bean的后置处理器

```java
   <!--配置Bean的后置处理器-->
        <bean class="Cycle.MyBeanPostProcessor"></bean>
```



1. 加上Bean后置处理器后Bean的生命周期
2. 通过构造器或者工厂方法创建Bean实例
3. 为Bean的属性设置值和对其他Bean的引用
4. 将Bean实例传递给Bean后置处理器的**postProcessBeforeInitialization**
5. 调用Bean的初始化方法
6. 将Bean实例传递给Bean后置处理器的**postProcessAfterInitialization**
7. Bean可以使用了
8. 当容器关闭时，调用Bean的销毁方法



运行结果

```java
Car's constructor...
setBrand...
postProcessBeforeInitialization:Car{brand='Audi'},car
init...
postProcessAfterInitialization:Car{brand='Audi'},car
Car{brand='Audi'}
destroy...
```

Bean后置处理器的使用方法

```java
		<!--使用方法：
            实现BeanPostProcessor接口，并具体提供
            Object postProcessBeforeInitialization(Object bean, String beanName)
            Object postProcessBeforeInitialization(Object bean, String beanName)
           的实现
           bean：bean实例本身
           beanName：IOC容器配置的Bean实例
           返回值：实际上返回给用户的Bean，注意，可以在以上两个方法中修改返回的bean，甚至返回一个新的bean
        -->
        <!--配置Bean的后置处理器，不需要配置id，IOC容器会自动识别是一个BeanPostProcessor-->
        <bean class="Cycle.MyBeanPostProcessor"></bean>

```





