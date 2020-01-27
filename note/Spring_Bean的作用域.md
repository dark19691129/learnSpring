## Spring_Bean的作用域

**使用bean的scope来配置bean的作用域**

### singleton

singleton：默认值，容器初始化时创建bean实例，在整个容器的生命周期内只创建这一个bean实例，单例的。在创建容器时，这个bean已经被实例化好了。

Car类

```java
    public Car(){
        System.out.println("This is the constructor...");
    }
```

Main类

```java
        ApplicationContext context=new ClassPathXmlApplicationContext("beans-scope.xml");
        Car car= (Car) context.getBean("car");
        Car car1= (Car) context.getBean("car");

        System.out.println(car==car1);
```

运行结果：

**This is the constructor...**
**true**



注释后的Main类

```java
        ApplicationContext context=new ClassPathXmlApplicationContext("beans-scope.xml");
//        Car car= (Car) context.getBean("car");
//        Car car1= (Car) context.getBean("car");
//
//        System.out.println(car==car1);
```

运行结果：

**This is the constructor...**

### prototype

prototype：容器创建时不创建bean的实例，而是在每次请求时创建一个bean实例并返回

Car类

```java
    public Car(){
        System.out.println("This is the constructor...");
    }
```

Main类

```java
        ApplicationContext context=new ClassPathXmlApplicationContext("beans-scope.xml");
        Car car= (Car) context.getBean("car");
        Car car1= (Car) context.getBean("car");

        System.out.println(car==car1);
```

运行结果：

**This is the constructor...**
**This is the constructor...**
**false**



注释后的Main类

```java
        ApplicationContext context=new ClassPathXmlApplicationContext("beans-scope.xml");
//        Car car= (Car) context.getBean("car");
//        Car car1= (Car) context.getBean("car");
//
//        System.out.println(car==car1);
```

运行结果：

无结果

