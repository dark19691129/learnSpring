## Spring通过注解配置Bean

配置Bean：

基于xml文件的形式（之前学的）

基于注解的方式（

特定组件：@Component，@Respority，@Service，@Controller）

Main类

```java
        TestObject to= (TestObject) context.getBean("testObject");
        System.out.println(to.toString());

        UserController userController= (UserController) context.getBean("userController");
        System.out.println(userController.toString());

        UserService userService= (UserService) context.getBean("userService");
        System.out.println(userService.toString());

        UserReposity userReposity= (UserReposity) context.getBean("userReposity");
        System.out.println(userReposity.toString());
```

### 1

```java
        <!--指定Spring IOC容器扫描的包-->
        <!--可以通过resource-pattern指定扫描的包-->
    	<context:component-scan base-package="annotation"
            ></context:component-scan>
```

结果：

```java
annotation.TestObject@5e25a92e
annotation.service.UserController@4df828d7
annotation.service.UserService@b59d31
annotation.reposity.UserReposityImpl@62fdb4a6
```



### 2

```java
    <!--context:exclude-filter子节点指定排除哪些指定表达式的组件-->
	<context:component-scan base-package="annotation">
        <!--排除-->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
```

结果：

```java
annotation.TestObject@33cb5951
annotation.service.UserController@365c30cc
annotation.service.UserService@701fc37a
Exception in thread "main" org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named 'userReposity' available
```



### 3

```java
    <!--context:include-filter子节点指定包含哪些表达式的组件，该子节点需要user-default-filters配合使用，将其置为false-->
    <context:component-scan base-package="annotation"
        use-default-filters="false">
        <!--包括-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
```

结果：

```java
Exception in thread "main" org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named 'testObject' available
```

修改Main类后

```java
//        TestObject to= (TestObject) context.getBean("testObject");
//        System.out.println(to.toString());
//
//        UserController userController= (UserController) context.getBean("userController");
//        System.out.println(userController.toString());
//
//        UserService userService= (UserService) context.getBean("userService");
//        System.out.println(userService.toString());

        UserReposity userReposity= (UserReposity) context.getBean("userReposity");
        System.out.println(userReposity.toString());

```

再次运行结果：

```java
annotation.reposity.UserReposityImpl@4e41089d
```

### 4

```java
    <context:component-scan base-package="annotation"
                            use-default-filters="true">
        <!--包括-->
        <context:exclude-filter type="assignable" expression="annotation.reposity.UserReposity"/>
    </context:component-scan>
        
```

结果：

```java
annotation.TestObject@701fc37a
annotation.service.UserController@4148db48
annotation.service.UserService@282003e1
Exception in thread "main" org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named 'userReposity' available
```





## Bean之间的关联关系（调用别的Bean）

利用注解建立bean和bean之间的引用关系

UserController

```java
@Controller
public class UserController {
    private  UserService userService;

    public void execute(){
        System.out.println("UserController execute...");
        userService.add();
    }

}
```

UserService

```java
@Service
public class UserService {
    private UserReposity userReposity;

    public void add(){
        System.out.println("UserService add...");
        userReposity.save();
    }

}
```

UserReposity

```java
@Repository("userReposity")
public class UserReposityImpl implements UserReposity {

    @Override
    public void save() {
        System.out.println("UserReposity Save...");
    }
}

```

Spring配置文件

```java
        <!--指定Spring IOC容器扫描的包-->
        <!--可以通过resource-pattern指定扫描的包-->
    <context:component-scan base-package="annotation"
            ></context:component-scan>
```

Main类

```java

public class Main {
    public static void main(String[] args) {
        ApplicationContext context=new ClassPathXmlApplicationContext("beans-annotation.xml");
        UserController userController= (UserController) context.getBean("userController");
        System.out.println(userController.toString());
        userController.execute();
    }
}

```

运行结果：

```java
annotation.service.UserController@5e25a92e
UserController execute...
Exception in thread "main" java.lang.NullPointerException
	at annotation.service.UserController.execute(UserController.java:11)
	at annotation.Main.main(Main.java:18)
```



### 组件装配

<context:component-scan>元素还会自动注册AutowireAnnotationBeanPostProcessor实例，该实例可以自动装配具有@Autowire和@Resource和@Inject注解的属性，@Resource和@Inject功能和@Autowire差不多，推荐使用@Autowire

添上注解后：

UserController

```java
@Controller
public class UserController {
    @Autowired
    private  UserService userService;

    public void execute(){
        System.out.println("UserController execute...");
        userService.add();
    }

}

```

UserService

```java
@Service
public class UserService {
    @Autowired
    private UserReposity userReposity;

    public void add(){
        System.out.println("UserService add...");
        userReposity.save();
    }

}
```

运行结果

```java
annotation.service.UserController@3d3fcdb0
UserController execute...
UserService add...
UserReposity Save...
```

**默认情况下，所有使用@AutoWire注解的属性都需要被设置，当Spring找不到匹配的Bean装配的属性是，便会抛出异常**

即：

UserReposity

```java
@Repository("userReposity")
public class UserReposityImpl implements UserReposity {

    @Autowired
    private TestObject testObject;

    @Override
    public void save() {
        System.out.println("UserReposity Save...");
        System.out.println(testObject);
    }
}
```

TestObject（没有加上@Conponent）

```java

public class TestObject {
}
```

此时在运行会抛出异常，因为TestObject并没有被设置注解属性

若一个属性不允许被设置，则可以设置@AutoWire注解的required属性为false，即：

UserReposity

```java
@Repository("userReposity")
public class UserReposityImpl implements UserReposity {

    @Autowired(required = false)
    private TestObject testObject;

    @Override
    public void save() {
        System.out.println("UserReposity Save...");
        System.out.println(testObject);
    }
}
```

运行结果：

```java
annotation.service.UserController@3d3fcdb0
UserController execute...
UserService add...
UserReposity Save...
null
```



