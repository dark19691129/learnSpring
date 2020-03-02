# 一，SpringBoot

## 1，环境配置

jdk1.8：Spring Boot 1.7 及以上；java version “1.8.0--112”

maven3.x：maven3.3以上版本；Apache Maven 3.3.9

IntelliJ IDEA2017：

Spring Boot 1.5.9.RELEASE：1.5.9

统一环境



## 2，maven设置

```java
<profile>
		<id>jdk-1.8</id>
		<activation>
			<activeByDefault>true</activeByDefault>
			<jdk>1.8</jdk>
		</activation>
		<properties>
			<maven.compiler.source>1.8</maven.compiler.source>
			<maven.compiler.target>1.8</maven.compiler.target>
			<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
		</properties>
	</profile>
```





## 3，Spring Boot HelloWorld

一个功能

浏览器发送一个hello请求，服务器接收并处理，相应HelloWorld字符串



### 3.1，创建一个maven工程

### 3.2，导入springboot相关的依赖

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
    </dependencies>
```

#### 3.1.1，在官网下复制相关代码：

![2222](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\2222.PNG)

#### 3.1.2，选择对应的版本号

![1111](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\1111.PNG)

#### 3.1.3，下滑到这里进行复制即可

![3333](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\3333.PNG)

3：编写一个主程序，目的是启动SpringBoot

```java
/**
 * @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {
        //Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }

}

```

4：编写相关的业务逻辑（Controller,Service）

```java
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "hello world";
    }
}

```

5：运行主程序测试

6：简化部署（导入SpringBoot的maven插件）

```xml
    <!--这个插件可以将应用打包成一个可执行的jar包-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

将这个应用打成jar包，然后在当前目录的命令行中使用**java -jar 打包后的包名**进行使用

即使没有tomcat也可以运行

## 4，HelloWorld探究

### 4.1，pom文件

父项目

```xml
<!--导入spring-boot-starter的父项目-->
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
</parent>

<!--他的父项目是-->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.4.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
  </parent>
<!--spring-boot-dependencies才是真正管理Spring Boot应用里面的所有依赖版本-->
<!--他默认是Spring Boot的版本仲裁中心-->
<!--以后我们导入的依赖默认时不需要写版本的（spring-boot-dependencies里面管理的依赖自然需要写版本）-->
```

启动器（导入的依赖）

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
</dependencies>
```

**spring-boot-starter**-*web*

​	spring-boot-starter：Spring-boot场景启动器；帮我们导入了web模块正常运行所依赖的组件



Spring Boot将所有的功能场景都抽取出来，做成一个个的starter（启动器），只需要在项目里面引入这些starter，相关场景的所有依赖都会导入出来，要用什么功能就导入什么场景的启动器



### 4.2，主程序类，主入口类

```java
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {
        //Spring应用启动起来
 		//SpringApplication.run()里面传入的类应该是用SpringBootApplication注解的类
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }

}
```

**@SpringBootApplication**：Spring Boot应用标注在某个类上说明这个类时Spring Boot的主配置类，Spring Boot就应该运行这个类的main方法来启动SpringBoot应用

**@SpringBootApplication**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

#### 1，@SpringBootConfiguration：SpringBoot的配置类

​	标注在某个类上，表示这是一个Spring Boot的配置类

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
```

​	@Configuration：配置类上来标注这个注解

​		配置类----配置文件：配置类也是容器中的一个组件；@Component

​		@Configuration

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
```



#### 2，@EnableAutoConfiguration：开启自动配置功能

以前我们需要配置的东西，SpringBoot帮我们自动配置；@EnableAutoConfiguration告诉Spring Boot开启自动配置功能；这样自动配置才能生效（自动配置扫描包，注册什么的......）

@EnableAutoConfiguration：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

​	**1：@AutoConfigurationPackage：自动配置包**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {

}
```

​	@Import(AutoConfigurationPackages.Registrar.class)

​	Spring的底层注解@Import，给容器中导入一个组件；导入的组件由AutoConfigurationPackages.Registrar.class

​	Registrar类：

```java
	/**
	 * {@link ImportBeanDefinitionRegistrar} to store the base package from the importing
	 * configuration.
	 */
	static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {

		@Override
		public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
			register(registry, new PackageImport(metadata).getPackageName());
		}

		@Override
		public Set<Object> determineImports(AnnotationMetadata metadata) {
			return Collections.singleton(new PackageImport(metadata));
		}

	}
```

​		new PackageImport(metadata).getPackageName()：获取包名

​		AutoConfigurationPackages.Registrar.class：将主配置类（@SpringBootApplication所标注的类）所在的包下面所有的组件都扫描进Spring容器中



​	**2：@Import(AutoConfigurationImportSelector.class)**

​		给容器中导入组件:：指出Spring容器要导入哪些组件，这些组件以String[] 的形式返回组件全类名，这些组件就会被添加到容器中

并给容器中导入非常多的配置类（xxxAutoConfiguration）：就是给容器中导入场景所需要的所有组件，并配置好这些组件 

```java
@Override
	public String[] selectImports(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return NO_IMPORTS;
		}
		AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
		AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(autoConfigurationMetadata,annotationMetadata);
		return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
	}

```

有了自动配置类，免去了手动编写配置注入功能组件等的工作

SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,ClassLoader());

**SpringBoot在启动时从类路径下的"META-INF/spring.factories"中获取EnableAutoConfiguration指定的值，将这些之作为自动配置类导入到容器中，自动配置类就生效了，帮我们进行自动配置工作。**以前需要我们自己进行配置的东西，自动配置类都帮我们配置好了



J2EE的整体整合解决方案都在spring-boot-autoconfigure-2.2.4.RELEASE.jar



## 5，使用Spring initializer快速创建Spring Boot项目

IDEA支持Spring 的项目向导快速创建一个Spring Boot项目；

选择我们需要的模块；想到会联网创建SpringBoot项目

默认生成的Spring Boot项目

 主程序已经生成好了，我们只需要我我们自己的逻辑

resources文件夹中目录结构

​	static：保存所有的静态资源；js css images；

​	template：保存所有的模板页面；（Spring Boot默认jar包使用嵌入式Tomcat，默认不支持jsp页面）；但可以使用模板引擎（freemarker，thymeleaf）；

​	application.properties：Spring Boot应用的配置文件；可以修改一些默认设置

controller层的HelloController类

```java
//也可以把@ResponseBody写在这里，功能是这个类返回的所有数据直接写给浏览器（如果是对象，转换成json数据）
//@ResponseBody
//@Controller
//也可以用@RestController来替换@ResponseBody和@Controller
@RestController
public class HelloController {


    @RequestMapping("/hello")
    public String hello(){
        return "Hello World quick";
    }

}
```



​	

# 二，配置文件

## 配置文件

Spring Boot使用一个全局的配置文件，配置文件名称是固定的

application.properties

application.yml



配置文件的作用：修改SpringBoot自动配置的默认值；SpringBoot在底层都给我们自动配置好了；但如果我们不满意，可以自己修改



YAML（YAML Ain‘t Markup Language）

​	YAML A Markup Language：是一个标记语言

​	YAML isn’t Markup Language：不是一个标记语言

标记语言：

​	以前的配置文件：大多都使用的是：xxx.xml文件

​	YAML：**以数据为中心**，比json，xml等更适合做配置文件

​	YAML：配置示例

```yaml
server:
  port:8081
```

​	xml：

```xml
<server>
	<port>8081</port>
</server>
```

xml文件的配置，大部分时间都用在标签上，而YAML很容易看得出，以数据为中心



## YAML语法

### 基本语法

k:(空格)v：表示一对键值对（空格必须有）：

以空格的缩进来控制层级关系；**只要是左对齐的一列数据，就算是同一个层级**

```yaml
server:
    port: 8080
    path: /hello
```

空格缩进与python差不多

属性和值也是大小写敏感

YAML不允许使用tab键

## 值的写法

#### 字面量：普通的值（数字，字符串。布尔类型）

​	k: v	：字面量直接来写

​		字符串默认不用加上单引号或者双引号

​		""：双引号：不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思（双引号：弱引用，会对特殊字符进行解析）

​				name：“zhangsan \n lisi” 输出：zhangsan 换行 lisi

​		''：单引号：会转义特殊字符，特殊字符最终只是一个普通字符串数据（单引号：强引用，不会对特殊字符进行解析）

​				name：’zhangsan \n lisi‘ 输出：zhangsan \n lisi





#### 对象，Map（属性和值）（键值对）

​	k: v：在下一行来写对象的属性和值的关系；

​		对象还是k: v的方式

```yaml
friends:
    lastName: zhangsan
    age: 20
```

行内写法：

```yaml
friends: {lastName: zhangsan,age: 20}
```

#### 数组

用-值表示数组中的一个元素

```yaml
pets:
  - cat
  - dog
  - pig
```

行内写法

```yaml
pets: [cat,dog,pig]
```



## 测试YAML配置文件

测试YAML配置文件的Person类：

```java
/**
 * 测试YAML配置文件
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties:告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
 * prefix = "person"配置文件中哪个的下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能使用容器的@ConfigurationProperties功能，所以加上@Component
 *
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
 	......   
}
```

测试YAML配置文件的Dog类

```java
public class Dog {

    private String name;
    private Integer age;

   	再加上一系列get，set和toString方法

}
```

yaml配置文件：

```yaml
person:
  lastName: zhangsan
  age: 18
  boss: false
  birth: 2017/12/12
  maps: {k1: v1,k2: v2}
  lists:
    - lisi
    - zhouliu
  dog:
    name: 小狗
    age: 2
```

IDEA会进行警告：因为使用了@ConfigurationProperties(prefix = "person")而没有导入相应的依赖，只需要在pom.xml加入：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

## 测试Properties配置文件

```properties
#由于IDEA使用的是utf-8，而properties使用的是ASCII码，所以需要在IDEA进行设置
#设置步骤：file->setting->file encoding->下方的properties文件选择编码格式，并把右边的勾勾上
#配置person的值
person.last-name=zhangsan
person.age=18
person.birth=2017/12/12
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=dog
person.dog.age=2
```

## @Value获取值和@ConfigurationProperties获取值比较

|                            | @Configuration             | @Value       |
| -------------------------- | -------------------------- | ------------ |
| 功能                       | 批量获取注入配置文件中的值 | 一个一个指定 |
| 松散绑定                   | 支持                       | 不支持       |
| SpEL                       | 不支持                     | 支持         |
| JSR303数据校验             | 支持                       | 不支持       |
| 复杂类型封装（例如键值对） | 支持                       | 不支持       |

@Value

```java
@Component
//@ConfigurationProperties(prefix = "person")
public class Person {

    @Value("person.last-name")
    private String lastName;
    @Value("person.age")
    private Integer age;
    @Value("person.boss")
    private Boolean boss;
    @Value("person.birth")
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
```

JSR303数据校验

```java
@Validated
public class Person {

    //意思是lastName必须是邮箱格式
    //@Value("person.last-name")
    @Email
    private String lastName;
```

配置文件无论是yml还是properties都能够获取值

什么时候使用@ConfigurationProperties，什么时候用@Value？

- 如果只是在某个业务中简单的获取配置文件中的某一项值，那么使用@Value
- 如果是专门编写了一个javaBean来和配置文件进行映射，我们就直接使用

## @PropertiesSource&@ImportResource

 @ConfigurationProperties是默认从全局配置文件中获取值；但在实际开发中，如果所有配置都写在全局配置文件中，会是这个文件变得冗余，所以我们可以使用@PropertySource，从指定的配置文件中获取值

指定的配置文件：person.properties

```properties
person.last-name=李四
person.age=18
person.birth=2017/12/12
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=dog
person.dog.age=2
```

Person类

```java
/**
 * 测试YAML配置文件
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties:告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
 * prefix = "person"配置文件中哪个的下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能使用容器的@ConfigurationProperties功能，所以加上@Component
 *
 * @ConfigurationProperties(prefix = "person")默认从全局配置文件中读取值
 */
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    //@Value("person.last-name")
    private String lastName;
    //@Value("person.age")
    private Integer age;
    //@Value("person.boss")
    private Boolean boss;
    //@Value("person.birth")
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
```

运行结果：

```java
Person{lastName='李四', age=18, boss=false, birth=Tue Dec 12 00:00:00 GMT+08:00 2017, maps={k1=v1, k2=14}, lists=[a, b, c], dog=Dog{name='dog', age=2}}
```



@ImportResource：导入Spring配置文件，让配置文件中的内容生效

Spring Boot里面没有Spring的配置文件，我们自己编写的配置文件，也不能自动识别

想让Spring的配置文件生效并加载进来；@ImportResource标注在一个**配置类**上

Spring的配置文件：beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="helloService" class="com.cb414.services.HelloService"></bean>

</beans>
```

Spring Boot的配置类

```java
@ImportResource(locations = {"classpath:beans.xml"})
@SpringBootApplication
public class SpringBoot02ConfigApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBoot02ConfigApplication.class, args);
    }
}
```

在测试类中测试此时的ioc容器中是否含有id为**helloService**的bean对象

```java
    @Autowired
    ApplicationContext ioc;


    @Test
    public void TestHelloService(){
        boolean b=ioc.containsBean("helloService");
        System.out.println(b);
    }
```



SpringBoot推荐给容器中添加组件的方式：通过全注解的方式

1：配置类=====Spring配置文件

2：使用@Bean给容器中添加组件

这是Spring Boot所推荐的添加方式：

```java
/**
 * @Configuration：指明当前类是一个配置类，就是来替代之前的Spring配置文件的；
 *
 * 在配置文件中用<bean></bean>标签添加组件
 *
 */
@Configuration
public class MyAppConfig {

    @Bean
    public HelloService helloService(){
        System.out.println("配置类@Bean给容器中添加组件了");
        return new HelloService();
    }
}
```



