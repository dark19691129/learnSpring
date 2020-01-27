## Spring使用外部属性文件

Spring配置文件：

```JAVA
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <property name="user" value="root"></property>
            <property name="password" value="123456"></property>
            <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
            <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/vote2"></property>
   </bean>
```

若是需要对这些值进行修改，在一些大型项目中会很麻烦

所以我们可以将这些值放到一个外部属性文件中

db.properties文件中

```java
user=root
password=123456
driverClass=com.mysql.jdbc.Driver
jdbcUrl=jdbc:mysql:///test
```

且Spring提供了一个BeanFactory后置处理器，这个处理器允许用户将Bean的配置的部分内容外移到部分属性文件中，然后在Bean配置文件中使用形式为${var}的变量。PropertyPlaceholderConfigurer从属性文件中加载属性，并使用这些属性来替换这些变量

```java
 		<!--导入属性文件-->
        <context:property-placeholder location="db.properties"/>


        <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <!--使用属性文件-->
            <property name="user" value="${user}"></property>
            <property name="password" value="${password}"></property>
            <property name="driverClass" value="${driverClass}"></property>
            <property name="jdbcUrl" value="${jdbcUrl}"></property>
        </bean>
```



遇到的问题

错误提示：

```java
一月 26, 2020 1:59:15 下午 org.springframework.context.support.ClassPathXmlApplicationContext refresh
警告: Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataSource' defined in class path resource [beans-properties.xml]: Instantiation of bean failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.mchange.v2.c3p0.ComboPooledDataSource]: No default constructor found; nested exception is java.lang.NoClassDefFoundError: com/mchange/v2/ser/Indirector
Exception in thread "main" org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataSource' defined in class path resource [beans-properties.xml]: Instantiation of bean failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.mchange.v2.c3p0.ComboPooledDataSource]: No default constructor found; nested exception is java.lang.NoClassDefFoundError: com/mchange/v2/ser/Indirector
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateBean(AbstractAutowireCapableBeanFactory.java:1320)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1214)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:557)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:517)
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:323)
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:222)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:321)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:879)
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:878)
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:550)
	at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:144)
	at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:85)
	at Properties.Main.main(Main.java:16)
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.mchange.v2.c3p0.ComboPooledDataSource]: No default constructor found; nested exception is java.lang.NoClassDefFoundError: com/mchange/v2/ser/Indirector
	at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:83)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateBean(AbstractAutowireCapableBeanFactory.java:1312)
	... 13 more
Caused by: java.lang.NoClassDefFoundError: com/mchange/v2/ser/Indirector
	at java.lang.Class.getDeclaredConstructors0(Native Method)
	at java.lang.Class.privateGetDeclaredConstructors(Class.java:2671)
	at java.lang.Class.getConstructor0(Class.java:3075)
	at java.lang.Class.getDeclaredConstructor(Class.java:2178)
	at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:78)
	... 14 more
Caused by: java.lang.ClassNotFoundException: com.mchange.v2.ser.Indirector
	at java.net.URLClassLoader.findClass(URLClassLoader.java:382)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:349)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 19 more
```

结果：少导入了mchange-commons-java-0.2.12.jar包

导入后并重新运行，结果正常





