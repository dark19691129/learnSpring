## Spring整合Hibernate



1. 由IOC容器来管理Hibernate的sessionFactory
2. 让Hibernate使用上Spring的声明式事务





## 整合步骤

### 加入Hibernate

1. ​	jar包

   hibernate-jpa-2.0-api-1.0.1.Final.jar

   jta-1.1.jar

   记得要添加

2. ​	添加hibernate的配置文件：hibernate.cfg.xml

   ```java
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE hibernate-configuration PUBLIC
   		"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
   		"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
   <hibernate-configuration>
       <session-factory>
       	<!-- 配置hibernate的基本属性 -->
       	<!-- 1.数据源需配置到容器中，所以此处不需要再配置数据源 -->
   		<!-- 2.关联的.hbm.xml也在IOC容器配置SessionFactory实例时进行配置 --> 
   		<!-- 3.配置Hibernate的基本属性：方言，SQL显示及格式化，生成数据表的二级缓存 --> 
   		<property name="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</property>  
   		<property name="hibernate.show_sql">true</property>
   		<property name="hibernate.format_sql">true</property>
   		
   		<property name="hibernate.hbm2ddl.auto">update</property>
   		
   		<!-- 配置hibernate相关的二级缓存属性 -->
   		
       </session-factory>
   </hibernate-configuration>
   ```

3. ​	编写持久化类对应的.hbm.xml文件

### 加入Spring

### 整合

Spring配置文件

```java
	<!-- 配置数据源 -->
	<!-- 导入资源文件（需要使用context命名空间） -->
	<context:property-placeholder location="classpath:db.properties"/>
	
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="user" value="${jdbc.user}"></property>
		<property name="password" value="${jdbc.password}"></property>
		<property name="driverClass" value="${jdbc.driverClass}"></property>
		<property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
		
		<property name="initialPoolSize" value="${jdbc.initPoolSize}"></property>
		<property name="maxPoolSize" value="${jdbc.maxPoolSize}"></property>
	</bean>
	
	
	<!-- 配置Hibernate的SessionFactory实例 -->
	<bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
		<!-- 配置数据源属性 -->
		<property name="dataSource" ref="dataSource"></property>
		<!-- 配置Hibernate配置文件的位置以及名称 -->
		<property name="configLocation" value="classpath:hibernate.cfg.xml"></property>
		<!-- 配置hibernate映射文件以及名称，可以使用通配符 -->
		<property name="mappingLocations" value="classpath:spring/hibernate/entities/*.hbm.xml"></property>
	</bean>
			



	<!-- 配置Spring的声明式事务 -->
	<!-- 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
		<property name="sessionFactory" ref="sessionFactory"></property>
	</bean>
		
	<!-- 配置事务属性，需要事务管理器 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<tx:method name="get*" read-only="true"/>
			<tx:method name="*"/>
		</tx:attributes>
	</tx:advice>
	
	
	<!-- 配置事务切入点，并把切入点和事务属性关联起来 -->
	<aop:config>
		<aop:pointcut expression="execution(* spring.hibernate.service.*.*(..))" 
			id="txPointcut"/>
		<aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
	</aop:config>
	
```

### 编写代码

 Spring hibernate事务的流程

1：在方法开始之前

- 获取Session
- 把Session和当前线程绑定，这样就可以在Dao中使用SessionFactory的getCurrentSession()方法来获取Session
- 开启事务

2：若方法正常结束，即没有出现异常，则

- 提交事务
- 使和当前线程绑定的Session解除绑定
- 关闭Session

3：若方法出现异常

- 回滚事务
- 使和当前线程绑定的Session解除绑定
- 关闭Session





BookShopDaoImpl类

```java
@Repository
public class BookShopDaoImpl implements BookShopDao {

	
	@Autowired
	private SessionFactory sessionFactory;
	
	
	/**
	 * 	不推荐使用HibernateTemplate 和 HibernateDaoSupport
	 * 	因为这样会导致Hibernate和Spring的api耦合
	 * 	可移植性变差
	 */
	//private HibernateTemplate hibernateTemplate;
	
	
	//获取和当前线程绑定的session
	private Session getSession() {
		return sessionFactory.getCurrentSession();
	}
	
	
	@Override
	public int findBookPriceByIsbn(String isbn) {
		String hql="SELECT b.price FROM Book b WHERE b.isbn=? ";
		Query query=getSession().createQuery(hql).setString(0, isbn);
		return (Integer) query.uniqueResult();
	}

	@Override
	public void updateBookStock(String isbn) {
		//验证书的库存是否足够
		String hql2="SELECT b.stock FROM Book b WHERE b.isbn=? ";
		int stock=(int) getSession().createQuery(hql2).setString(0, isbn).uniqueResult();
		if(stock==0) {
			throw new BookStockException("库存不足！！！！！！");
		}

		String hql="UPDATE Book b SET b.stock = b.stock -1 WHERE b.isbn= ?";
		getSession().createQuery(hql).setString(0, isbn).executeUpdate();
		
	}

	@Override
	public void updateUserAccount(int price, String username) {
		//验证余额是否足够
		String hql2="SELECT a.balance FROM Account a WHERE a.username= ?";
		int balance=(int) getSession().createQuery(hql2).setString(0, username).uniqueResult();
		
		if(balance < price) {
			throw new UserAccountException("余额不足");
		}
		
		String hql="UPDATE Account a SET a.balance = a.balance - ? WHERE a.username= ?";
		getSession().createQuery(hql).setInteger(0, price).setString(1, username).executeUpdate();
		
	}

}
```

测试类

```java
class SpringHibernateTest {

	private ApplicationContext context=null;
	private BookShopService bookShopService;
	private Cashier cashier;
	
	{
		context=new ClassPathXmlApplicationContext("applicationContext.xml");
		bookShopService=context.getBean(BookShopService.class);
		cashier=context.getBean(Cashier.class);
	}
	@Test
	public void testCashier() {
		cashier.checkout("aa", Arrays.asList("1001","1002"));
	}
	
	
	
	
	public void testBookShopService() {
		bookShopService.purchase("aa", "1001");
	}
	
	

	public void testDataSource() throws SQLException {
		DataSource dataSource=context.getBean(DataSource.class);
		System.out.println(dataSource.getConnection());
		
		
	}

}

```