## 配置文件占位符

### 1，随机数

```java
${random.int},${random.long},${random.value}
${random.int(10)},${random.int[1024,65536]}
```

### 2，占位符获取之前已经指定的值，如果没有这个值，会当成字符串处理（可以用: 值  来为这个值指定默认值）

```properties
person.last-name=张三${random.int}
person.age=${random.int}
person.birth=2017/12/12
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=${person.hello:cb414}的dog
person.dog.age=2
```

结果：

```java
Person{lastName='张三1063473383', age=-792007248, boss=false, birth=Tue Dec 12 00:00:00 GMT+08:00 2017, maps={k1=v1, k2=14}, lists=[a, b, c], dog=Dog{name='cb414的dog', age=2}}
```



## Profile

### 1，多Profile文件

我们在主配置文件编写的时候，文件名可以是application-{profile}.properties

配置默认使用application.properties文件的配置

### 2，yml支持多文档块方式

```yaml
server:
  port: 8081
spring:
  profiles:
    active: dev

---
server:
  port: 8082
spring:
  profiles: dev

---
server:
  port: 80
spring:
  profiles: prod
```

### 3，激活指定profile

dev是测试样例中的一个配置模式

1：在配置文件中指定激活：spring.profiles.active=dev（指定激活dev配置文件）

2：命令行：

​	将项目打包，打包后在文件存储路径上使用cmd，然后执行处理命令：

```shell
java -jar 打包后的包名 --spring.profiles.active=dev;
```

​	可以在测试的时候，配置传入参数

3：虚拟机参数

​	-DSpring.profiles.active=dev

## 配置文件加载位置

配置文件自定义位置

springboot启动会扫描以下位置的application.properties或者application.yml文件作为Spring Boot的配置文件

-file:./config	（项目的根目录下的自己创建的config文件夹中的配置文件）

-file:./	（项目的根目录下的配置文件）

-classpath:/config	（类路径下的自己创建的config文件夹中的配置文件）

-classpath:/	（类路径下的配置文件）

**优先级从上往下是从高到低**，高优先级的配置会覆盖**低优先级**的配置；还可以做到互补配置



我们还可以使用spring.config.location来改变默认的配置文件的位置；（假设我们将一个自定义配置文件放在d盘下）

然后将项目打包后，使用命令行方式启动，在启动的同时，将d盘下的配置文件作为参数进行使用，这时候**指定配置文件就能和默认配置文件一起被加载并同时起作用形成互补配置**

**猜测：指定配置文件的优先级高于项目根目录下面的config文件夹的优先级**





## 外部配置加载顺序

**SpringBoot也可以从以下位置加载配置：优先级从高到低，高优先级的配置会覆盖低优先级的配置，若配置不相同，所有的配置会形成互补配置**

- 命令行参数 

  ```shell
  java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8083
  ```

  多个配置项用空格隔开：--配置型=值

- 来自java:com/env的JNDI属性

- java系统属性（System.getProperties()）

- 操作系统环境变量

- RandomPropertiesSource配置的random.*属性值



​		==顺序：由jar包外向jar包内进行寻找==

​		==优先加载带profile的==

- **jar包外部的application-{profile}.properties或application.yml（带spring.profile）配置文件**

- **jar包内部的application-{profile}.properties或application.yml（带spring.profile）配置文件**

  

  **再来加载不带profile的**

- **jar包外部的application.properties或application.yml（不带spring.profile）配置文件**

- **jar包内部的application.properties或application.yml（不带spring.profile）配置文件**

  

- @Configuration注解类上的@PropertySource

- 通过SpringApplication.setDefaultProperties指定的默认属性

所有配置参考来源：https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-external-config



## 自动配置原理

配置文件到底能写什么？怎么写？自动配置原理

配置文件能配置的属性参照：https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/appendix-application-properties.html#common-application-properties



==自动配置原理==

### 1，SpringBoot启动的时候加载主配置类，开启了自动配置功能@EnableAutoConfiguration

### 2，@EnableAutoConfiguration的作用：

​	利用@AutoConfigurationImportSelector给容器中导入一些组件

​	 可以查看selectImports()的内容：

```java
List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
//获取候选的配置
```

### 3，SpringFactoriesLoader.loaderFactoryNames()

扫描所有jar包类路径下：META-INF/spring.factories

把扫描到的这些文件的内容包装成properties对象

从properties中获取到的EnableAutoConfiguration.class类（类名）对应的值，然后把它们添加在容器中。

==将类路径下META-INF/spring.factories里面配置的所有EnableAutoConfiguration的值加入到容器中==

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudServiceConnectorsAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveRestClientAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\
org.springframework.boot.autoconfigure.elasticsearch.rest.RestClientAutoConfiguration,\
org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration,\
org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration,\
org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration,\
org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration,\
org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration,\
org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketMessagingAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketRequesterAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketServerAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketStrategiesAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\
org.springframework.boot.autoconfigure.security.rsocket.RSocketSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.saml2.Saml2RelyingPartyAutoConfiguration,\
org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration,\
org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration,\
org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration,\
org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.function.client.ClientHttpConnectorAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration,\
org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration,\
org.springframework.boot.autoconfigure.webservices.client.WebServiceTemplateAutoConfiguration
```



### 4，每一个这样的xxxAutoConfiguration类都是容器中的一个组件，都加入到容器中；用他们来做自动配置

每一个自动配置类进行自动配置功能



以HttpEncodingAutoConfiguration为例解释自动配置原理

```java
@Configuration(proxyBeanMethods = false)
//表示这是一个配置类，跟以前编写的配置文件一样，也可以给容器中添加组件

@EnableConfigurationProperties(HttpProperties.class)
//启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpProperties绑定起来；并把HttpProperties加入到IOC容器当中

@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
//Spring底层的Conditional注解，根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效

@ConditionalOnClass(CharacterEncodingFilter.class)
//判断当前项目是否有这个类：CharacterEncodingFilter。CharacterEncodingFilter是SpringMVC中用来解决字符乱码的过滤器

@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)
//判断配置文件中是否存在某个配置：spring.http.encoding.enabled;如果不存在，判断也是成立的，因为matchIfMissing = true；
//即：就算配置文件中不配置spring.http.encoding.enabled;也是生效的

public class HttpEncodingAutoConfiguration {
	//他已经SpringBoot的配置文件映射了
    private final HttpProperties.Encoding properties;
    
    //只有一个有参构造的情况下，参数的值就会从容器中拿
    	public HttpEncodingAutoConfiguration(HttpProperties properties) {
		this.properties = properties.getEncoding();
	}
    
    @Bean//给容器中添加一个组件，这个组件的某些值需要从properties中获取
	@ConditionalOnMissingBean//判断容器中没有这个组件，如果没有这个组件才给你加这个组件
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
		return filter;
	}
}
```

根据当前不同条件判断，决定这个配置类是否生效

一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的









### 5，所有配置文件中能配置的属性都是在xxxProperties类中封装着；配置文件能配置什么就可以参照某一个功能对应的这个属性类

HttpProperties类：

```java
@ConfigurationProperties(prefix = "spring.http")//从配置文件中获取指定的值和的属性及逆行绑定 
public class HttpProperties {
```





==精髓：==

1. SpringBoot启动的时候会加载大量的自动配置类
2. 我们看我们需要的功能有没有SpringBoot默认写好的自动配置类
3. 我们再来看这个自动配置类中到底配置了哪些组件（只要我们要用的组件有，我们就不需要再来配置的）
4. 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性，我们就可以在配置文件中指定这些属性的值



xxxAutoConfiguration：自动配置类；

给容器中添加组件

xxxproperties：封装配置文件中的相关属性





## 细节

### @Conditional派生注解（Spring注解版原生的@Conditional注解）

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置类里面的所有内容才生效



自动配置类只有在一定条件下才能生效

可以通过在properties文件中启用 debug=true属性；来让控制台打印自动配置报告，这样我们就能很方便的知道哪些配置类生效了

```properties
debug=true
```

控制台：

```shell
============================
CONDITIONS EVALUATION REPORT
============================


Positive matches:	#自动配置启用的
-----------------

   AopAutoConfiguration matched:
      - @ConditionalOnProperty (spring.aop.auto=true) matched (OnPropertyCondition)

   AopAutoConfiguration.ClassProxyingConfiguration matched:
      - @ConditionalOnMissingClass did not find unwanted class 'org.aspectj.weaver.Advice' (OnClassCondition)
      - @ConditionalOnProperty (spring.aop.proxy-target-class=true) matched (OnPropertyCondition)

   DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet' (OnClassCondition)
      - found 'session' scope (OnWebApplicationCondition)
     
      ......
      
Negative matches:	#自动配置没有启用的自动配置类
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'javax.jms.ConnectionFactory' (OnClassCondition)

