---
title: Mybatis 基础
date: 2018-12-24 21:18:12
tags: [java]
category: java基础
---


# 基本使用

配置文件：`SqlMapConfig.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--配置连接 MySQl-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="happy0224"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入 sqlMapper 配置-->
    <mappers>
        <mapper resource="com/passon/mybatis/sqlmap/User.xml"/>
    </mappers>

</configuration>
```

sql map:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--放置 sql 语句-->
<mapper namespace="test">

    <select id="getAllUser" resultType="com.passon.mybatis.domain.User">
      select * from user
    </select>

    <select id="findUserById" parameterType="Integer" resultType="com.passon.mybatis.domain.User">
        select * from user where id = #{v}
    </select>

</mapper>
```

> namespace + id 组成 sql 语句调用标示

maven pom: 需要添加下面代码才会编译 java 目录下的 xml 文件

```xml
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
        </resources>
    </build>
```

test:

```java
    @Test
    public void temp() throws IOException {

        String resPath = "SqlMapConfig.xml";
        InputStream is = Resources.getResourceAsStream(resPath);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
        SqlSession sqlSession = factory.openSession();

        User user = (User) sqlSession.selectOne("test.findUserById", 10);
        System.out.println(user);

        sqlSession.commit();
        sqlSession.close();
    }
