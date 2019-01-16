---
title: Spring 基础
date: 2018-12-19 21:18:12
tags: [java]
category: java基础
---

Spring 管理项目中的所有对象。

Spring 一站式框架: Spring 是一个对象容器，容器中有什么对象就有什么功能。

Spring 核心思想：控制反转 IoC 和 面向切面 AOP。

## 简单使用

创建一个对象

```java
public class User {
```

配置注册对象到容器: (任意位置，通常放在 src 下，配置文件名任意(推荐 applicationContext.xml))

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--将 User 对象交给 Spring 容器管理-->
    <bean name="user" class="com.passon.springdemo.demo01.User">

    </bean>

</beans>
```

测试:

```java
    @Test
    public void test01() {
        //创建容器对象
        ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        //向容器中加入对象
        User user = (User) ac.getBean("user");
        //使用对象
        System.out.println(user);
    }
```

# IoC, DI

IoC: Inverse Of Control, 反转控制

将对象创建的方式反转，对象由 Spring 创建后给程序。

DI: Dependency Injection 依赖注入，实现 IoC 需要 DI 支持。

注入方式：

1. `set` 方法
2. 构造方法注入
3. 字段注入

注入类型：

1. 值类型注入：8大基本数据类型注入
2. 引用类型注入：依赖对象注入

# 配置详解

`applicationContext.xml`

## Bean

```xml
<bean name="user" class="com.passon.springdemo.demo01.User"></bean>
```

三种创建对象的方式：

方式一：

```xml
<!-- 空参数构造函数 -->
<bean name="user" class="com.passon.springdemo.demo01.User"></bean>
```

方式二：静态工厂创建

```java
public class UserFactory {

    public static User createUser() {
        return new User();
    }
}
```

```xml
<bean name="user2" class="com.passon.springdemo.demo01.UserFactory" factory-method="createUser"></bean>
```

方式三：实例工厂创建

```java
public class UserFactory {
    public User createUser2() {
        return new User();
    }
}

```

```xml
<bean name="userFactory" class="com.passon.springdemo.demo01.UserFactory"/>
<bean name="user3" class="com.passon.springdemo.demo01.UserFactory" factory-bean="userFactory" factory-method="createUser2"/>
```

## scope

scope: prototype(多例), singleton(默认值, 单例)

## 对象生命周期设置 初始化，销毁

初始化与销毁:

```xml
<bean name="user" class="com.passon.springdemo.demo01.User" init-method="init" destroy-method="destroy"/>
```

出发销毁代码：

```
// 容器关闭的时候，对象销毁
ac.close();
```

## 分模块配置

```xml
<import resource="applicationContext2.xml"/>
```

# Spring 属性注入

1. set 方法注入

```xml
    <bean name="user" class="com.passon.springdemo.demo01.User">
        <property name="name" value="tom"/>
        <property name="age" value="12"/>

        <!-- 注入对象 -->
        <property name="car" ref="cat"/>
    </bean>
```

2. 构造函数注入

```xml
<bean name="user" class="com.passon.springdemo.demo01.User">
    <constructor-arg name="name" value="jerry" index="0" type="java.lang.String"/>
    <constructor-arg name="age" value="212"/>
</bean>
```

3. p名称空间注入
4. spel注入


list, map, properties 复杂类型注入方式

```xml
<!-- list, array -->
        <property name="names">
            <array>
                <value>one</value>
                <value>two</value>
                <ref bean="user2"/>
            </array>
        </property>

        <!-- map -->
        <property name="mapone">
            <map>
                <entry key="url" value="http"/>
            </map>
        </property>

        <!-- properties -->
        <property name="prop">
            <props>
                <prop key="key">value</prop>
            </props>
        </property>
```

## 使用注解代替配置

配置设置开启注解扫描：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!-- 设置要扫描的包 -->
    <context:component-scan base-package="com.passon"/>

</beans>
```

在类中使用注解。

```java
/**
 * <bean name="user" class="com.passon.springdemo.demo01.User" />
 */
@Component("user")
public class User {
```

其他注解：

```java
@Component("user")

// 下面三各注解和 Component 实际作用一样，只是字面意义不一样
@Service("user") // service 层
@Controller("user") // web 层
@Repository("user") // dao 层
```

作用范围注解：

```
@Scope(scopeName = "singleton") // prototype
```