   AopAutoConfiguration.AspectJAutoProxyingConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'org.aspectj.weaver.Advice' (OnClassCondition)
      
```





# 三，SpringBoot与日志

## 1，日志框架

市面上的日志框架：

JUL，JCL，Jboss-logging，logback。log4j，log4j2，slf4j等等

| 日志门面（日志的抽象层）                                     | 日志实现                                     |
| ------------------------------------------------------------ | -------------------------------------------- |
| JCL（Jakarta Commons Logging） SLF4j（Simple Logging Facade for java） jboss-logging | Log4j JUL(java. util.logging) log4j2 logBack |

左边选一个门面（抽象层），右边选一个实现

（扩展：SLF4j和log4j和logback都是同一人开发，但是logBack是Log4j的升级版）

日志门面：SLF4j

日志实现：logBack



SpringBoot：底层是Spring框架，Spring框架默认时使用CL

​	==SpringBoot选用SLF4j和logBack==





## 2，SLF4j使用

### 1，如何在系统中使用SLF4j

以后开发的时候，日志记录方法的调用，不应该直接调用日志的实现类，而实调用日志抽象层里面的方法；

给系统里面导入slf4j的jar和logBack的实现jar

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

图示：

![concrete-bindings](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\concrete-bindings.png)

每一个日志的实现框架都有自己的配置文件，使用slf4j以后，**配置文件还是做成日志实现框架的配置文件**



### 2，遗留问题

比如一个a系统，开发时a的日志框架为slf4j+logBack，而a系统里面使用了Spring框架，Hibernate，Mybatis，他们都有各自的日志框架

a（slf4j+logBack）：Spring（commons）+Hibernate（Jboss-logging）+Mybatis+xxxx

统一日志记录：即使是别的框架，和我一起统一使用

![legacy](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\legacy.png)

如何让系统中所有的日志都统一到slf4j：

1，将系统中其他的日志框架先排除出去；（此时若运行会出现因为找不到这些已经被排除出去的框架包而报错）

2，用中间包来替换原有的日志框架；

3，导入slf4j其他的实现



### 3，SpringBoot日志关系

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <version>2.2.4.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```





SpringBoot使用它来做日志功能

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
      <version>2.2.4.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```

![日志依赖图标1](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\日志依赖图标1.png)



总结：

1：SpringBoot底层也是使用slf4j+logback的方式进行日志记录

2：SpringBoot也把其他的日志都替换成了slf4j

3：中间替换包

```java
public class SLF4JLoggerContextFactory implements LoggerContextFactory {
    private static final StatusLogger LOGGER = StatusLogger.getLogger();
    private static LoggerContext context = new SLF4JLoggerContext();
```

4：如果我们一定要引入其他框架，一定要把这个框架的默认日志框架移除掉

==SpringBoot能自动适配所有的日志，而且底层使用的是slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉==





### 4，日志地使用

#### 1，默认配置

```java
    @Test
    void contextLoads() {

        //日志的级别
        //由低到高：trace<debug<info<warn<error
        //可以调整输出的日志级别，日志就只会在这个级别以后的高级别生效
        logger.trace("这是trace日志");
        logger.debug("这是debug日志");
        //SpringBoot默认是使用info级别，没有指定级别的就用SpringBoot默认规定的级别，root级别（info）
        logger.info("这是info日志'");
        logger.warn("这是warn日志");
        logger.error("这是error日志");
    }
```

结果：

```shell
2020-02-17 15:17:23.444  INFO 3588 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是info日志'
2020-02-17 15:17:23.444  WARN 3588 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是warn日志
2020-02-17 15:17:23.460 ERROR 3588 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是error日志
```

==这说明SpringBoot默认是从info级别后开始打印日志的==



可以在properties文件中进行修改：

```properties
logging.level.com.cb414.springboot03=trace
#com.cb414.springboot03是包名
```

修改后的结果：

```shell
2020-02-17 15:20:47.557 TRACE 1944 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是trace日志
2020-02-17 15:20:47.557 DEBUG 1944 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是debug日志
2020-02-17 15:20:47.557  INFO 1944 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是info日志'
2020-02-17 15:20:47.557  WARN 1944 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是warn日志
2020-02-17 15:20:47.557 ERROR 1944 --- [           main] .c.s.SpringBoot03LoggingApplicationTests : 这是error日志
```

可以对日志输出进行配置

```properties
logging.level.com.cb414.springboot03=trace

#当前项目下生成springboot.log日志
#logging.file.name=springboot1.log
#可以指定完整的路径
#logging.file.name=D:/springboot1.log
#在当前的磁盘的根路径下创建使用spring文件夹和里面的log文件夹，文件夹里面的日志文件，其默认名为spring,log
logging.file.path=/spring/log


#在控制台输出的日志的格式
logging.pattern.console=%d{yy-MM-dd HH:mm:ss.SSS}[%thread] %-5level %logger{50} - %msg%n
#指定文件中输出的日志的格式
logging.pattern.file=%d{yy-MM-dd} === [%thread] === %-5level %logger{50} - %msg%n
```

格式示例

```properties
#	日志输出格式
#	%d表示日期时间
#	%thread表示线程名
#	%-5level级别从左显示5个字符宽度
#	%logger{50}表示logger名字最长50个字符，否则按照句点分割，
#	%msg日志消息
#	%n是换行符

%d{yy-MM-dd HH:mm:ss.SSS}[%thread] %-5level %logger{50} - %msg%n
```



#### 2，指定配置

给类路径下放上每个日志框架自己的配置文件即可，SpringBoot就不适用默认配置了

| Logging System          | Customization                                                |
| :---------------------- | :----------------------------------------------------------- |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |



自定义日志配置文件：命名问题

logback.xml：直接被日志框架识别了

logback-spring.xml：==日志框架就不直接加载日志的配置项，而由SpringBoot框架进行解析日志配置，这样做的好处是，由SpringBoot进行解析的话，可以在日志配置里面使用SpringBoot的高级Profile功能==

```xml
<springProfile name="staging">
	<!--configuration to be enabled when the "staging" profile is active-->
    可以指定某段配置只在某个环境下生效
</springProfile>
```

如果不是由SpringBoot进行解析的话，</springProfile>无法被理解，就会报错

详见：

https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-logging



### 5，切换日志框架

可以根据slf4j的日志适配图，进行相关的切换

slf4j+log4j的方式：可以在日志适配图进行操作



# 四，WEB开发

## 1，使用SpringBoot：

1. 创建SpringBoot应用，选中我们需要的模块
2. SpringBoot已经默认将这些场景配置好了（自动配置原理），只需要在配置文件中指定少量配置就可以运行起来
3. 自己编写业务代码



==了解自动配置原理:==

这个场景SpringBoot帮我们配置了什么?能不能修改?能修改哪些配置?能不能扩展?

```java
xxxxAutoConfiguration:帮我们给容器中自动配置组件
xxxxProperties:配置类来封装配置文件的内容
```



## 2，SpringBoot对静态资源的映射规则

```java
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties {
    //可以设置和静态资源有关的配置
```





```java
		@Override
		public void addResourceHandlers(ResourceHandlerRegistry registry) {
			if (!this.resourceProperties.isAddMappings()) {
				logger.debug("Default resource handling disabled");
				return;
			}
			Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
			CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
			if (!registry.hasMappingForPattern("/webjars/**")) {
				customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
						.addResourceLocations("classpath:/META-INF/resources/webjars/")
						.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
			}
			String staticPathPattern = this.mvcProperties.getStaticPathPattern();
			if (!registry.hasMappingForPattern(staticPathPattern)) {
				customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
						.addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
						.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
			}
		}
	
		
		//配置欢迎页的映射 
		@Bean
		public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
				FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
			WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
					new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
					this.mvcProperties.getStaticPathPattern());
			welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
			return welcomePageHandlerMapping;
		}

```

==1：所有/webjars/**，都去classpath:/META-INF/resources/webjars/找资源；==

![jquery-web-jar](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\jquery-web-jar.PNG)

http://localhost:8080/webjars/jquery/3.3.1/jquery.js

```xml
        <!--引入jquery-webjar -->在访问的时候只需要写webjar下面资源的名称即可
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.3.1</version>
        </dependency>
```





==2："/**"，访问当前项目的任何资源（静态资源的文件夹）==

==在IDEA中main目录中的java和resources两个就是类路径的根目录，所以类路径下的resources目录就相当于在java或者resources目录下再建立一个resources文件夹==

```java
"classpath:/META-INF/resources/",
"classpath:/resources/",
"classpath:/static/", 
"classpath:/public/"
"/**"	当前项目的根路径
//静态资源文件保存的位置
```

localhost:8080/abc ====去静态资源文件夹里面找abc



==3：欢迎页；静态资源文件夹下面的所有index.html页面；被"/**"映射==

​	例如在浏览器输入“localhost:8080/	会去找index.html页面



==4：所有的**/favicon.icon都是在静态资源文件下找；（例如图标资源）==





## 3，模板引擎

==例如一个网页，有一个网页结构文件支持动态赋值（动态表达式），而表达式对应的数值存在另一个文件中，而模板引擎就负责将这些数据和网页结构文件进行组合==

![模板引擎](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\模板引擎.PNG)

模板引擎：JSP,Velocity,Freemarker,Thymele、



SpringBoot推荐使用的模板引擎：Thymeleaf

## 4，引入Thymeleaf

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```



## 5，使用thymeleaf和thymeleaf语法

```java
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {

	private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;

	public static final String DEFAULT_PREFIX = "classpath:/templates/";

	public static final String DEFAULT_SUFFIX = ".html";
    
    //视图解析，只要我们把html页面放在classpath:/templates/，thymeleaf就能自动渲染
```



使用：

1，导入thymeleaf的名称空间

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

2，使用thymeleaf的语法

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>成功</h1>
    <!--th:text将div里面的值设置为我们指定的值-->
    <div th:text="${hello}">======这是显示的信息</div>

</body>
</html>
```

3，语法规则

### （1）th:text；改变当前元素里面的文本内容

​	th：任意html属性；来替换原生属性的值

![thymeleaf语法](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\thymeleaf语法.PNG)

### （2）表达式

```properties
Simple expressions:（表达式语法）
    Variable Expressions: ${...}	获取变量值；OGNL
    	（1）获取对象的属性，调用方法
    	（2）使用内置的基本对象
            #ctx : the context object.
            #vars: the context variables.
            #locale : the context locale.
            #request : (only in Web Contexts) the HttpServletRequest object.
            #response : (only in Web Contexts) the HttpServletResponse object.
            #session : (only in Web Contexts) the HttpSession object.
            #servletContext : (only in Web Contexts) the ServletContext object.
         （3）内置的工具对象
            #execInfo : information about the template being processed.
            #messages : methods for obtaining externalized messages inside variables expressions, in the same way as they
            #would be obtained using #{…} syntax.
            #uris : methods for escaping parts of URLs/URIs
            #conversions : methods for executing the configured conversion service (if any).
            #dates : methods for java.util.Date objects: formatting, component extraction, etc.
            #calendars : analogous to #dates , but for java.util.Calendar objects.
            #numbers : methods for formatting numeric objects.
            #strings : methods for String objects: contains, startsWith, prepending/appending, etc.
            #objects : methods for objects in general.
            #bools : methods for boolean evaluation.
            #arrays : methods for arrays.
            #lists : methods for lists.
            #sets : methods for sets.
            #maps : methods for maps.
            #aggregates : methods for creating aggregates on arrays or collections.
            #ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).
         
    Selection Variable Expressions: *{...}	选择表达式，和${}在功能上是一样的；
    	但有一个补充功能，配合	v th:object="${session.user}"使用，详见图A
    Message Expressions: #{...}	获取国际化内容
    Link URL Expressions: @{...}	定义URL链接的,详见图B
    	@{/order/process(execId=${execId},execType='FAST')}
    Fragment Expressions: ~{...}	片段引用表达式
    	<div th:insert="~{commons :: main}">...</div>
Literals:（字面量）
    Text literals: 'one text' , 'Another one!' ,…
    Number literals: 0 , 34 , 3.0 , 12.3 ,…
    Boolean literals: true , false
    Null literal: null
    Literal tokens: one , sometext , main ,…
Text operations:（文本操作）
    String concatenation: +
    Literal substitutions: |The name is ${name}|
Arithmetic operations:（数学运算）
    Binary operators: + , - , * , / , %
    Minus sign (unary operator): -
Boolean operations:（布尔运算）
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
Comparisons and equality:（比较运算）
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
Conditional operators:（条件运算（三元运算符））
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
Special tokens:
    No-Operation: _
```



![图A](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\选择表达式.PNG)

图A



![图Bl表达式](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\LinkUrl表达式.PNG)

图B





## 6，SpringMVC自动配置

### 1，@SpringAutoConfiguration

详见：

https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-developing-web-applications

SpringBoot自动配置好了SpringMVC

以下是SpringBoot对SpringMVC的配置

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

  自动配置了ViewResolver（试图解析器；根据方法的返回值得到视图对象（View），视图对象决定如何渲染（转发还是重定向到前端页面））

  ContentNegotiatingViewResolver：组合所有试图解析器的；

  ==如何定制：我们可以自己给容器中添加一个试图解析器；自动地将其添加进来==

- Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content))).**静态资源文件夹路径webjars**

  

- Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.

  - Converter：转换器：public String hello(User user){...}

    当页面发请求到这个方法，且页面提交过来的数据符合User的话，Converter会将页面提交过来的数据进行类型转换（因为页面提交过来的数据是文本类型。例如18是文本类型，true也是文本类型，所有需要Converter进行文本类型转换）

  - Formatter 格式化器：例如页面传来一个日期2017-08-08，需要将其转换成yy-MM-dd格式的日期类型数据

  ==自己添加的格式器，转换器，我们只需要放在容器中即可==

  



- Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)).

  - HttpMessageConverter：SpringMVC用来转换Http请求和响应的；

  - HttpMessageConverter是从容器中确定的；hu偶去所有的HttpMessageConverter；

    ==自己给容器中添加HttpMessageConverter，只需要将自己的组件注册容器中（@Bean,@Component）==

- Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-message-codes)).
  
  - 定义错误代码生成规则的
- Static `index.html` support.  **静态首页访问**
- Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)). **favicon.ico**
- Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)).
  - 我们可以配置一个ConfigurableWebBindingInitializer来替换默认的（添加到容器中）
    - 初始化WebDataBinder
    - 请求数据====JavaBean：将数据和Bean绑定到一起

==If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.2.3.RELEASE/spring-framework-reference/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.==



If you want to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, or `ExceptionHandlerExceptionResolver`, and still keep the Spring Boot MVC customizations, you can declare a bean of type `WebMvcRegistrations` and use it to provide custom instances of those components.



==If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`, or alternatively add your own `@Configuration`-annotated `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`.==



### 2，扩展SpringMVC

```xml
    <mvc:view-controller path="/hello" view-name="success"/>
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/hello"/>
            <bean>
                
            </bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

==可以编写一个配置类（是WebMvcConfigurer类型的），不能标注@EnableWebMvc==

既保留了所有的自动配置，也能用我们扩展的配置

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //把浏览器发送的请求转到哪个页面
        registry.addViewController("/cb414").setViewName("success");
    }
}

```

原理：

1. WebMvcAutoConfiguration是SpringMVC的自动配置类

2. 在做其他的配置时，会导入@Import(EnableWebMvcConfiguration.class)

   ```java
   	@Configuration(proxyBeanMethods = false)
   	public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration implements ResourceLoaderAware {
           
       
           
       //方法自动装配的话，方法的参数就要从容器中获取
       //即从容器中获取所有的WebMvcConfigurer
       @Autowired(required = false)
   	public void setConfigurers(List<WebMvcConfigurer> configurers) {
   		if (!CollectionUtils.isEmpty(configurers)) {
   			this.configurers.addWebMvcConfigurers(configurers);
               
               //其中的一个参考实现：将所有的WebMvcConfigurer相关配置都来一起调用；
               //	@Override
   			//public void addViewControllers(ViewControllerRegistry registry) {
   			//	for (WebMvcConfigurer delegate : this.delegates) {
   			//	delegate.addViewControllers(registry);
   			//	}
   			//}
   		}
   	}
   ```

3. 容器中所有的WebMvcConfigurer都会一起起作用

4. 我们的配置类也会被一起调用

效果：SpringMVC的自动配置和我们的扩展配置都会被加载进来起作用



### 3，全面接管SpringMVC

加上@EnableWebMvc的话，SpringBoot对SpringMVC的自动配置就不需要了，所有的配置就需要我们自己配置了。

```java
@EnableWebMvc
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //把浏览器发送的请求转到哪个页面
        registry.addViewController("/cb414").setViewName("success");
    }
}

//但不推荐
```

原理：为什么加入@EnableWebMvczhi后，自动配置就会失效了呢？

1），@EnableWebMvc的核心

```java
...
@Import(DelegatingWebMvcConfiguration.class)
public @interface EnableWebMvc {
}
```

 2），

```java
@Configuration(proxyBeanMethods = false)
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
```

3），WebMvcAutoConfiguration

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })


//判断容器中没有这个组件(WebMvcConfigurationSupport)的话，这个自动配置类才会生效
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
		ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
