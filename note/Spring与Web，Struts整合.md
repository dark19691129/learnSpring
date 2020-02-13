# Spring与Web，Struts整合

## Spring和Web应用整合的思路

（一）Spring如何在Web应用中使用？

### 需要额外加入的jar包

spring-web-5.2.3.RELEASE.jar

spring-webmvc-5.2.3.RELEASE.jar

Spring的配置文件，没有什么不同

### 如何创建IOC容器

1. 非Web应用在main方法中直接创建

2. 应该在Web应用被服务器加载时就创建IOC容器（在被加载时，可以使用一个监听器，此时监听器的初始化方法里面创建IOC容器最为合适：在**ServletContextListener # contextInitialized（ServletContextEvent sce）**创建IOC容器）

3. 在Web应用的其他组件中如何来访问IOC容器呢？

   在**ServletContextListener # contextInitialized（ServletContextEvent sce）**创建容器后，可以把其放在ServletContext（即application域）的一个属性中

4. 实际上，Spring的配置文件的名字和位置应该也是可配置的！将其配置到当前的Web应用的初始化参数中较为合适

### 在web环境下使用Spring

需要加入额外的jar包

spring-web-5.2.3.RELEASE.jar

spring-webmvc-5.2.3.RELEASE.jar

Spring的配置文件和非Web环境没有什么不同

需要在web.xml文件中加入如下配置：

```java
 <!-- 配置Spring配置文件的名称和位置 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext.xml</param-value>
	</context-param>

	<!-- 启动IOC容器的ServletContextListener-->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
```



## （二）Spring如何整合Struts2？

### 整合目标？	

使IOC容器来管理Struts2的Action

### 如何进行整合？

#### 正常加入Struts2

在Spring的IOC容器中配置Struts2的Action

（注意：在IOC容器中配置Struts2的Action时，需要配置scope属性，其值必须为Prototype，不是默认的singleTop）

```java
<bean id="PersonAction"
    class="struts2.action.PersonAction"
    scope="prototype">
    <property name="personService" ref="personService">
</bean>      
```

配置Struts2的配置文件Laction节点的class属性需要指向IOC容器中该bean的id

```java
<action name="person-save" class="PersonAction">
    <result>/success.jsp</result>
</action>  
```

####  加入struts2-struts1-plugin-2.3.15.1.jar

### 整合原理

通过添加struts2-struts1-plugin-2.3.15.1.jar以后，Struts2会先从IOC容器中获取Action的实例。