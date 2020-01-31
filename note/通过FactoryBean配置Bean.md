## 通过FactoryBean配置Bean

配置文件：

```java
        <!--
            通过FactoryBean来配置Bean的实例
            class：指向FactoryBean的全类名
            property：配置FactoryBean的属性

            但实际返回的实力却是FactoryBean的getObject()方法返回的实例
        -->
        <bean id="car" class="FactoryBean.CarFactoryBean">
            <property name="brand" value="BMW"></property>
        </bean>
```

FactoryBean类

```java

public class CarFactoryBean implements FactoryBean<Car> {


    private String brand;

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    @Override
    public Car getObject() throws Exception {
        return new Car("BMW",55000);
    }

    /**
     * 返回bean的类型
     * @return
     */
    @Override
    public Class<?> getObjectType() {
        return Car.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}

```

结果：

```java
Car{brand='BMW', price=55000.0}
```