```

==4），因为如第一步所的：@EnableWebMvc会将DelegatingWebMvcConfiguration导入进来，而在第二步中可见DelegatingWebMvcConfiguration是继承了WebMvcConfigurationSupport，所以第三步中的自动配置类不会生效==

5），导入的WebMvcConfigurationSupport知识SpringMVC最基本的功能



## 7，如何修改SpringBoot的默认配置

模式：

1. SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean@Component），如果有，就用用户配置的，如果没有，就自动配置；如果有些组件可以有多个（例如视图解析器）会试图将用户配置的和自动配置的组合起来
2. 在SpringBoot中会有非常多的xxxxConfiguration帮助我们进行扩展配置
3. 在SpringBoot中会有很多的xxxCustomizer帮助我们进行定制配置





## 8，RestfulCrud

### 1，默认访问页面





### 2，国际化

编写国际化配置文件

使用ReourceBundleMessageSource管理国际化配置文件

在页面使用fmt:message取出国际化内容



步骤：

1，编写国际化配置文件，抽取页面需要显示的国际化信息

![国际化配置文件](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\国际化配置文件.PNG)

2，SpringBoot自动配置好了管理国际化资源文件的组件

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnMissingBean(name = AbstractApplicationContext.MESSAGE_SOURCE_BEAN_NAME, search = SearchStrategy.CURRENT)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
@Conditional(ResourceBundleCondition.class)
@EnableConfigurationProperties
public class MessageSourceAutoConfiguration {

	private static final Resource[] NO_RESOURCES = {};

	@Bean
	@ConfigurationProperties(prefix = "spring.messages")
	public MessageSourceProperties messageSourceProperties() {
		return new MessageSourceProperties();
	}

    
	@Bean
	public MessageSource messageSource(MessageSourceProperties properties) {
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		if (StringUtils.hasText(properties.getBasename())) {
            
            //设置国际化资源文件的基础（去掉语言国家代码的名字）
			messageSource.setBasenames(StringUtils			.commaDelimitedListToStringArray(StringUtils.trimAllWhitespace(properties.getBasename())));
		}
		if (properties.getEncoding() != null) {
			messageSource.setDefaultEncoding(properties.getEncoding().name());
		}
		messageSource.setFallbackToSystemLocale(properties.isFallbackToSystemLocale());
		Duration cacheDuration = properties.getCacheDuration();
		if (cacheDuration != null) {
			messageSource.setCacheMillis(cacheDuration.toMillis());
		}
		messageSource.setAlwaysUseMessageFormat(properties.isAlwaysUseMessageFormat());
		messageSource.setUseCodeAsDefaultMessage(properties.isUseCodeAsDefaultMessage());
		return messageSource;
	}
    
    
    ...
        
		@Override
		public ConditionOutcome getMatchOutcome(ConditionContext context, AnnotatedTypeMetadata metadata) {
        //也就是说我们的配置文件可以直接放在类路径下叫message.properties
			String basename = context.getEnvironment().getProperty("spring.messages.basename", "messages");
			ConditionOutcome outcome = cache.get(basename);
			if (outcome == null) {
				outcome = getMatchOutcomeForBasename(context, basename);
				cache.put(basename, outcome);
			}
			return outcome;
		}
}
```



#### 1，去页面获取国际化的值

![fileEncoding](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\fileEncoding.PNG)

login.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
		<meta name="description" content="">
		<meta name="author" content="">
		<title>Signin Template for Bootstrap</title>
		<!-- Bootstrap core CSS -->
		<link href="asserts/css/bootstrap.min.css" th:href="@{/webjars/bootstrap/4.4.1/css/bootstrap.css}" rel="stylesheet">
		<!-- Custom styles for this template -->
		<link href="asserts/css/signin.css" th:href="@{/asserts/css/signin.css}" rel="stylesheet">
	</head>

	<body class="text-center">
		<form class="form-signin" action="dashboard.html">
			<img class="mb-4" th:src="@{/asserts/img/bootstrap-solid.svg}" src="asserts/img/bootstrap-solid.svg" alt="" width="72" height="72">
			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
			<label class="sr-only" th:text="#{login.username}">Username</label>
			<input type="text" class="form-control" placeholder="Username" th:placeholder="#{login.username}" required="" autofocus="">
			<label class="sr-only" th:text="#{login.password}">Password</label>
			<input type="password" class="form-control" placeholder="Password" th:text="#{login.password}" required="">
			<div class="checkbox mb-3">
				<label>
          <input type="checkbox" value="remember-me"> [[#{login.remember}]]
        </label>
			</div>
			<button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
			<p class="mt-5 mb-3 text-muted">© 2017-2018</p>
			<a class="btn btn-sm">中文</a>
			<a class="btn btn-sm">English</a>
		</form>

	</body>

</html>
```

效果：根据浏览器语言设置的信息切换了国际化



原理：

国际化locale（区域信息对象）；localeResolver（获取区域信息对象）

```java
		@Bean
		@ConditionalOnMissingBean
		@ConditionalOnProperty(prefix = "spring.mvc", name = "locale")
		public LocaleResolver localeResolver() {
			if (this.mvcProperties.getLocaleResolver() == WebMvcProperties.LocaleResolver.FIXED) {
				return new FixedLocaleResolver(this.mvcProperties.getLocale());
			}
			AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
			localeResolver.setDefaultLocale(this.mvcProperties.getLocale());
			return localeResolver;
		}

```

==默认的就是根据请求头带来的区域信息获取Locale进行国际化==



AcceptHeaderLocaleResolver是根据请求头的信息来更改

```java
	@Override
	public Locale resolveLocale(HttpServletRequest request) {
		Locale defaultLocale = getDefaultLocale();
		if (defaultLocale != null && request.getHeader("Accept-Language") == null) {
			return defaultLocale;
		}
		Locale requestLocale = request.getLocale();
		List<Locale> supportedLocales = getSupportedLocales();
		if (supportedLocales.isEmpty() || supportedLocales.contains(requestLocale)) {
			return requestLocale;
		}
		Locale supportedLocale = findSupportedLocale(request, supportedLocales);
		if (supportedLocale != null) {
			return supportedLocale;
		}
		return (defaultLocale != null ? defaultLocale : requestLocale);
	}
```

#### 2，点击链接切换国际化

```java
public class MyLocaleResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest request) {

        String l=request.getParameter("l");
        Locale locale=Locale.getDefault();
        if(!StringUtils.isEmpty(l)){
            String[]  split= l.split("_");
            locale=new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {

    }
}


//在容器中加入配置：config包下
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }



//原理
//之所以可以我们设置的localeResolver是因为：
		@Bean
		//意味着如果容器中没有LocalResolver的话，这个配置就会生效，有的话（自己配置的）就不会生效
		//我们在上面已经把自己的localeResolver加到容器中了
		@ConditionalOnMissingBean
		@ConditionalOnProperty(prefix = "spring.mvc", name = "locale")
		public LocaleResolver localeResolver() {
            
```





### 3，登录

开发期间，如果对模板引擎页面进行修改的话，

可以：

先禁用thymeleaf的缓存功能

```properties
#禁用模板引擎的缓存，这样的话，在编译器一修改页面，就能生效
spring.thymeleaf.cache=false
```

再按CTRL+F9使得页面重新编译，使修改实时生效



登录错误消息的显示

```html
<!--判断-->
<!--如果msg里面有值，就代表出错了-->
<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
```



### 4，拦截器进行登录检查

```java
/**
 * 设置拦截器,来进行登录检查
 */

public class LoginHandlerInterceptor implements HandlerInterceptor {

    //目标方法执行之前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object user = request.getSession().getAttribute("loginUser");
        if(user==null){
            //未登录，返回登录页面
            request.setAttribute("msg","没有权限请先登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }else{
            //已登录，放行请求
            return true;

        }
    }
    
    ...
}


//其他类
session.setAttribute("loginUser",username);
```





### 5，CRUD-员工列表

实验要求：

1）RestfulCRUD：CRUD满足Rest风格

URI：/资源名称/资源标识	==HTTP请求方式区分对资源CRUD操作==（通过请求方式区分增删改查）

|      | 普通CRUD（uri来区分操作） | RestfulCRUD                |
| ---- | ------------------------- | -------------------------- |
| 查询 | getEmp                    | emp===以GET方式发送的      |
| 添加 | addEmp?员工信息           | emp===以POST的方式         |
| 修改 | updateEmp?员工信息        | emp/{id}===以PUT的方式     |
| 删除 | deleteEmp?id=1            | emp/{id}====以delete的方式 |



2），实验的请求架构

|                                        | 请求URI  | 请求方式 |
| -------------------------------------- | -------- | -------- |
| 查询所有员工                           | emps     | GET      |
| 查询某个员工                           | emp/{id} | GET      |
| 来到添加页面                           | emp      | GET      |
| 添加员工                               | emp      | POST     |
| 来到修改页面（查出员工，进行信息回显） | emp/{id} | GET      |
| 修改员工                               | emp      | PUT      |
| 删除员工                               | emp/{id} | DELETE   |





## 9，错误处理机制

### （1）SpringBoot的错误处理机制

原理：可以参照 ErrorMvcAutoConfiguration ：错误处理的自动配置

给容器他添加了一下的组件

```java
DefaultErrorAttributes//帮我们在页面共享信息
```

源代码：

```java
    public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
        Map<String, Object> errorAttributes = new LinkedHashMap();
        errorAttributes.put("timestamp", new Date());
        this.addStatus(errorAttributes, requestAttributes);
        this.addErrorDetails(errorAttributes, requestAttributes, includeStackTrace);
        this.addPath(errorAttributes, requestAttributes);
        return errorAttributes;
    }
```



```java
BasicErrorController//处理默认/error请求
```

源代码：

```java
@Controller
@RequestMapping({"${server.error.path:${error.path:/error}}"})
public class BasicErrorController extends AbstractErrorController {
    
    ...
    @RequestMapping(
        produces = {"text/html"}
    )//产生html类型的数据	浏览器发送的请求来到这个方法进行处理
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = this.getStatus(request);
        Map<String, Object> model = Collections.unmodifiableMap(this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        
        //去哪个页面作为错误页面：包含页面地址和页面内容
        ModelAndView modelAndView = this.resolveErrorView(request, response, status, model);
        return modelAndView == null ? new ModelAndView("error", model) : modelAndView;
    }

    @RequestMapping
    @ResponseBody//产生json类型的数据，其他客户端来到这个方法处理
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        Map<String, Object> body = this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.ALL));
        HttpStatus status = this.getStatus(request);
        return new ResponseEntity(body, status);
    }
    
    
    ...
        
}
```



```java
ErrorPageCustomizer
```

源代码：

```java
    @Value("${error.path:/error}")
    private String path = "/error";	//系统出现错误后来到error请求进行处理；（web.xml注册的错误页面规则）
```



```java
DefaultErrorViewResolver
```

源代码：

```java
public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
    ModelAndView modelAndView = this.resolve(String.valueOf(status), model);
    if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
        modelAndView = this.resolve((String)SERIES_VIEWS.get(status.series()), model);
    }

    return modelAndView;
}

private ModelAndView resolve(String viewName, Map<String, Object> model) {
    //默认SpringBoot可以去找到一个页面======error/状态码对应的页面（例如404）
    String errorViewName = "error/" + viewName;
    
    //如果模板引擎能够解析这个页面就用模板引擎进行解析
    TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName, this.applicationContext);
    
    //模板引擎可用的情况下返回到ErrorViewName指定的视图地址；不可用的情况下，就去静态资源文件夹下面找errorViewName对应的页面，相当于去找
    return provider != null ? new ModelAndView(errorViewName, model) : this.resolveResource(errorViewName, model);
}
```





步骤：一旦系统出现4xx或者5xx之类的错误：ErrorPageCustomizer就会生效（定制错误的响应规则）；就会来到/error请求；就会被BasicErrorController处理：

​	（1）响应页面：去哪一个页面是由DefaultErrorViewResolver解析得到的

```java
protected ModelAndView resolveErrorView(HttpServletRequest request, HttpServletResponse response, HttpStatus status, Map<String, Object> model) {
    Iterator var5 = this.errorViewResolvers.iterator();

    //所有的ErrorViewResolver的到ModelAndView
    ModelAndView modelAndView;
    do {
        if (!var5.hasNext()) {
            return null;
        }

        ErrorViewResolver resolver = (ErrorViewResolver)var5.next();
        modelAndView = resolver.resolveErrorView(request, status, model);
    } while(modelAndView == null);

    return modelAndView;
}
```







### （2），如何定制错误响应

- 如何定制错误的页面

  - **1）、有模板引擎的情况下；error/状态码;** 【将错误页面命名为  错误状态码.html 放在模板引擎文件夹里面的 error文件夹下】，发生此状态码的错误就会来到  对应的页面；

    我们可以使用4xx和5xx作为错误页面的文件名来匹配这种类型的所有错误，精确优先（优先寻找精确的状态码.html）；		

    页面能获取的信息；

  ​				timestamp：时间戳

  ​				status：状态码

  ​				error：错误提示

  ​				exception：异常对象

  ​				message：异常消息

  ​				errors：JSR303数据校验的错误都在这里

  - 2）、没有模板引擎（模板引擎找不到这个错误页面），静态资源文件夹下找；

  - 3）、以上都没有错误页面，就是默认来到SpringBoot默认的错误提示页面；

- 如何定制错误的json页面

  （1）自适应异常处理&返回定制json数据

```java
@ControllerAdvice
public class MyExceptionHandler {

    //1、浏览器客户端返回的都是json
    //无法做到自适应
    @ResponseBody
    @ExceptionHandler(UserNotExistException.class)
    public Map<String,Object> handleException(Exception e){
        Map<String,Object> map = new HashMap<>();
        map.put("code","user.notexist");
        map.put("message",e.getMessage());
        return map;
    }
    
 	   
}
```

​	（2）转发到/error进行自适应响应效果处理（原理查看BasicErrorController）

```java
@ExceptionHandler(UserNotExistException.class)
public String handleException(Exception e, HttpServletRequest request){
    Map<String,Object> map = new HashMap<>();
    //传入我们自己的错误状态码  4xx 5xx		否则就不会使用项目中的定制的错误页面，因为没有被项目的SpringBoot框架进行错误流程解析，也就不会用定制的错误页面显示
    /**
     * Integer statusCode = (Integer) request
     .getAttribute("javax.servlet.error.status_code");
     */
    request.setAttribute("javax.servlet.error.status_code",500);
    map.put("code","user.notexist");
    map.put("message","用户出错啦");

    request.setAttribute("ext",map);
    //转发到/error
    return "forward:/error";
}
```
### （3），将我们的定制数据携带出去；

出现错误以后，会来到/error请求，会被BasicErrorController处理，响应出去可以获取的数据是由getErrorAttributes得到的（是AbstractErrorController（ErrorController）规定的方法）；

​	1、完全来编写一个ErrorController的实现类【或者是编写AbstractErrorController的子类】，放在容器中；

​	2、页面上能用的数据，或者是json返回能用的数据都是通过errorAttributes.getErrorAttributes得到；

​			容器中DefaultErrorAttributes.getErrorAttributes()；默认进行数据处理的；

自定义ErrorAttributes

```java
//给容器中加入我们自己定义的ErrorAttributes
@Component
public class MyErrorAttributes extends DefaultErrorAttributes {

