## Spring事务管理

事务：一系列动作，他们被当作一个单独的工作单元，这些动作要么全部完成，要么全部不起作用

 Dao层

```java
@Repository("bookShopDao")
public class BookShopDaoImpl implements BookShopDao {

	@Autowired
	private JdbcTemplate jdbcTemplate;
	
	@Override
	public int findBookPriceByIsbn(String isbn) {
		System.out.println("=====this is findBookPriceByIsbn(String isbn)=====");
		String sql="SELECT price FROM book WHERE isbn=?";

		return jdbcTemplate.queryForObject(sql, Integer.class,isbn);
	}

	@Override
	public void updateBookStock(String isbn) {
		System.out.println("=====this is updateBookStock(String isbn)=====");
		//检查书的库存是否足够，不够则抛出异常
		String sql2="SELECT stock FROM book_stock WHERE isbn= ?";
		int stock= jdbcTemplate.queryForObject(sql2, Integer.class,isbn);
		if(stock==0) {
			throw new BookStockException("库存不足");
		}
		
		String sql="UPDATE book_stock SET stock = stock - 1 WHERE isbn= ?";
		jdbcTemplate.update(sql,isbn);
	}

	@Override
	public void updateUserAccount(int price,String username) {
		System.out.println("=====this is updateUserAccount(String username, int price)=====");
		//验证余额是否不足 
		String sql2="SELECT balance FROM account WHERE username= ?";
		int balance= jdbcTemplate.queryForObject(sql2, Integer.class,username);
		if(balance < price) {
			throw new UserAccountException("余额不足");
		}
		
		String sql="UPDATE account SET balance = balance - ? WHERE username= ? ";
		jdbcTemplate.update(sql,price,username);
	
	}

}

```

测试类

```java
class SpringTransactionTest {

	private ApplicationContext context=null;
	private BookShopDao bookShopDao=null;
	private BookShopService bookShopService=null;
	{
		context=new ClassPathXmlApplicationContext("application-context1.xml");
		bookShopDao=context.getBean(BookShopDao.class);
		bookShopService=context.getBean(BookShopService.class);
	}

	/**
	 * 用户的购买服务
	 */
	@Test
	public void testBookShopService() {
		bookShopService.purchase("AA", "1001");
	}
	/**
	 * 更新用户的余额
	 */
	public void testBookShopDaoUpdateUserAccount() {
		System.out.println("this is testBookShopDaoUpdateUserAccount()");
		bookShopDao.updateUserAccount(100,"AA");
	}
	
	/**
	 * 更新书的库存
	 */
	public void testBookShopDaoUpdateBookStock() {
		System.out.println("this is testBookShopDaoUpdateBookStock()");
		bookShopDao.updateBookStock("1001");
	}
	
	/**
	 * 根据书号查询书的单价
	 */
	public void testBookShopDaoFindPriceByIsbn() {
		System.out.println("this is testBookShopDaoFindPriceByIsbn()");
		System.out.println(bookShopDao.findBookPriceByIsbn("1001"));
	}

}

```

Spring配置文件

```java
	<context:component-scan base-package="com.test"></context:component-scan>
	
	<!-- 导入资源文件 -->
	<context:property-placeholder location="classpath:db.properties"></context:property-placeholder>
	
	
	<!-- 配置datasource数据源 -->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="user" value="${jdbc.user}"></property>
		<property name="password" value="${jdbc.password}"></property>
		<property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
		<property name="driverClass" value="${jdbc.driverClass}"></property>
		
		<property name="initialPoolSize" value="${jdbc.initPoolSize}"></property>
		<property name="maxPoolSize" value="${jdbc.maxPoolSize}"></property>
		
	</bean>
	
	<!-- 配置JdbcTemplate -->
	<bean id="jdbcTemplate"
		class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>	
	</bean>


	<!-- 配置 namedParameterJdbcTemplate，该对象可以使用具名参数，其没有无参构造方法，所以在配置Bean时，需要传入dataSource参数-->
	<bean id="namedParameterJdbcTemplate"
		class="org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate">
		<constructor-arg ref="dataSource"></constructor-arg>
	</bean>
```

但无法执行事务：

执行购买类：

```java
@Service("bookShopService")
public class BookShopServiceImpl implements BookShopService{

	
	@Autowired
	private BookShopDao bookShopDao;
	
	@Override
	public void purchase(String username, String isbn) {
		System.out.println("*****this is purchase(String username, String isbn)");
		//获取书的单价
		int price =bookShopDao.findBookPriceByIsbn(isbn);
		//更新书的库存
		bookShopDao.updateBookStock(isbn);
		//更新用户的余额
		bookShopDao.updateUserAccount(price, username);	
	}
}

```

上述存在的问题是，一但余额不足以购买时，会发生：库存会变化（-1），