属性注入注解：

```
// 注入基本类型
@Value(value = "fang")
private String name;

// or

@Value(value = "fang")
public void setName(String name) {
    this.name = name;
}

// 注入引用类型
// 方式一：自动装配，自动从容器中找对象, 
@Autowired
private Car car;
// 方式二：指定对象名称
@Autowired
@Qualifier(value = "car2")
private Car çar;
// 方式三：有对象冲突的时候使用，(常用)
@Resource(name = "car2")
private String age;


```

>区别：一个使用反射赋值，一个使用 setter 赋值

初始化/销毁注解：

```java
    @PostConstruct
    public void init(){
        
    }
    
    @PreDestroy
    public void destroy(){
        
    }
```

# Spring 与 junit 整合测试

导入 `spring-test` 包

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(value = "classpath:applicationContext.xml") //指定容器创建时使用的配置文件
public class TestClient {
    
    @Resource(name = "user")
    private User user;

}
```

# AOP

Spring AOP: 通过动态代理(优先) 和 cglib 代理实现的

>Proxy 动态代理必须通过接口实现，cglib 代理通过继承实现代理

Spring 可以为容器中的对象生成动态代理对象。

cglib 例子：

```java
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(User.class);
        enhancer.setCallback(new MethodInterceptor() {

            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                //。。。

                //调用原有方法
                Object retValue = methodProxy.invoke(o, objects);

                //...
                return retValue;
            }
        });
        User userProxy = (User) enhancer.create();
        userProxy.init();
```

## AOP 名词

1. Joinpoint: 连接点, 目标对象中所有可以增强的方法
2. Pointcut: 切入点，已经赠强的方法
3. Advice: 通知/增强，增强的代码
4. Target: 目标对象，被代理的对象
5. Weaving: 织入，将通知织入到切点的过程
6. Proxy: 将通知织入到切点后生成的对象
7. aspect: 切面，切入点+通知

>需要导入第三方包：`aopalliance, aspectj.weaver`


## 使用

五类通知：

```java
public class MyAdvice {
    // 前置通知 -> 目标方法运行之前
    public void before() {
    }

    // 后置通知 (如果出现异常就不会调用)
    public void afterReturning() {
    }

    // 环绕通知, 自己调用目标方法
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        Object proceed = pjp.proceed(); // 调用目标方法
        return proceed;
    }

    // 异常拦截通知
    public void afterException() {
    }

    // 后置通知（无论是否出现异常都会调用）
    public void after() {
    }

}
```

配置：配置目标对象，配置通知对象，配置将通知织入目标对象

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

    <bean name="user" class="com.passon.springdemo.demo01.User"/>
    <bean name="myAdvice" class="com.passon.springdemo.aspectdemo.MyAdvice"/>
    <aop:config>
        <!--
        配置 pointcut
        public void com.passon.springdemo.demo01.User.init()
        void com.passon.springdemo.demo01.User.init() // 不要求public
        * com.passon.springdemo.demo01.User.init() // 指定方法
        * com.passon.springdemo.demo01.User.*() // 空参数
        * com.passon.springdemo.demo01.User.*(..) // 任意参数
        * com.passon.springdemo..*User.*(..) // 子包，命名匹配
        -->
        <aop:pointcut id="pc" expression="execution(public void com.passon.springdemo.demo01.User.init(..))"/>

        <aop:aspect ref="myAdvice">
            <!--关联 pointcut 和 Advice -->
            <aop:before method="before" pointcut-ref="pc"/>
        </aop:aspect>
    </aop:config>
</beans>
```

## AOP 注解配置

配置中开启注解 AOP:

```xml
<aop:aspectj-autoproxy/>
```

方式一：

```java
@Aspect //表示类是通知类 Advice
public class MyAdvice {
    @Before("execution(* com.passon.springdemo.demo01.User.init(..))")
    public void before() {

    }
}
```

方式二: 

```java
@Aspect //表示类是通知类 Advice
public class MyAdvice {
    
    @Pointcut("execution(* com.passon.springdemo.demo01.User.init(..))")
    public void pc(){}

    // 前置通知 -> 目标方法运行之前
    @Before("MyAdvice.pc()")
    public void before() {
    }
}
```

# 整合 JDBC

Spring 中提供了一个可以操作数据库的对象，对象封装了 jdbc 技术