    @Override
    public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
        Map<String, Object> map = super.getErrorAttributes(requestAttributes, includeStackTrace);
        map.put("company","atguigu");
        return map;
    }
}
```

最终的效果：响应是自适应的，可以通过定制ErrorAttributes改变需要返回的内容，

![其他平台的错误页面](images/搜狗截图20180228135513.png)



主要原理：

BasicErrorController中：

```java
    @RequestMapping(
        produces = {"text/html"}
    )
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = this.getStatus(request);
        Map<String, Object> model = Collections.unmodifiableMap(this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        ModelAndView modelAndView = this.resolveErrorView(request, response, status, model);
        return modelAndView == null ? new ModelAndView("error", model) : modelAndView;
    }

    @RequestMapping
    @ResponseBody
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        Map<String, Object> body = this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.ALL));
        HttpStatus status = this.getStatus(request);
        return new ResponseEntity(body, status);
    }
```

model和body两个变量都是需要通过==**getErrorAttributes()**==得到的

而他的源代码是：

```java
public abstract class AbstractErrorController implements ErrorController {
	
    private final ErrorAttributes errorAttributes;
    ...
    
    protected Map<String, Object> getErrorAttributes(HttpServletRequest request, boolean includeStackTrace) {
        RequestAttributes requestAttributes = new ServletRequestAttributes(request);
        return this.errorAttributes.getErrorAttributes(requestAttributes, includeStackTrace);
    }
    
    ...
}
```

所以我们只需要重写一下这个方法，让他添加一些我们想要自定义添加的

而在DefaultErrorAttributes中，有一个

```java
    @Bean
    @ConditionalOnMissingBean(
        value = {ErrorAttributes.class},
        search = SearchStrategy.CURRENT
    )
	//这个注解的意思是，如果容器中没有ErrorAttributes这个组件的话，这个方法就会为容器自动配置一个DefaultErrorAttributes，如果有，那就不配置了
    public DefaultErrorAttributes errorAttributes() {
        return new DefaultErrorAttributes();
    }
```

==所以我们可以继承DefaultErrorAttributes，重写 getErrorAttributes给容器中加入一个我们自定义的ErrorAttributes，从而使容器不自己配置一个默认的ErrorAttributes的==

也就是上面所写的

```java
//给容器中加入我们自己定义的ErrorAttributes
@Component
public class MyErrorAttributes extends DefaultErrorAttributes {

    @Override
    public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
        Map<String, Object> map = super.getErrorAttributes(requestAttributes, includeStackTrace);
        map.put("company","atguigu");
        return map;
    }
}
```



## 10，配置嵌入式Servlet容器

SpringBoot默认使用Tomcat作为嵌入式的Servlet容器

![tomcat依赖图](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\tomcat依赖图.PNG)

问题：

### 1，如何定制和修改Servlet容器的相关配置

​		修改和Server有关的配置（Server.properties）

```properties
server.port=8081
server.context-path=/crud
server.tomcat.uri-encoding=UTF-8

//通用的Servlet容器设置
Server.xxx
//Tomcat的设置
server.tomcat.xxx
```

​		编写一个EmbeddedServletContainerCustomizer：嵌入式的Servlet容器的定制器：来修改Servlet容器的配置；

```java
    @Bean
    public EmbeddedServletContainerCustomizer embeddedServletContainerCustomizer(){
        return new EmbeddedServletContainerCustomizer() {
            @Override
            public void customize(ConfigurableEmbeddedServletContainer container) {
                container.setPort(8081);
            }
        };
    }
```

注意：SpringBoot2.0以上的话，不再使用EmbeddedServletContainerCustomizer，而是使用WsebServerFactoryCustomizer

注意：也可以查看ServerProperties类



### 2，注册Servletron的三大组件：

由于SpringBoot默认是以jar包的方式启动嵌入式的Servlet容器来启动SpringBoot的Web应用，没有web.xml文件；

注册三大组件用以下方式：

<u>**Servlet，Filter，Listener**</u>

ServletRegistrationBean

```java
    @Bean
    public ServletRegistrationBean myServlet(){
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(new MyServlet(),"/myServlet");
        registrationBean.setLoadOnStartup(1);
        return registrationBean;
    }
```

FilterRegistrationBean

```java
    @Bean
    public FilterRegistrationBean myFilter(){
        FilterRegistrationBean registrationBean = new FilterRegistrationBean();
        registrationBean.setFilter(new MyFilter());
        registrationBean.setUrlPatterns(Arrays.asList("/hello","/myServlet"));
        return registrationBean;
    }
```



ServletListenerRegistrationBean

```java
    @Bean
    public ServletListenerRegistrationBean myListener(){
        ServletListenerRegistrationBean<MyListener> registrationBean = new ServletListenerRegistrationBean<>(new MyListener());
        return registrationBean;
    }
```



 SpringBoot帮我们自动配置SpringMVC的时候，自动的注册了SpringMVC的前端控制器：DispatcherServlet；

```java
@Bean(
    name = {"dispatcherServletRegistration"}
)
@ConditionalOnBean(
    value = {DispatcherServlet.class},
    name = {"dispatcherServlet"}
)
public ServletRegistrationBean dispatcherServletRegistration(DispatcherServlet dispatcherServlet) {
    ServletRegistrationBean registration = new ServletRegistrationBean(dispatcherServlet, new String[]{this.serverProperties.getServletMapping()});
    
    //默认拦截： / ==》所有请求；包括静态资源，而不拦截jsp请求；/*==》会拦截jsp
    //可以通过server.servletPath来修改SpringMVC的前端控制器默认拦截的请求路径
    registration.setName("dispatcherServlet");
    registration.setLoadOnStartup(this.webMvcProperties.getServlet().getLoadOnStartup());
    if (this.multipartConfig != null) {
        registration.setMultipartConfig(this.multipartConfig);
    }

    return registration;
}
```







==SpringBoot能不能支持其他的Servlet容器？==

### 3，替换为其他嵌入式Servlet容器

![tomcat的Servlet继承结构](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\tomcat的Servlet继承结构.png)

默认支持：

Tomcat（默认使用）

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   引入web模块默认就是使用嵌入式的Tomcat作为Servlet容器；
</dependency>
```

Jetty

```xml
<!-- 引入web模块 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   <exclusions>
      <exclusion>
         <artifactId>spring-boot-starter-tomcat</artifactId>
         <groupId>org.springframework.boot</groupId>
      </exclusion>
   </exclusions>
</dependency>

<!--引入其他的Servlet容器-->
<dependency>
   <artifactId>spring-boot-starter-jetty</artifactId>
   <groupId>org.springframework.boot</groupId>
</dependency>
```

Undertow

```xml
<!-- 引入web模块 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   <exclusions>
      <exclusion>
         <artifactId>spring-boot-starter-tomcat</artifactId>
         <groupId>org.springframework.boot</groupId>
      </exclusion>
   </exclusions>
</dependency>

<!--引入其他的Servlet容器-->
<dependency>
   <artifactId>spring-boot-starter-undertow</artifactId>
   <groupId>org.springframework.boot</groupId>
</dependency>
```

### 4，嵌入式Servlet容器自动配置原理；

EmbeddedServletContainerAutoConfiguration：嵌入式的Servlet容器自动配置

```java
@AutoConfigureOrder(-2147483648)
@Configuration
@ConditionalOnWebApplication
@Import({EmbeddedServletContainerAutoConfiguration.BeanPostProcessorsRegistrar.class})
//导入了BeanPostProcessorsRegistrar：给容器中导入一些组件
//导入了EmbeddedServletContainerCustomizerBeanPostProcessor：嵌入式的Servlet容器的定制器的后置处理器
//后置处理器：Bean初始化前（创建完对象，属性还没赋值）时执行一些初始化工作

public class EmbeddedServletContainerAutoConfiguration {

    
    @Configuration
    @ConditionalOnClass({Servlet.class, Tomcat.class})//判断当前是否有Tomcat类（导入了Tomcat依赖就会有这个类）
    @ConditionalOnMissingBean(
        value = {EmbeddedServletContainerFactory.class},
        search = SearchStrategy.CURRENT
    )//判断当前容器中没有用户自定义的EmbeddedServletContainerFactory：嵌入式得Servlet容器工厂；EmbeddedServletContainerFactory的作用是：创建嵌入式得Servlet容器
    
    public static class EmbeddedTomcat {
        public EmbeddedTomcat() {
        }

        @Bean
        public TomcatEmbeddedServletContainerFactory tomcatEmbeddedServletContainerFactory() {
            return new TomcatEmbeddedServletContainerFactory();
        }
    }

    
    
}
```

EmbeddedServletContainerFactory：嵌入式的Servlet容器工厂

```java
public interface EmbeddedServletContainerFactory {
    //获取嵌入式得Servlet容器
    EmbeddedServletContainer getEmbeddedServletContainer(ServletContextInitializer... var1);
}
```

![ServletFactory图片](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\ServletFactory图片.png)

 EmbeddedServletContainer：嵌入式的Servlet容器

![ServletContainer](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\ServletContainer.png)



==在之前的pom.xml移除tomcat依赖，再导入其他的依赖，就可以使用其他的Servlet容器的原理;==

```java
public class EmbeddedServletContainerAutoConfiguration {

    ...
        
    //Undertow.class, SslClientAuthMode.class相当于判断容器中是否有Undertow的类
	@Configuration
    @ConditionalOnClass({Servlet.class, Undertow.class, SslClientAuthMode.class})
    @ConditionalOnMissingBean(
        value = {EmbeddedServletContainerFactory.class},
        search = SearchStrategy.CURRENT
    )
    public static class EmbeddedUndertow {
        public EmbeddedUndertow() {
        }

        @Bean
        public UndertowEmbeddedServletContainerFactory undertowEmbeddedServletContainerFactory() {
            return new UndertowEmbeddedServletContainerFactory();
        }
    }

    //Server.class, Loader.class, WebAppContext.class相当于判断容器中是否有Jetty的类
    @Configuration
    @ConditionalOnClass({Servlet.class, Server.class, Loader.class, WebAppContext.class})
    @ConditionalOnMissingBean(
        value = {EmbeddedServletContainerFactory.class},
        search = SearchStrategy.CURRENT
    )
    public static class EmbeddedJetty {
        public EmbeddedJetty() {
        }

        @Bean
        public JettyEmbeddedServletContainerFactory jettyEmbeddedServletContainerFactory() {
            return new JettyEmbeddedServletContainerFactory();
        }
    }
    
    
    ...
    
}   
```





以TomcatEmbeddedServletContainer为例

```java
    public EmbeddedServletContainer getEmbeddedServletContainer(ServletContextInitializer... initializers) {
        //创建一个Tomcat
        Tomcat tomcat = new Tomcat();
        
       
        //配置Tomcat的基本属性
        File baseDir = this.baseDirectory != null ? this.baseDirectory : this.createTempDir("tomcat");
        tomcat.setBaseDir(baseDir.getAbsolutePath());
        //配置Tomcat的连接器
        Connector connector = new Connector(this.protocol);
        tomcat.getService().addConnector(connector);
        this.customizeConnector(connector);
        tomcat.setConnector(connector);
        tomcat.getHost().setAutoDeploy(false);
        //配置Tomcat引擎
        this.configureEngine(tomcat.getEngine());
        Iterator var5 = this.additionalTomcatConnectors.iterator();

        while(var5.hasNext()) {
            Connector additionalConnector = (Connector)var5.next();
            tomcat.getService().addConnector(additionalConnector);
        }

        this.prepareContext(tomcat.getHost(), initializers);
        //将配置好的tomcat传入进去，并返回一个EmbeddedServletContainer（嵌入式的Servlet容器）；并且启动Tomcat服务器（因为在相关的源码调用中有一步是启动Tomcat服务器，所以可以说Tomcat是在Servlet创建好了之后，就会启动）
        return this.getTomcatEmbeddedServletContainer(tomcat);
    }
```

#### （1），我们对嵌入式容器的配置修改是怎么生效的

```java
//之前所学的有两种方法
ServerProperties;
EmbeddedServletContainerCustomizer;
```



EmbeddedServletContainerCustomizer;定制器帮我们修改了Servlet容器的配置

怎么修改的原理？

容器中导入了EmbeddedServletContainerCustomizerBeanPostProcessor（==EmbeddedServletContainerCustomizer（嵌入式的Servlet容器的定制器）的BeanPostProcessor（后置处理器）==）

```java

//初始化之前（创建好，但是属性还没赋值）
public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
    //如果当前初始化的是一个ConfigurableEmbeddedServletContainer类型的组件
    if (bean instanceof ConfigurableEmbeddedServletContainer) {
        //postProcessBeforeInitialization源代码如下
        this.postProcessBeforeInitialization((ConfigurableEmbeddedServletContainer)bean);
    }

    return bean;
}



//postProcessBeforeInitialization的源代码：
private void postProcessBeforeInitialization(ConfigurableEmbeddedServletContainer bean) {
    //getCustomizers()源代码如下
    Iterator var2 = this.getCustomizers().iterator();

    //获取所有的定制器，调用每一个定制器的customize来给Servlet容器进行赋值；
    while(var2.hasNext()) {
        EmbeddedServletContainerCustomizer customizer = (EmbeddedServletContainerCustomizer)var2.next();
        customizer.customize(bean);
    }

}




//getCustomizers()的源代码
private Collection<EmbeddedServletContainerCustomizer> getCustomizers() {
    if (this.customizers == null) {
        //从容器中获取所有这个类型（EmbeddedServletContainerCustomizer）的组件
        //所以！如果要定制Servlet容器，可以给容器中添加一个EmbeddedServletContainerCustomizer类型的组件
        this.customizers = new ArrayList(this.beanFactory.getBeansOfType(EmbeddedServletContainerCustomizer.class, false, false).values());
        Collections.sort(this.customizers, AnnotationAwareOrderComparator.INSTANCE);
        this.customizers = Collections.unmodifiableList(this.customizers);
    }

    return this.customizers;
}
//这是EmbeddedServletContainerCustomizer;对嵌入式容器的配置生效的第二种方法
```