```

# 增删改查

```xml
    <select id="findUserById" parameterType="Integer" resultType="com.passon.mybatis.domain.User">
        select * from user where id = #{v}
    </select>

    <insert id="insertUser" parameterType="com.passon.mybatis.domain.User">
        <selectKey keyProperty="id" resultType="Integer" order="AFTER">
            select last_insert_id()
        </selectKey>

        insert into user (username, birthday, address, sex)
        value (#{username}, #{birthday}, #{address}, #{sex})
    </insert>

    <update id="updateUserById" parameterType="com.passon.mybatis.domain.User">
      update user
      set username=#{username}, sex=#{sex}, birthday=#{birthday}, address=#{address}
      where id=#{id}
    </update>

    <delete id="deleteUserById" parameterType="Integer">
        delete from user
        where id=#{id}
    </delete>
```

```java

    @Test
    public void insertUser() throws IOException {
        String resPath = "SqlMapConfig.xml";
        InputStream is = Resources.getResourceAsStream(resPath);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
        SqlSession sqlSession = factory.openSession();

        User user1 = new User();
        user1.setAddress("assad");
        user1.setBirthday(new Date(System.currentTimeMillis()));
        user1.setSex("男");
        user1.setUsername("lisi");

        int insert = sqlSession.insert("test.insertUser", user1);
        System.out.println(insert);

        sqlSession.commit();
        sqlSession.close();
    }

    @Test
    public void updateUserById() throws IOException {
        String resPath = "SqlMapConfig.xml";
        InputStream is = Resources.getResourceAsStream(resPath);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
        SqlSession sqlSession = factory.openSession();

        User user1 = new User();
        user1.setId(27);
        user1.setAddress("assad");
        user1.setBirthday(new Date(System.currentTimeMillis()));
        user1.setSex("男");
        user1.setUsername("liudehua");

        int udpate = sqlSession.update("test.updateUserById", user1);
        System.out.println(udpate);

        sqlSession.commit();
        sqlSession.close();
    }

    @Test
    public void deleteUserById() throws IOException {
        String resPath = "SqlMapConfig.xml";
        InputStream is = Resources.getResourceAsStream(resPath);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
        SqlSession sqlSession = factory.openSession();

        int udpate = sqlSession.delete("test.deleteUserById", 27);
        System.out.println(udpate);

        sqlSession.commit();
        sqlSession.close();
    }
```

# Mybatis 与 Hibernate 区别

Mybatis 优点：

Mybatis: 需要自己写 sql 语句。
Mybatis 自己控制 sql 语句，方便 sql 优化。

Mybatis 缺点：

Mybatis: 无法快速切换数据库，sql 语句与数据库相关，Hibernate 数据库无关可以随时切换。

# Mybatis 实现 Dao 层

动态代理，Mapper 接口类遵循四个原则:

1. 接口方法名称与 xml 中的id一致
2. 返回值类型与 xml 中一致
3. 方法的入参类型与 xml 中一致
4. 命名空间绑定此接口


# 配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <properties resource="jdbc.properties"/>
    <typeAliases>
        <package name="com.passon.mybatis.domain"/>
    </typeAliases>

    <!--配置连接 MySQl-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="happy0224"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入 sqlMapper 配置-->
    <mappers>
        <!--<mapper resource="com/passon/mybatis/sqlmap/User.xml"/>-->
        <!--<mapper class="com.passon.mybatis.mapper.UserMapper"/>-->
        <package name="com.passon.mybatis.mapper"/>
    </mappers>

</configuration>
```

# 手动映射

字段不一致处理方法 resultMap

```xml
    <resultMap id="user" type="User">
        <!--手动映射-->
        <result column="user_id" property="userId"/>
    </resultMap>

    <select id="getAllUser" resultMap="user">
      select * from user
    </select>
```

# 动态 sql

```xml
    <select id="selectUserBySexAndUsername" parameterType="User" resultType="User">
      select * from user
      where
      <if test="sex != null and sex != '' ">
          sex = #{sex}
      </if>

      <if test="username != null and username != '' ">
          and username = #{username}
      </if>

    </select>
```


sql 片段:

```xml
    <sql id="selector">
        select * from user
    </sql>
    
    <select id="findAllUser" parameterType="QueryVo" resultType="User">
        <include refid="selector"/>
    </select>
```

foreach:

```xml
    <!-- 多个 ID (1,2,3)-->
    <select id="selectUserByIds" parameterType="QueryVo" resultType="User">
        <include refid="selector"/>
        <where>
            id in
            <!--  如果参数是 List collection="list" -->
            <foreach collection="array" item="id" separator="," open="(" close=")">
                #{id}
            </foreach>
        </where>
    </select>

```

# 一对一关联

```xml
    <!--一对一映射的时候需要的属性映射都要写上-->
    <!--Orders 对象用有属性 User user-->
    <resultMap id="order" type="Orders">
        <result column="id" property="id"/>
        <result column="user_id" property="userId"/>
        <result column="number" property="number"/>
        <result column="createtime" property="createtime"/>
        <association property="user" javaType="User">
            <id column="user_id" property="id"/>
            <result column="username" property="username"/>
        </association>
    </resultMap>

    <select id="selectOrders" resultMap="order">
        select
        o.id,
        o.user_id,
        o.number,
        o.createtime,
        u.username
        from orders o
        left join user u
        on o.user_id = u.id
    </select>
```

# 一对多查询

```xml
    <!--一对多映射-->
    <resultMap id="user" type="User">
        <id column="user_id" property="id"/>
        <result column="username" property="userId"/>
        <collection property="orderList" ofType="Orders">
            <id column="id" property="id"/>
            <result column="number" property="number"/>
        </collection>

    </resultMap>

    <select id="selectUserList" resultMap="user">
        select
        o.id,
        o.user_id,
        o.number,
        o.createtime,
        u.username
        from user u
        left join order o
        on o.user_id = u.id
    </select>
```

# Mybatis 整合 Spring

关键配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <aop:aspectj-autoproxy proxy-target-class="true"/>

    <context:component-scan base-package="com.passon"/>

    <!--读取配置文件 db.properties-->
    <context:property-placeholder location="classpath:db.properties"/>

    <!--数据库连接池  配置 c3p0 -->
    <bean name="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"/>
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="user" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--Mybatis 工厂-->
    <bean name="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:SqlMapConfig.xml"/>
    </bean>

    <bean name="userDao" class="com.passon.sm.dao.impl.UserDaoImpl">
        <property name="sqlSessionFactory" ref="sqlSessionFactoryBean"/>
    </bean>

    <!--Mapper 动态代理 普通配置-->
    <!--<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">-->
    <!--<property name="sqlSessionFactory" ref="sqlSessionFactoryBean"/>-->
    <!--<property name="mapperInterface" value="com.passon.sm.mapper.UserMapper"/>-->
    <!--</bean>-->

    <!--Mapper 动态代理 扫描-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.passon.sm.mapper"/>
    </bean>
</beans>
```

# 注解记录

`@Transient`: 标注在成员变量上，表示这个成员变量不做数据库映射。











