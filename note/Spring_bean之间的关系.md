## Spring_bean之间的关系

### 继承

不同于java之中的继承

```java
//一般写法
    <bean id="address" class="Relation.Address"
        p:city="BeiJing" p:street="WuDaoKou"></bean>

    <bean id="address2" class="Relation.Address"
        p:city="BeiJing" p:street="DaZhongSi"></bean>
            
//使用继承
    <bean id="address" class="Relation.Address"
        p:city="BeiJing" p:street="WuDaoKou"></bean>        
    <!--bean配置的继承；使用bean的parent属性指定继承那个bean的配置 -->
    <!--不是所有属性都会被继承，例如abstract和autpwire-->
    <bean id="address2" p:street="DaZhongSi" parent="address"></bean>
            
```

### 抽象bean

```java
    <!--使用abstract=“true“将该bean设置成模板，无法被IOC容器实例化，只能被继承-->
    <!--若一个bean的class属性没有被指定，那么该bean必须是一个抽象bean，抽象bean的class可以不指定（可以忽略class属性）-->
    <bean id="address" class="Relation.Address"
          p:city="BeiJing" p:street="WuDaoKou" abstract="true"></bean>
```



### 依赖

Spring允许用户通过depend-on属性设定前置依赖的bean，若需要前置依赖的多个bean，可以通过逗号进行连接

```java
    <bean id="car" class="AutoWire.Car"
        p:brand="Audi" p:price="40000"></bean>

    <!--若person的属性car必须要有，即：person这个bean依赖于Car这个bean-->
    <bean id="person" class="Relation.Person"
        p:name="Hally" p:address-ref="address2" depends-on="car"></bean>
```