==那么对嵌入式容器的配置生效的第一种方法ServerProperties，因为ServerProperties是实现了EmbeddedServletContainerCustomizer的类！==



#### （2）生效的完整步骤

==完整的步骤：==

（1）SpringBoot根据导入的依赖情况，给容器中添加对应的EmbeddedServletContainerFactory（例如在此处添加的是TomcatEmbeddedServletContainerFactory）

```java
@Configuration
@ConditionalOnClass({Servlet.class, Tomcat.class})
@ConditionalOnMissingBean(
    value = {EmbeddedServletContainerFactory.class},
    search = SearchStrategy.CURRENT
)
public static class EmbeddedTomcat {
    public EmbeddedTomcat() {
    }

    @Bean
    public TomcatEmbeddedServletContainerFactory tomcatEmbeddedServletContainerFactory() {
        return new TomcatEmbeddedServletContainerFactory();
    }
}
```

（2）一但在容器中创建对象就会触发后置处理器：EmbeddedServletContainerCustomizerBeanPostProcessor；只要是嵌入式的Servlet容器工厂，后置处理器就会工作

（3）后置处理器会从容器中获取所有的EmbeddedServletContainerCustomizer，调用定制器的定制方法

```java
//getCustomizers()的源代码
private Collection<EmbeddedServletContainerCustomizer> getCustomizers() {
    if (this.customizers == null) {
        //从容器中获取所有这个类型（EmbeddedServletContainerCustomizer）的组件
        //所以！如果要定制Servlet容器，可以给容器中添加一个EmbeddedServletContainerCustomizer类型的组件
        this.customizers = new ArrayList(this.beanFactory.getBeansOfType(EmbeddedServletContainerCustomizer.class, false, false).values());
        Collections.sort(this.customizers, AnnotationAwareOrderComparator.INSTANCE);
        this.customizers = Collections.unmodifiableList(this.customizers);
    }

    return this.customizers;
}
//这是EmbeddedServletContainerCustomizer;对嵌入式容器的配置生效的第二种方法
```



### 5，嵌入式Servlet容器启动原理

什么时候创建嵌入式的Servlet容器工厂？什么时候获取嵌入式的Servlet容器并启动Tomcat？



```java
    @Configuration
    @ConditionalOnClass({Servlet.class, Tomcat.class})
    @ConditionalOnMissingBean(
        value = {EmbeddedServletContainerFactory.class},
        search = SearchStrategy.CURRENT
    )
    public static class EmbeddedTomcat {
        public EmbeddedTomcat() {
        }

        @Bean
        public TomcatEmbeddedServletContainerFactory tomcatEmbeddedServletContainerFactory() {
            return new TomcatEmbeddedServletContainerFactory();
        }
    }
```





```java
    public EmbeddedServletContainer getEmbeddedServletContainer(ServletContextInitializer... initializers) {
        Tomcat tomcat = new Tomcat();
        File baseDir = this.baseDirectory != null ? this.baseDirectory : this.createTempDir("tomcat");
        tomcat.setBaseDir(baseDir.getAbsolutePath());
        Connector connector = new Connector(this.protocol);
        tomcat.getService().addConnector(connector);
        this.customizeConnector(connector);
        tomcat.setConnector(connector);
        tomcat.getHost().setAutoDeploy(false);
        this.configureEngine(tomcat.getEngine());
        Iterator var5 = this.additionalTomcatConnectors.iterator();

        while(var5.hasNext()) {
            Connector additionalConnector = (Connector)var5.next();
            tomcat.getService().addConnector(additionalConnector);
        }

        this.prepareContext(tomcat.getHost(), initializers);
        return this.getTomcatEmbeddedServletContainer(tomcat);
    }
```



==在IDEA中以DEBUG的方式跟踪创建过程！==

获取嵌入式Servlet容器工厂：

#### 1），SpringBoot应用启动运行run方法

#### 2），refreshContext(context)
refreshContext(context)：SpringBoot刷新IOC容器：【创建IOC容器对象，并初始化容器，创建容器中的每一个组件】；而根据是否是Web环境来创建不同的容器对象（代码如下）

```java
//重点代码句
context = this.createApplicationContext();

new FailureAnalyzers(context);
this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);

//重点代码句
this.refreshContext(context);


//creatApplicationContext()源代码
protected ConfigurableApplicationContext createApplicationContext() {
    Class<?> contextClass = this.applicationContextClass;
    if (contextClass == null) {
        try {
            
            
            //根据是否是web环境（webEnvironment）（也就是判断是否是web应用）来创建相应的IOC容器；是的话就创建：AnnotationConfigEmbeddedWebApplicationContext；不是的话就创建AnnotationConfigApplicationContext
            contextClass = Class.forName(this.webEnvironment ? "org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext" : "org.springframework.context.annotation.AnnotationConfigApplicationContext");
        
        
        
        } catch (ClassNotFoundException var3) {
            throw new IllegalStateException("Unable create a default ApplicationContext, please specify an ApplicationContextClass", var3);
        }
    }

    return (ConfigurableApplicationContext)BeanUtils.instantiate(contextClass);
}

```

#### 3），this.refresh(context)
this.refresh(context)刷新刚才创建好的IOC容器（刷新的源代码如下）

```java
public void refresh() throws BeansException, IllegalStateException {
    synchronized(this.startupShutdownMonitor) {
        this.prepareRefresh();
        ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
        this.prepareBeanFactory(beanFactory);

        try {
            this.postProcessBeanFactory(beanFactory);
            this.invokeBeanFactoryPostProcessors(beanFactory);
            this.registerBeanPostProcessors(beanFactory);
            this.initMessageSource();
            this.initApplicationEventMulticaster();
            this.onRefresh();
            this.registerListeners();
            this.finishBeanFactoryInitialization(beanFactory);
            this.finishRefresh();
        } catch (BeansException var9) {
            if (this.logger.isWarnEnabled()) {
                this.logger.warn("Exception encountered during context initialization - cancelling refresh attempt: " + var9);
            }

            this.destroyBeans();
            this.cancelRefresh(var9);
            throw var9;
        } finally {
            this.resetCommonCaches();
        }

    }
}
```

#### 4），onRefresh()
onRefresh()：web的IOC容器重写了onRefresh()方法

```java
//原来的onRefersh()
public abstract class AbstractApplicationContext extends DefaultResourceLoader implements ConfigurableApplicationContext, DisposableBean {
    
	protected void onRefresh() throws BeansException {
    }
    
}

//web应用重写的onRefresh()
public class EmbeddedWebApplicationContext extends GenericWebApplicationContext {
    protected void onRefresh() {
        super.onRefresh();

        try {
            this.createEmbeddedServletContainer();
        } catch (Throwable var2) {
            throw new ApplicationContextException("Unable to start embedded container", var2);
        }
    }       
}

//createEmbeddedServletContainer()源代码见第5点
```

#### 5），WebIOC容器会创建嵌入式的Servlet容器
WebIOC容器会创建嵌入式的Servlet容器：createEmbeddedServletContainer()

```java
private void createEmbeddedServletContainer() {
    EmbeddedServletContainer localContainer = this.embeddedServletContainer;
    //获取IOC容器
    ServletContext localServletContext = this.getServletContext();
    if (localContainer == null && localServletContext == null) {
        
        //获取嵌入式的Servlet容器工厂（详见第6点！！！！！）
        EmbeddedServletContainerFactory containerFactory = this.getEmbeddedServletContainerFactory();
        
        //详见第7点！！！！！
        this.embeddedServletContainer = containerFactory.getEmbeddedServletContainer(new ServletContextInitializer[]{this.getSelfInitializer()});
    } else if (localServletContext != null) {
        try {
            this.getSelfInitializer().onStartup(localServletContext);
        } catch (ServletException var4) {
            throw new ApplicationContextException("Cannot initialize servlet context", var4);
        }
    }

    this.initPropertySources();
}
```

#### 6），==获取嵌入式的Servlet容器工厂：==

EmbeddedServletContainerFactory containerFactory = this.getEmbeddedServletContainerFactory();

```java
//getEmbeddedServletContainerFactory()的源代码：
protected EmbeddedServletContainerFactory getEmbeddedServletContainerFactory() {
    
    //从IOC容器中获取EmbeddedServletContainerFactory组件：在上面所提到的TomcatEmbeddedServletContainerFactory创建对象，一旦后置处理器看到是这个对象，就会获取所有的定制器的定制方法来先定制Servlet容器的相关配置
    String[] beanNames = this.getBeanFactory().getBeanNamesForType(EmbeddedServletContainerFactory.class);
    
    
    if (beanNames.length == 0) {
        throw new ApplicationContextException("Unable to start EmbeddedWebApplicationContext due to missing EmbeddedServletContainerFactory bean.");
    } else if (beanNames.length > 1) {
        throw new ApplicationContextException("Unable to start EmbeddedWebApplicationContext due to multiple EmbeddedServletContainerFactory beans : " + StringUtils.arrayToCommaDelimitedString(beanNames));
    } else {
        return (EmbeddedServletContainerFactory)this.getBeanFactory().getBean(beanNames[0], EmbeddedServletContainerFactory.class);
    }
}

```

#### 7），==使用容器工厂获取嵌入式Servlet容器：==

this.embeddedServletContainer = containerFactory.getEmbeddedServletContainer(new ServletContextInitializer[]{this.getSelfInitializer()});

```java
//1
public EmbeddedServletContainer getEmbeddedServletContainer(ServletContextInitializer... initializers) {
    Tomcat tomcat = new Tomcat();
    File baseDir = this.baseDirectory != null ? this.baseDirectory : this.createTempDir("tomcat");
    tomcat.setBaseDir(baseDir.getAbsolutePath());
    Connector connector = new Connector(this.protocol);
    tomcat.getService().addConnector(connector);
    this.customizeConnector(connector);
    tomcat.setConnector(connector);
    tomcat.getHost().setAutoDeploy(false);
    this.configureEngine(tomcat.getEngine());
    Iterator var5 = this.additionalTomcatConnectors.iterator();

    while(var5.hasNext()) {
        Connector additionalConnector = (Connector)var5.next();
        tomcat.getService().addConnector(additionalConnector);
    }

    this.prepareContext(tomcat.getHost(), initializers);
    return this.getTomcatEmbeddedServletContainer(tomcat);
}
==============================================
//2
protected TomcatEmbeddedServletContainer getTomcatEmbeddedServletContainer(Tomcat tomcat) {
    return new TomcatEmbeddedServletContainer(tomcat, this.getPort() >= 0);
}
==============================================
//3
public TomcatEmbeddedServletContainer(Tomcat tomcat, boolean autoStart) {
    this.monitor = new Object();
    this.serviceConnectors = new HashMap();
    Assert.notNull(tomcat, "Tomcat Server must not be null");
    this.tomcat = tomcat;
    this.autoStart = autoStart;
    this.initialize();
}

private void initialize() throws EmbeddedServletContainerException {
    logger.info("Tomcat initialized with port(s): " + this.getPortsDescription(false));
    synchronized(this.monitor) {
        try {
            this.addInstanceIdToEngineName();

            try {
                this.removeServiceConnectors();
                
                //重点：这就是为什么拿到Tomcat容器对象后就会启动Tomcat
                //启动Tomcat；
                this.tomcat.start();
                
                this.rethrowDeferredStartupExceptions();
                Context context = this.findContext();

                try {
                    ContextBindings.bindClassLoader(context, this.getNamingToken(context), this.getClass().getClassLoader());
                } catch (NamingException var5) {
                }

                this.startDaemonAwaitThread();
            } catch (Exception var6) {
                containerCounter.decrementAndGet();
                throw var6;
            }
        } catch (Exception var7) {
            throw new EmbeddedServletContainerException("Unable to start embedded Tomcat", var7);
        }

    }
}
```



#### 8），创建并启动Servlet容器
嵌入式的Servlet容器创建对象并启动Servlet容器：（例如在此例中配置的Servlet容器是Tomcat的话，就启动Tomcat）（注意：在此步中IOC容器实际上还没初始化完成，因为在第3步的源代码中）

```java
            
//第3步的源代码
this.postProcessBeanFactory(beanFactory);
this.invokeBeanFactoryPostProcessors(beanFactory);
this.registerBeanPostProcessors(beanFactory);
this.initMessageSource();
this.initApplicationEventMulticaster();

this.onRefresh();

this.registerListeners();

//初始化IOC容器中所有的单实例组件！！！（就是我们自己写的Controller和Service才会在此处实现）
this.finishBeanFactoryInitialization(beanFactory);

this.finishRefresh();
```

==所以是：先启动Servlet容器，再将容器中剩下的没有创建出来的对象获取出来==



## 11，使用外置的Servlet容器

嵌入式的Servlet容器：

优点：简单便携

缺点：默认不支持jsp，优化定制比较复杂【使用定制器（ServerProperties，自定义EmbeddedServletContainerCustomizer），或是自己编写嵌入式Servlet容器的创建工厂（EmbeddedServletContainerFactory）】



