## SpEl

Spring的表达式语言

### 引用Bean，属性，方法

```java
 		<bean id="address" class="Spel.Address">
            <!--使用spel属性赋一个字面值-->
            <property name="city" value="#{'BeiJing'}"></property>
            <property name="street" value="WuDaoKou"></property>
        </bean>

        <bean id="car" class="Spel.Car">
            <property name="brand" value="Audi"></property>
            <property name="price" value="50000"></property>
            <!--使用T（）引用java.lang.Math的静态属性-->
            <property name="tyrePermeter" value="#{T(java.lang.Math).PI * 80}"></property>
        </bean>

        <bean id="person" class="Spel.Person">
            <!--使用SpEl来引用其他的Bean-->
            <property name="car" value="#{car}"></property>
            <!--使用SpEl来引用其他的Bean的属性-->
            <property name="city" value="#{address.city}"></property>
            <!--在SpEl中使用运算符(动态赋值)-->
            <property name="info" value="#{car.price>30000 ? '金领' : '白领'}"></property>
            <property name="name" value="Hand"></property>
        </bean>

```