需要导入：`mysql-connector-java`, `commmons-dbcp2`, `commons-pools2`

```java
    @Test
    public void test01() {
        DataSource dataSource = DBCPUtils.getDataSource();
        
        JdbcTemplate jt = new JdbcTemplate();
        jt.setDataSource(dataSource);

        String sql = "insert into t_user values('fangmingdng', 1)";
        jt.update(sql);
    }
```

## 增删改查

```java
        // save
        String saveSql = "insert into t_user values(null, ?)";
        jt.update(saveSql, "fang");
        // delete
        String deleteSql = "delete from t_user where id=?";
        jt.update(saveSql, 1);
        // query
        String querySql = "select * from t_user where id=?";
        List<User> list = jt.query(querySql, new RowMapper<User>() {
            @Override
            public User mapRow(ResultSet resultSet, int i) throws SQLException {
                User user = new User();
                user.setAge(resultSet.getString("age"));
                user.setName(resultSet.getString("name"));
                return user;
            }
        }, 1);
        // count
        String countSql = "select count(*) from t_user where id=?";
        Integer cnt = jt.queryForObject(countSql, Integer.class);
```

## JDBC 配置

```xml
    <!--1. 连接池-->
    <bean name="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/login?useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="xxxxxxx"/>

        <property name="initialSize" value="10"/>
        <property name="minIdle" value="2"/>
        <property name="maxIdle" value="5"/>

    </bean>

    <!--2. JDBCTemplate-->
    <bean name="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--3. XXDao-->
```

使用:

```java
    @Resource(name = "jdbcTemplate")
    private JdbcTemplate jdbcTemplate;
```

## JDBCDaoSupport

```java
public class UserDaoImpl extends JdbcDaoSupport implements UserDao {

    @Override
    public void save(String id, String name) {
        JdbcTemplate jt = getJdbcTemplate();
    }
    
}
```

## 读取 properties 配置

数据库配置信息移到外部

`src/db.properties`

```
db.driverClassName=mysql://...
....
```

读取配置：

```xml
<context:property-placeholder location="classpath:db.properties"/>

<!-- <property name="driverClassName" value="${db.jdbcUrl}"/> -->
```

# AOP 事务

Spring 事务管理的代码通过 AOP 封装了。

Spring 提供接口：`DataSourceTransactionManager`

子类：`JDBCTransactioManager`, `HibernateTransactionManager`

事务管理需要配置的属性：

1. 事务隔离级别
2. 是否只读
3. 事务的传播行为

```
PROPAGATION_REQUIRED: (默认)，支持当前事务
PROPAGATION_SUPPORTS
PROPAGATION_MANDATORY

PROPAGATION_REQUIRES_NEW
PROPAGATION_NOT_SUPPORTED
PROPAGATION_NEVER
PROPAGATION_NESTED
```

## 加事务

配置事务

```xml
    <!--事务对象配置-->
    <bean name="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--事务模版-->
    <bean name="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
        <property name="transactionManager" ref="transactionManager"/>
    </bean>
```


方式一：编码式 (通常不使用)

```java
    @Resource(name = "transactionTemplate")
    private TransactionTemplate tt;

    public void test03() {
        tt.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                //sql
            }
        });
    }
```

方式二：xml 配置(aop)

Spring 内置了事务通知(Advice)

配置事务

```xml
    <!--配置事务通知 Advice-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="save*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
            <tx:method name="persist*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
            <tx:method name="update*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
            <tx:method name="modify*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
            <tx:method name="delete*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
            <tx:method name="remove*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
            <tx:method name="get*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true"/>
            <tx:method name="find*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!--配置织入-->
    <aop:config>
        <aop:pointcut id="txPc" expression="execution(* com.passon.springdemo.jdbc.dao.UserDaoImpl.save(..)"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPc"/>
    </aop:config>
```

方式三：注解配置(常用)

```xml
<!--开启使用注解管理事务-->
<tx:annotation-driven/>
```

```java
@Transactional(isolation = Isolation.REPEATABLE_READ, propagation = Propagation.REQUIRED, readOnly = false)

// 可以修饰类，也可以修饰方法
// 方法中设置可以覆盖类的设置
```

# SSH 整合

Spring + Struts2 + Hibernate

Struts2: 将 Action 交给 Spring 容器管理
HibernateSessionFactory 交给 Spring 维护