但在实际开发中，需要使用jsp。所以我们可以使用外置的Servlet容器

### （1），外置的Servlet容器

步骤：

1），必须创建一个war项目（利用IDEA创建好目录结构）

2），==将嵌入式的Servlet容器指定为Provided==（意为目标环境已经有了tomcat了，打包的时候就不用带上tomcat）

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
```

3），==必须编写一个SpringBootServletInitializer的子类，并调用configure方法==

```java
//类名可以随意，但必须继承SpringBootServletInitializer
public class ServletInitializer extends SpringBootServletInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        
        //传入SpringBoot应用的主程序，就相当于告诉Servlet容器SpringBoot的主类（用SpringBootApplication标注的类）在那，该启动那个类
        return application.sources(SpringBoot04WebJarApplication.class);
    }

}
```

4），启动服务器



### （2），原理

jar包：执行SpringBoot主类的main方法，启动IOC容器，创建嵌入式的Servlet容器；

war包：启动服务器，**服务器启动SpringBoot应用**，启动IOC容器

而服务器能启动SpringBoot应用的关键：SpringBootServletInitializer

在Servlet3.0以上的规范中（==8.2.4：Shared libraries / runtimes pluggability==）有：

```html
The ServletContainerInitializer class is looked up via the jar services API.
For each application, an instance of the ServletContainerInitializer is
created by the container at application startup time. The framework providing an
implementation of the ServletContainerInitializer MUST bundle in the
META-INF/services directory of the jar file a file called

javax.servlet.ServletContainerInitializer, as per the jar services API,
that points to the implementation class of the ServletContainerInitializer.
In addition to the ServletContainerInitializer we also have an annotation -
HandlesTypes. The HandlesTypes annotation on the implementation of the
ServletContainerInitializer is used to express interest in classes that may
have annotations (type, method or field level annotations) specified in the value of
the HandlesTypes or if it extends / implements one those classes anywhere in the
class’ super types. The HandlesTy
```

规则：

（1）：服务器启动（Web应用启动）的时候会创建当前Web应用里面每一个jar包里面的ServletContainerInitializer实例；

（2）：ServletContainerInitializer的实现放在jar包的META-INF/services文件夹下 ，有一个名为：javax.servlet.ServletContainerInitializer的文件；内容就是ServletContainerInitializer实现类的全类名

（3）：还可以使用@HandlesTypes，在应用启动的时候加载我们感兴趣的类



流程：

（1）：启动Tomcat

（2）：org\springframework\spring-web\5.2.3.RELEASE\spring-web-5.2.3.RELEASE.jar!\META-INF\services\javax.servlet.ServletContainerInitializer

Spring的web模块里面有这个文件；

内容是：org.springframework.web.SpringServletContainerInitializer

（3）：SpringServletContainerInitializer将@HandlesTypes(WebApplicationInitializer.class)标注的所有这个类型的类都传入onStartup方法的Set<Class<?>>；为这些WebApplicationInitializer类型的类创建实例

（4）：每一个WebApplicationInitializer都调用自己的onStartup方法：

![WebApplicationInitializer及实现类](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\WebApplicationInitializer及实现类.png)

（5）：相当于我们自己写的SpringBootServletInitializer会被创建实例，并执行onStartup方法

（6）：SpringBootServletInitializer的实例执行onStartup方法的时候会createRootApplicationContext；创建容器！

（实现类没有onStartup就会使用父类的）

SpringBootServletInitializer的onStartup方法

```java
@Override
public void onStartup(ServletContext servletContext) throws ServletException {
    // Logger initialization is deferred in case an ordered
    // LogServletContextInitializer is being used
    this.logger = LogFactory.getLog(getClass());

    //创建容器
    WebApplicationContext rootAppContext = createRootApplicationContext(servletContext);

    if (rootAppContext != null) {
        servletContext.addListener(new ContextLoaderListener(rootAppContext) {
            @Override
            public void contextInitialized(ServletContextEvent event) {
                // no-op because the application context is already initialized
            }
        });
    }
    else {
        this.logger.debug("No ContextLoaderListener registered, as createRootApplicationContext() did not "
                          + "return an application context");
    }
}