**本来思路是：余额变化后，若余额足以购买，则更新库存，**

**实际情况是：余额变化后，无论余额是否足够，库存都会被更新！！**



## 使用了Spring的事务管理

步骤：

在配置文件上加入

```java
	<!-- 配置事务管理器 -->
	<bean id="transactionManager" 
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>


	<!-- 启用事务注解 -->
	<tx:annotation-driven transaction-manager="transactionManager"/>

```

然后在要进行

事务管理的方法上加入事务的注解

```java
	//添加事务注解
	@Transactional
	@Override
	public void purchase(String username, String isbn) {
		System.out.println("*****this is purchase(String username, String isbn)");
		//获取书的单价
		int price =bookShopDao.findBookPriceByIsbn(isbn);
		//更新书的库存
		bookShopDao.updateBookStock(isbn);
		//更新用户的余额
		bookShopDao.updateUserAccount(price, username);
		
	}
```





## 事务的传播行为

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播

例如：方法可能继续在现有事务中运行，也可能开启一个新的事务，并在自己的事务中运行

```java
//添加事务注解
	//使用propogation指定事务的传播行为，即当前的事务方法被另外一个事务方法调用时
	//如何使用事务，默认取值为REQUIRED，即使用调用方法的事务
	@Transactional(propagation=Propagation.REQUIRES_NEW)
	@Override
	public void purchase(String username, String isbn) {
		System.out.println("*****this is purchase(String username, String isbn)");
		//获取书的单价
		int price =bookShopDao.findBookPriceByIsbn(isbn);
		//更新书的库存
		bookShopDao.updateBookStock(isbn);
		//更新用户的余额
		bookShopDao.updateUserAccount(price, username);
		
	}
```

默认传播行为为：**REQUIRES**

即：余额为110的话，1001书的价格为100，1002书的价格为70

若使用REQUIRES的话，会一本都买不了

当使用REQUIRES_NEW是，会购买第一本，然后此时余额不足以购买第二本而报错，



## 事务的隔离级别

```java
//添加事务注解
	//1：使用propogation指定事务的传播行为，即当前的事务方法被另外一个事务方法调用时
	//如何使用事务，默认取值为REQUIRED，即使用调用方法的事务
	//2：使用Isolation指定事务的隔离级别，最常用的是READ_COMMITTED（读已提交）
	//3：默认情况下，Spring对所有的运行时异常进行回滚，也可以通过指定属性来设置对某些异常不进行回滚，通常情况下默认值即可
	//4：使用readOnly指定事务是否为只读，表示这个事务只读数据但不进行更新数据，这样可以帮助数据库引擎优化事务，若真的是一个只读数据的事务，应当设置readOnly=true
	//5：timeout可以指定事务强制回滚之前占用的时间
	@Transactional(propagation=Propagation.REQUIRES_NEW
			,isolation=Isolation.READ_COMMITTED
			,noRollbackFor= {UserAccountException.class}
			,readOnly=false
			,timeout=3)
	@Override
	public void purchase(String username, String isbn) {
		
		try {
			Thread.sleep(3000);
		} catch (InterruptedException e) {}
		
		System.out.println("*****this is purchase(String username, String isbn)");
		//获取书的单价
		int price =bookShopDao.findBookPriceByIsbn(isbn);
		//更新书的库存
		bookShopDao.updateBookStock(isbn);
		//更新用户的余额
		bookShopDao.updateUserAccount(price, username);
		
	}
```



## 基于XML文件配置事务

步骤：

1. 配置事务管理器
2. 配置事务属性
3. 配置事务切面，将事务属性和事务切面关联起来

Spring配置文件新增：（记得添加aspectJweaver.jar包。否则会报错）

```java
	<bean id="bookShopDao" class="com.test.txBaseXml.BookShopDaoImpl">
		<property name="jdbcTemplate" ref="jdbcTemplate"></property>
	</bean>

	<bean id="bookShopService" class="com.test.txBaseXml.service.impl.BookShopServiceImpl">
		<property name="bookShopDao" ref="bookShopDao"></property>
	</bean>

	<bean id="cashier" class="com.test.txBaseXml.service.impl.CashierImpl">
		<property name="bookShopService" ref="bookShopService"></property>
	</bean>

	<!-- 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 配置事务属性 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 根据方法名指定事务的属性 -->
			<tx:method name="purchase" propagation="REQUIRES_NEW"/>
			<tx:method name="get*" read-only="true"/>
			<tx:method name="find*" read-only="true"/>
			<tx:method name="*"/>
		</tx:attributes>
	</tx:advice>

	<!-- 配置事务切入点，以及把事务切入点和事务属性关联起来 -->
	<aop:config>
		<aop:pointcut expression="execution(* com.test.txBaseXml.service.*.*(..))" 
			id="txPointCut"/>
			<aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
	</aop:config>
```

