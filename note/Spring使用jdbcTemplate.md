## Spring使用jdbcTemplate

student类

```java
public class Student {

	private String studentNo;
	private String s_userName;
	private String s_password;
	private String sex;
	private String voteState;
	public String getStudentNo() {
		return studentNo;
	}
	public void setStudentNo(String studentNo) {
		this.studentNo = studentNo;
	}
	public String getS_userName() {
		return s_userName;
	}
	public void setS_userName(String s_userName) {
		this.s_userName = s_userName;
	}
	public String getS_password() {
		return s_password;
	}
	public void setS_password(String s_password) {
		this.s_password = s_password;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public String getVoteState() {
		return voteState;
	}
	public void setVoteState(String voteState) {
		this.voteState = voteState;
	}
	@Override
	public String toString() {
		return "Student [studentNo=" + studentNo + ", s_userName=" + s_userName + ", s_password=" + s_password
				+ ", sex=" + sex + ", voteState=" + voteState + "]";
	}
	
	
}
```

Spring配置类

```java
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
```

db.properties文件

```java
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql://localhost:3306/vote2
jdbc.user=root
jdbc.password=123456

jdbc.initPoolSize=5
jdbc.maxPoolSize=10
```

测试类-->jdbcTest类

```java
class JDBCTest {

	private ApplicationContext context=null;
	private JdbcTemplate jdbcTemplate=null;
	
	{
		context=new ClassPathXmlApplicationContext("application-context1.xml");
		jdbcTemplate=(JdbcTemplate) context.getBean("jdbcTemplate");
	}
	/**
	 * 获取单个列的值，或作统计查询
	 */
	@Test
	public void testQueryForList2() {
		String sql="SELECT count(s_id) FROM t_student ";
		long count =jdbcTemplate.queryForObject(sql, long.class);
		System.out.println(count);
	}
	
	/**
	 * 查到实体类的集合
	 * 注意此处使用的是query(sql, rowMapper,5)
	 */
	@Test
	public void testQueryForList() {
		String sql="SELECT s_studentNo studentNo,s_userName,s_password,sex,voteState FROM t_student WHERE s_id<?";
		RowMapper<Student> rowMapper=new BeanPropertyRowMapper<>(Student.class);
		List<Student> student=jdbcTemplate.query(sql, rowMapper,5);
	
		System.out.println(student.toString());
	}
	
	/**
	 * 从数据库中获取一条记录，实际得到对应的一个对象
	 * 从数据库中查询到的记录是通过setter()方法赋给对应的对象的属性
	 * 需要调用queryForObject(sql, RowMapper<Student> rowMapper,Object...args)
	 * 1：其中的RowMapper指定如何去映射结果集的行，常用的实现类为 BeanPropertyRowMapper<>(Student.class)
	 * 2：使用Sql列中的别名完成列名和属性名之间的映射例如s_studentNo studentNo，这样才能给对应的对象的属性进行赋值，否则值会为null
	 * 3：JdbcTemplate不支持级联属性的映射
	 */
	@Test
	public void testQueryForObject() {
		String sql="SELECT s_studentNo studentNo,s_userName,s_password,sex,voteState FROM t_student WHERE s_id=?";
		RowMapper<Student> rowMapper=new BeanPropertyRowMapper<>(Student.class);
		Student student=jdbcTemplate.queryForObject(sql, rowMapper,1);
		System.out.println(student.toString());
	}
	
	/**
	 * 执行批量更新
	 * 最后一个参数是Object数组，修改一条记录是一个Object数组，那么修改多条记录不就是需要多个Object数组么，所以使用List<Object[]>
	 * 每一个List元素是一个Object数组
	 */
	@Test
	public void testBatchUpdate() {
		String sql="INSERT INTO t_student(s_studentNo,s_userName,s_password,sex,voteState) VALUES(?,?,?,?,?)";
		
		List<Object[]> batchArgs=new ArrayList<>();
		
		batchArgs.add(new Object[]{"2017118196","测试一","11111111","男","0"});
		batchArgs.add(new Object[]{"2017118197","测试二","22222222","女","0"});
		batchArgs.add(new Object[]{"2017118198","测试三","33333333","女","0"});
		batchArgs.add(new Object[]{"2017118199","测试四","44444444","男","0"});
		
		jdbcTemplate.batchUpdate(sql,batchArgs);
	}
	
	/**
	 * 执行INSERT DELETE UPDATE
	 * @throws SQLException
	 */
	@Test
	public void testUpdate() {
		String sql="UPDATE t_student SET s_userName=? WHERE s_studentNo=?";
		jdbcTemplate.update(sql,"JACK",2017118129);
	}
	
	@Test
	void testDataSource() throws SQLException {
		DataSource dataSource=context.getBean(DataSource.class);
		System.out.println(dataSource.getConnection());	
	}
}
```

## 使用NamedParameterJdbcTemplate

```java
	/**
	 * 使用具名参数，可以使用update(sql, sqlParameterSource)方法进行更新操作
	 * 	sql语句中的参数名（ VALUES(:s_studentNo,:s_userName,:s_password,:sex,:voteState）和类的属性一致
	 * 	使用SqlParameterSource中的 BeanPropertySqlParameterSource实现类作为参数
	 * 
	 */
	@Test
	public void testNameParameterJdbcTemplate2() {
		String sql="INSERT INTO t_student(s_studentNo,s_userName,s_password,sex,voteState) VALUES(:s_studentNo,:s_userName,:s_password,:sex,:voteState)";
		System.out.println("3333333333333333333333");
		Student student=new Student();
		student.setS_studentNo("2017118161");
		student.setS_userName("ROSE");
		student.setS_password("666666666");
		student.setSex("男");
		student.setVoteState("0");
		
		SqlParameterSource sqlParameterSource=new BeanPropertySqlParameterSource(student);
		
		namedParameterJdbcTemplate.update(sql, sqlParameterSource);
	}
	
	/**
	 * 	好处：若是有多个参数，则不用再去对应位置进行修改，可以直接根据map的键名进行更改，便于维护
	 * 	缺点：较为麻烦
	 */
	@Test
	public void testNamedParameterJdbcTemplate() {
		String sql="INSERT INTO t_student(s_studentNo,s_userName,s_password,sex,voteState) VALUES(:no,:name,:password,:sex,:state)";

		Map<String, Object> paramMap=new HashMap<String, Object>();
		paramMap.put("no", "2017118180");
		paramMap.put("name", "帅哥");
		paramMap.put("password", "88888888");
		paramMap.put("sex", "男");
		paramMap.put("state", "1");
	
		namedParameterJdbcTemplate.update(sql, paramMap);
		
	}
```