//creatRootApplicationContext源代码
protected WebApplicationContext createRootApplicationContext(ServletContext servletContext) {
    //创建SpringApplicationBuilder(Spring应用的构建器)
    SpringApplicationBuilder builder = createSpringApplicationBuilder();
    builder.main(getClass());
    ApplicationContext parent = getExistingRootWebApplicationContext(servletContext);
    if (parent != null) {
        this.logger.info("Root context already created (using as parent).");
        servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, null);
        builder.initializers(new ParentContextApplicationContextInitializer(parent));
    }
    builder.initializers(new ServletContextApplicationContextInitializer(servletContext));
    builder.contextClass(AnnotationConfigServletWebServerApplicationContext.class);
    
    //调用congifure方法（由于子类重写了这个方法，在这里其实是调用子类的configure方法）将SpringBoot的主程序类传进来
    builder = configure(builder);
    
    builder.listeners(new WebEnvironmentPropertySourceInitializer(servletContext));
    
    //使用build创建一个Spring应用
    SpringApplication application = builder.build();
    if (application.getAllSources().isEmpty()
        && MergedAnnotations.from(getClass(), SearchStrategy.TYPE_HIERARCHY).isPresent(Configuration.class)) {
        application.addPrimarySources(Collections.singleton(getClass()));
    }
    Assert.state(!application.getAllSources().isEmpty(),
                 "No SpringApplication sources have been defined. Either override the "
                 + "configure method or add an @Configuration annotation");
    // Ensure error pages are registered
    if (this.registerErrorPageFilter) {
        application.addPrimarySources(Collections.singleton(ErrorPageFilterConfiguration.class));
    }
    //启动Spring应用
    return run(application);
}
```

（7）：Spring的应用就启动起来并创建IOC容器

```java
//run方法
public ConfigurableApplicationContext run(String... args) {
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    ConfigurableApplicationContext context = null;
    Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
    configureHeadlessProperty();
    SpringApplicationRunListeners listeners = getRunListeners(args);
    listeners.starting();
    try {
        ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
        ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
        configureIgnoreBeanInfo(environment);
        Banner printedBanner = printBanner(environment);
        context = createApplicationContext();
        exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
                                                         new Class[] { ConfigurableApplicationContext.class }, context);
        prepareContext(context, environment, listeners, applicationArguments, printedBanner);
        refreshContext(context);
        afterRefresh(context, applicationArguments);
        stopWatch.stop();
        if (this.logStartupInfo) {
            new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
        }
        listeners.started(context);
        callRunners(context, applicationArguments);
    }
    catch (Throwable ex) {
        handleRunFailure(context, ex, exceptionReporters, listeners);
        throw new IllegalStateException(ex);
    }

    try {
        listeners.running(context);
    }
    catch (Throwable ex) {
        handleRunFailure(context, ex, exceptionReporters, null);
        throw new IllegalStateException(ex);
    }
    return context;
}
```





## 12，Docker

### 1，简介

**Docker**是一个开源的应用容器引擎；是一个轻量级容器技术；

Docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；

运行中的这个镜像称为容器，容器启动是非常快速的。

![Docker图标](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\Docker图标.png)



![Docker原理抽象](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\Docker原理抽象.png)



### 2，核心概念

Docker可以把我们安装配置好的软件打包成一个镜像

Docker主机（Host）：安装了Docker程序的机器（Docker直接安装在操作系统之上的）

Docker客户端（Client）：连接Docker主机进行操作

Docker仓库（Registry）：用来保存各种打包好的软件镜像

Docker镜像（Images）：软件打包好的镜像；放在Docker仓库中

Docker容器（Container）：镜像启动后成为一个容器；容器是独立运行的一个或一组应用



使用Docker的步骤：

（1）：安装Docker

（2）：去Docker仓库中找到这个软件对应的镜像

（3）：使用Docker运行这个镜像；这个镜像就会成为一个Docker容器。

（4）：对容器的启动停止就是对这个软件的启动停止



### 3，安装Docker

#### 1），安装Linux虚拟机

​	1）、VMWare、VirtualBox（安装）；

​	2）、导入虚拟机文件centos7-atguigu.ova；

​	3）、双击启动linux虚拟机;使用  root/ 123456登陆

​	4）、使用客户端连接linux服务器进行命令操作；

​	5）、设置虚拟机网络；

​		桥接网络--》选好网卡--》接入网线；

​	6）、设置好网络以后使用命令重启虚拟机的网络

```shell
service network restart
```

​	7）、查看linux的ip地址

```shell
ip addr
```

​	8）、使用客户端连接linux；

#### 2），在Linux虚拟机上安装Docker

```shell
1、检查内核版本，必须是3.10及以上
uname -r
2、安装docker
yum install docker
3、输入y确认安装
4、启动docker
[root@localhost ~]# systemctl start docker
[root@localhost ~]# docker -v
Docker version 1.12.6, build 3e8e77d/1.12.6
5、开机启动docker
[root@localhost ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
6、停止docker
systemctl stop docker
```

### 4、Docker常用命令&操作

#### 1）、镜像操作

| 操作 | 命令                                            | 说明                                                     |
| ---- | ----------------------------------------------- | -------------------------------------------------------- |
| 检索 | docker  search 关键字  eg：docker  search redis | 我们经常去docker  hub上检索镜像的详细信息，如镜像的TAG。 |
| 拉取 | docker pull 镜像名:tag                          | :tag是可选的，tag表示标签，多为软件的版本，默认是latest  |
| 列表 | docker images                                   | 查看所有本地镜像                                         |
| 删除 | docker rmi image-id                             | 删除指定的本地镜像                                       |

https://hub.docker.com/

#### 2）、容器操作

软件镜像（QQ安装程序）----运行镜像----产生一个容器（正在运行的软件，运行的QQ）；

步骤：

````shell
1、搜索镜像
[root@localhost ~]# docker search tomcat
2、拉取镜像
[root@localhost ~]# docker pull tomcat
3、根据镜像启动容器
docker run --name mytomcat -d tomcat:latest
4、docker ps  
查看运行中的容器
5、 停止运行中的容器
docker stop  容器的id
6、查看所有的容器
docker ps -a
7、启动容器
docker start 容器id
8、删除一个容器
 docker rm 容器id
9、启动一个做了端口映射的tomcat
[root@localhost ~]# docker run -d -p 8888:8080 tomcat
-d：后台运行
-p: 将主机的端口映射到容器的一个端口    主机端口:容器内部的端口

10、为了演示简单关闭了linux的防火墙
service firewalld status ；查看防火墙状态
service firewalld stop：关闭防火墙
11、查看容器的日志
docker logs container-name/container-id

更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档

````



#### 3）、安装MySQL示例

```shell
docker pull mysql
```



错误的启动

```shell
[root@localhost ~]# docker run --name mysql01 -d mysql
42f09819908bb72dd99ae19e792e0a5d03c48638421fa64cce5f8ba0f40f5846

mysql退出了
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS               NAMES
42f09819908b        mysql               "docker-entrypoint.sh"   34 seconds ago      Exited (1) 33 seconds ago                            mysql01
538bde63e500        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       compassionate_
goldstine
c4f1ac60b3fc        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       lonely_fermi
81ec743a5271        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       sick_ramanujan


//错误日志
[root@localhost ~]# docker logs 42f09819908b
error: database is uninitialized and password option is not specified 
  You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD；这个三个参数必须指定一个
```

正确的启动

```shell
[root@localhost ~]# docker run --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
b874c56bec49fb43024b3805ab51e9097da779f2f572c22c695305dedd684c5f
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
b874c56bec49        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 3 seconds        3306/tcp            mysql01
```

做了端口映射

```shell
[root@localhost ~]# docker run -p 3306:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
ad10e4bc5c6a0f61cbad43898de71d366117d120e39db651844c0e73863b9434
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ad10e4bc5c6a        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   mysql02
```



几个其他的高级操作

```
docker run --name mysql03 -v /conf/mysql:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
把主机的/conf/mysql文件夹挂载到 mysqldocker容器的/etc/mysql/conf.d文件夹里面
改mysql的配置文件就只需要把mysql配置文件放在自定义的文件夹下（/conf/mysql）

docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
指定mysql的一些配置参数
```



## 13，SpringBoot与数据访问

### 1，JDBC

pom.xml

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
```

application.yml

```yaml
spring:
  datasource:
    username: root
    data-password: 123456
    url: jdbc:mysql://localhost:3306/jdbc?serverTimezone=UTC
    driver-class-name: com.mysql.jdbc.Driver
#url中的时区在mysql新版本必须加，不然会报错
```

效果：

​	默认是用org.apache.tomcat.jdbc.pool.DataSource作为数据源；

​	数据源的相关配置都在DataSourceProperties里面；

org.springframework.boot.autoconfigure.jdbc





==DataSourceConfiguration源代码：==

```java
abstract class DataSourceConfiguration {
    DataSourceConfiguration() {
    }

    protected static <T> T createDataSource(DataSourceProperties properties, Class<? extends DataSource> type) {
        return properties.initializeDataSourceBuilder().type(type).build();
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnMissingBean({DataSource.class})
    @ConditionalOnProperty(
        name = {"spring.datasource.type"}
    )
    static class Generic {
        Generic() {
        }

        @Bean
        DataSource dataSource(DataSourceProperties properties) {
            return properties.initializeDataSourceBuilder().build();
        }
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnClass({BasicDataSource.class})
    @ConditionalOnMissingBean({DataSource.class})
    @ConditionalOnProperty(
        name = {"spring.datasource.type"},
        havingValue = "org.apache.commons.dbcp2.BasicDataSource",
        matchIfMissing = true
    )
    static class Dbcp2 {
        Dbcp2() {
        }

        @Bean
        @ConfigurationProperties(
            prefix = "spring.datasource.dbcp2"
        )
        BasicDataSource dataSource(DataSourceProperties properties) {
            return (BasicDataSource)DataSourceConfiguration.createDataSource(properties, BasicDataSource.class);
        }
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnClass({HikariDataSource.class})
    @ConditionalOnMissingBean({DataSource.class})
    @ConditionalOnProperty(
        name = {"spring.datasource.type"},
        havingValue = "com.zaxxer.hikari.HikariDataSource",
        matchIfMissing = true
    )
    static class Hikari {
        Hikari() {
        }

        @Bean
        @ConfigurationProperties(
            prefix = "spring.datasource.hikari"
        )
        HikariDataSource dataSource(DataSourceProperties properties) {
            HikariDataSource dataSource = (HikariDataSource)DataSourceConfiguration.createDataSource(properties, HikariDataSource.class);
            if (StringUtils.hasText(properties.getName())) {
                dataSource.setPoolName(properties.getName());
            }

            return dataSource;
        }
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnClass({org.apache.tomcat.jdbc.pool.DataSource.class})
    @ConditionalOnMissingBean({DataSource.class})
    @ConditionalOnProperty(
        name = {"spring.datasource.type"},
        havingValue = "org.apache.tomcat.jdbc.pool.DataSource",
        matchIfMissing = true
    )
    static class Tomcat {
        Tomcat() {
        }

        @Bean
        @ConfigurationProperties(
            prefix = "spring.datasource.tomcat"
        )
        org.apache.tomcat.jdbc.pool.DataSource dataSource(DataSourceProperties properties) {
            org.apache.tomcat.jdbc.pool.DataSource dataSource = (org.apache.tomcat.jdbc.pool.DataSource)DataSourceConfiguration.createDataSource(properties, org.apache.tomcat.jdbc.pool.DataSource.class);
            DatabaseDriver databaseDriver = DatabaseDriver.fromJdbcUrl(properties.determineUrl());
            String validationQuery = databaseDriver.getValidationQuery();
            if (validationQuery != null) {
                dataSource.setTestOnBorrow(true);
                dataSource.setValidationQuery(validationQuery);
            }

            return dataSource;
        }
    }
}
```





1，参考DataSourceConfiguration，根据配置创建数据源，默认使用Tomcat连接池

```java
spring.datasource.type//指定自定义的数据源类型
```

2、SpringBoot默认可以支持；

```
org.apache.tomcat.jdbc.pool.DataSource、HikariDataSource、BasicDataSource、
```

3、自定义数据源类型

```java
    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnMissingBean({DataSource.class})
    @ConditionalOnProperty(
        name = {"spring.datasource.type"}
    )
    static class Generic {
        Generic() {
        }

        @Bean
        DataSource dataSource(DataSourceProperties properties) {
             //使用DataSourceBuilder创建数据源，利用反射创建响应type的数据源，并且绑定相关属性
            return properties.initializeDataSourceBuilder().build();
        }
    }
```

原理：

initializeDataSourceBuilder()源代码：

```Java
public DataSourceBuilder<?> initializeDataSourceBuilder() {
    return DataSourceBuilder.create(this.getClassLoader()).type(this.getType()).driverClassName(this.determineDriverClassName()).url(this.determineUrl()).username(this.determineUsername()).password(this.determinePassword());
}



//creat()源代码
//返回一个DataSourceBuilder
public static DataSourceBuilder<?> create(ClassLoader classLoader) {
    return new DataSourceBuilder(classLoader);
}

//在调用这个DataSourceBuilder(classLoader)的build方法
public T build() {
    Class<? extends DataSource> type = this.getType();
    DataSource result = (DataSource)BeanUtils.instantiateClass(type);
    this.maybeGetDriverClassName();
    this.bind(result);
    return result;
}
```

4、**DataSourceInitializer：ApplicationListener**；

​	作用：

​		1）、runSchemaScripts();运行建表语句；

​		2）、runDataScripts();运行插入数据的sql语句；

默认只需要将文件命名为：

```properties
schema-*.sql、data-*.sql
默认规则：schema.sql，schema-all.sql；
可以使用   
	schema:
      - classpath:department.sql
      指定位置
```

注意：新版本的DataSourceInitializer已经使用DataSourceInitializerInvoker

源代码：

```java
@Configuration(
    proxyBeanMethods = false
)
@Import({DataSourceInitializerInvoker.class, DataSourceInitializationConfiguration.Registrar.class})
class DataSourceInitializationConfiguration {

    ...
}

//DataSourceInitializerInvoker源代码
class DataSourceInitializerInvoker implements ApplicationListener<DataSourceSchemaCreatedEvent>, InitializingBean {
    private static final Log logger = LogFactory.getLog(DataSourceInitializerInvoker.class);
    private final ObjectProvider<DataSource> dataSource;
    private final DataSourceProperties properties;
    private final ApplicationContext applicationContext;
    private DataSourceInitializer dataSourceInitializer;
    private boolean initialized;

    ...

}

//DataSourceInitializer
class DataSourceInitializer {


    ...


        boolean createSchema() {
        List<Resource> scripts = this.getScripts("spring.datasource.schema", this.properties.getSchema(), "schema");
        if (!scripts.isEmpty()) {
            if (!this.isEnabled()) {
                logger.debug("Initialization disabled (not running DDL scripts)");
                return false;
            }

            String username = this.properties.getSchemaUsername();
            String password = this.properties.getSchemaPassword();
            this.runScripts(scripts, username, password);
        }

        return !scripts.isEmpty();
    }   

    ...

        //默认规则的原理：   
   	private List<Resource> getScripts(String propertyName, List<String> resources, String fallback) {
        if (resources != null) {
            return this.getResources(propertyName, resources, true);
        } else {
            String platform = this.properties.getPlatform();
            List<String> fallbackResources = new ArrayList();
            fallbackResources.add("classpath*:" + fallback + "-" + platform + ".sql");
            fallbackResources.add("classpath*:" + fallback + ".sql");
            return this.getResources(propertyName, fallbackResources, false);
        }
    }


    ...
        
}  
```

5、操作数据库：自动配置了JdbcTemplate操作数据库

### 2，整合Druid

```java
导入druid数据源
@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid(){
       return  new DruidDataSource();
    }

    //配置Druid的监控
    //1、配置一个管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
        Map<String,String> initParams = new HashMap<>();

        initParams.put("loginUsername","admin");
        initParams.put("loginPassword","123456");
        initParams.put("allow","");//默认就是允许所有访问
        initParams.put("deny","192.168.15.21");

        bean.setInitParameters(initParams);
        return bean;
    }


    //2、配置一个web监控的filter
    @Bean
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        Map<String,String> initParams = new HashMap<>();
        initParams.put("exclusions","*.js,*.css,/druid/*");

        bean.setInitParameters(initParams);

        bean.setUrlPatterns(Arrays.asList("/*"));

        return  bean;
    }
}

```







### 3、整合MyBatis

```xml
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.1</version>
		</dependency>
```

![mybatis依赖](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\mybatis依赖.png)

步骤：

1）、配置数据源相关属性（见上一节Druid）

2）、给数据库建表

3）、创建JavaBean

4）、注解版

```java
//指定这是一个操作数据库的mapper
@Mapper
public interface DepartmentMapper {

    @Select("select * from department where id=#{id}")
    public Department getDeptById(Integer id);

    @Delete("delete from department where id=#{id}")
    public int deleteDeptById(Integer id);

    @Options(useGeneratedKeys = true,keyProperty = "id")
    @Insert("insert into department(departmentName) values(#{departmentName})")
    public int insertDept(Department department);

    @Update("update department set departmentName=#{departmentName} where id=#{id}")
    public int updateDept(Department department);
}
```

问题：

自定义MyBatis的配置规则；给容器中添加一个ConfigurationCustomizer；

```java
@org.springframework.context.annotation.Configuration
public class MyBatisConfig {

    @Bean
    public ConfigurationCustomizer configurationCustomizer(){
        return new ConfigurationCustomizer(){

            @Override
            public void customize(Configuration configuration) {
                configuration.setMapUnderscoreToCamelCase(true);
            }
        };
    }
}
```



```java
使用MapperScan批量扫描所有的Mapper接口；
@MapperScan(value = "com.atguigu.springboot.mapper")
@SpringBootApplication
public class SpringBoot06DataMybatisApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBoot06DataMybatisApplication.class, args);
	}
}
```

5）、配置文件版

```yaml
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml 指定全局配置文件的位置
  mapper-locations: classpath:mybatis/mapper/*.xml  指定sql映射文件的位置
```

更多使用参照

http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/





### 4、整合SpringData JPA

#### 1）、SpringData简介

![jpa](D:\Typora图片\SpringBoot\第一次建立Spring Boot项目\jpa.png)

#### 2）、整合SpringData JPA

JPA:ORM（Object Relational Mapping）；

1）、编写一个实体类（bean）和数据表进行映射，并且配置好映射关系；

```java
//使用JPA注解配置映射关系
@Entity //告诉JPA这是一个实体类（和数据表映射的类）
@Table(name = "tbl_user") //@Table来指定和哪个数据表对应;如果省略默认表名就是user；
public class User {

    @Id //这是一个主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)//自增主键
    private Integer id;

    @Column(name = "last_name",length = 50) //这是和数据表对应的一个列
    private String lastName;
    @Column //省略默认列名就是属性名
    private String email;
```

2）、编写一个Dao接口来操作实体类对应的数据表（Repository）

```java
//继承JpaRepository来完成对数据库的操作
public interface UserRepository extends JpaRepository<User,Integer> {
}

```

3）、基本的配置JpaProperties

```yaml
spring:  
 jpa:
    hibernate:
#     更新或者创建数据表结构
      ddl-auto: update
#    控制台显示SQL
    show-sql: true
```





## 14，启动配置原理

几个重要的事件回调机制

配置在META-INF/spring.factories

**ApplicationContextInitializer**

**SpringApplicationRunListener**



只需要放在ioc容器中

**ApplicationRunner**

**CommandLineRunner**



启动流程：

### **1、创建SpringApplication对象**

```java
initialize(sources);
private void initialize(Object[] sources) {
    //保存主配置类
    if (sources != null && sources.length > 0) {
        this.sources.addAll(Arrays.asList(sources));
    }
    //判断当前是否一个web应用
    this.webEnvironment = deduceWebEnvironment();
    //从类路径下找到META-INF/spring.factories配置的所有ApplicationContextInitializer；然后保存起来
    setInitializers((Collection) getSpringFactoriesInstances(
        ApplicationContextInitializer.class));
    //从类路径下找到ETA-INF/spring.factories配置的所有ApplicationListener
    setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
    //从多个配置类中找到有main方法的主配置类
    this.mainApplicationClass = deduceMainApplicationClass();
}
```

![](C:/Users/蔡梓文/Desktop/images/搜狗截图20180306145727.png)

![](C:/Users/蔡梓文/Desktop/images/搜狗截图20180306145855.png)

### 2、运行run方法

```java
public ConfigurableApplicationContext run(String... args) {
   StopWatch stopWatch = new StopWatch();
   stopWatch.start();
   ConfigurableApplicationContext context = null;
   FailureAnalyzers analyzers = null;
   configureHeadlessProperty();
    
   //获取SpringApplicationRunListeners；从类路径下META-INF/spring.factories
   SpringApplicationRunListeners listeners = getRunListeners(args);
    //回调所有的获取SpringApplicationRunListener.starting()方法
   listeners.starting();
   try {
       //封装命令行参数
      ApplicationArguments applicationArguments = new DefaultApplicationArguments(
            args);
      //准备环境
      ConfigurableEnvironment environment = prepareEnvironment(listeners,
            applicationArguments);
       		//创建环境完成后回调SpringApplicationRunListener.environmentPrepared()；表示环境准备完成
       
      Banner printedBanner = printBanner(environment);
       
       //创建ApplicationContext；决定创建web的ioc还是普通的ioc
      context = createApplicationContext();
       
      analyzers = new FailureAnalyzers(context);
       //准备上下文环境;将environment保存到ioc中；而且applyInitializers()；
       //applyInitializers()：回调之前保存的所有的ApplicationContextInitializer的initialize方法
       //回调所有的SpringApplicationRunListener的contextPrepared()；
       //
      prepareContext(context, environment, listeners, applicationArguments,
            printedBanner);
       //prepareContext运行完成以后回调所有的SpringApplicationRunListener的contextLoaded（）；
       
       //s刷新容器；ioc容器初始化（如果是web应用还会创建嵌入式的Tomcat）；Spring注解版
       //扫描，创建，加载所有组件的地方；（配置类，组件，自动配置）
      refreshContext(context);
       //从ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调
       //ApplicationRunner先回调，CommandLineRunner再回调
      afterRefresh(context, applicationArguments);
       //所有的SpringApplicationRunListener回调finished方法
      listeners.finished(context, null);
      stopWatch.stop();
      if (this.logStartupInfo) {
         new StartupInfoLogger(this.mainApplicationClass)
               .logStarted(getApplicationLog(), stopWatch);
      }
       //整个SpringBoot应用启动完成以后返回启动的ioc容器；
      return context;
   }
   catch (Throwable ex) {
      handleRunFailure(context, listeners, analyzers, ex);
      throw new IllegalStateException(ex);
   }
}
```

### 3、事件监听机制

配置在META-INF/spring.factories

**ApplicationContextInitializer**

```java
public class HelloApplicationContextInitializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {
    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        System.out.println("ApplicationContextInitializer...initialize..."+applicationContext);
    }
}

```



