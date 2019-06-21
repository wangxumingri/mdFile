# spring

[TOC]

##基础

IOC容器：

​	在`org.springframework.beans`和`org.springframework.context`包是Spring框架的IoC容器的基础。 

`bean`

> 在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。bean是一个由Spring IoC容器实例化，组装和管理的对象。否则，bean只是应用程序中许多对象之一。
>
> 这些bean是使用您提供给容器的配置元数据创建的。依赖关系也在元数据中
>
> 在容器本身内，这些bean定义表示为`BeanDefinition` 对象 

创建方式：

​	构造函数

​	实例工厂:使用实例[工厂方法进行](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/core.html#beans-factory-class-static-factory-method)实例化会从容器调用现有bean的非静态方法来创建新bean。 

​	静态工厂

DI：

​	构造函数

​	setter



***

`Beanfactory`

提供了配置框架和基本功能 

`org.springframework.context.ApplicationContext`

> 该接口是 `Beanfactory` 的超集，继承了`Beanfactory`
>
> 该接口代表  **`Spring IoC容器，负责实例化，配置和组装bean`**。
>
> 容器通过读取配置元数据获取有关要实例化，配置和组装的对象的指令。
>
> 配置元数据以XML，Java注释或Java代码表示。它允许您表达组成应用程序的对象以及这些对象之间丰富的相互依赖性。 （**三者可以结合使用**）
>
> 实现类：
>
> ​	`ClassPathXmlApplicationContext` : 加载类路径下的 xml配置文件
>
> ​	`FileSystemXmlApplicationContext`：加载文件系统中的 xml配置文件
>
> ​	`AnnotationConfigApplicationContext` :基于注解

它`ApplicationContext`是高级工厂的接口，能够维护不同bean及其依赖项的注册表。通过使用该方法`T getBean(String name, Class<T> requiredType)`，您可以检索Bean的实例。 



导入其他xml配置文件：

所有位置路径都与执行导入的定义文件相关，因此`services.xml`必须与执行导入的文件位于相同的目录或类路径位置， `messageSource.xml`而且`themeSource.xml`必须位于`resources`导入文件位置下方的位置。如您所见，忽略前导斜杠。但是，鉴于这些路径是相对的，最好不要使用斜杠。 

***

### 原理：





***

### 常用注解：



`@ContextConfiguration`

​	作用：  `Spring整合JUnit4测试时`，使用注解引入 xml 配置文件，或java 配置类

​	位置：类

​	属性：

​		`String[] value` : 和  `locations` 可互换

​		`String[] locations` ： 加载xml文件

​		`Class<?>[] classes`： 加载配置类的字节码

***

##spring整合其他技术

###spring Junit:

**环境搭建：**

**1.导入maven坐标:**

```xml
<!--junit单元测试-->
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
<!--spring 整合 测试的 jar-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${spring.version}</version>
</dependency>
```

**2.配置文件**

​	**`注解方式：`**

​		1.配置   `包扫描`：

​			` <context:component-scan base-package="com.wxss"></context:component-scan> `

​		2.在类上使用注解： 如    ` @Compont`

​	**`xml方式：`**			

​		1. 用    `bean` 标签单独创建bean对象

​			  `<bean id="goodsService" class="service.impl.GoodsServiceImpl"/>`

**3.测试类：**

在测试类上使用注解：`@RunWith(SpringJUnit4ClassRunner.class)`

```JAVA
package spring;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import service.GoodsServiceI;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring/applicationContext.xml")
public class SpringTest {
	// 1.使用注解加载配置文件开始，需配置包扫描
    @Autowired
    GoodsServiceI goodsServiceI;
	
    @Test
    public void testByZhuJie(){
        goodsServiceI.test();
    }

    // 2.使用java代码加载配置文件，可注释上方的@ContextConfiguration注解
    @Test
    public void testByCode(){
        ApplicationContext applicationContext = new 
                         ClassPathXmlApplicationContext("spring/applicationContext.xml");
       GoodsServiceI goodsService = applicationContext.getBean("goodsService1",                                                                               GoodsServiceI.class);
       goodsService.test();
    }
}

```

`tips:`

```
1.@RunnerWith 注解的作用：
2.方式2硬编码较多，使用较少，推荐方式1，通过注解加载配置文件
3.方式2还可以使用 【@Before】 注解和 】@After】注解来 初始化 ApplicationContext 和 释放资源
4.@ContextConfiguration注解只能在测试中使用：可以加载java配置类，或xml配置文件
```

###spring task:

**定时频率设置：**

​	1.Cron表达式：

​	2.fixedRate

​	3.fixedDelay

**环境搭建：**

**1.maven:**

`只需引入spring相关包，无需额外引入其他。`

**2.配置文件：**

​	**注解方式：**

​		1.在配置文件中开启注解支持即可：

​			`<task:annotation-driven/>`

​		2.定时任务所在类需要注册为      ` bean`  ; 在方法上使用   `@Schedule` 注解即可

​		`tips:` 

​			**方法所在类，不是bean，定时任务不会执行**

​			**将开启定时任务注解支持放在spring的主配置中，好像会出错**

>java.lang.IllegalStateException: BeanFactory not initialized or already closed - call 'refresh' before accessing beans via the ApplicationContext



​	**xml方式：**

​		**1.创建applicationContext-task.xml的配置文件:**

​		**2.web.xml中加载applicationContext-task.xml配置文件**

​			（**单元测试**可以使用   `@ContextConfiguration`注解）

**applicationContext-task.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.2.xsd">
    
    <!--1.进行定时任务的类，将其定义为一个bean-->
    <bean id="springTaskDemoByXML" class="com.wxss.task.SpringTaskDemoByXML"/>
    <!--2.通过task标签，定义定时功能-->
    <task:scheduled-tasks>
        <task:scheduled ref="springTaskDemoByXML" method="printTime" cron="0/10 * * * * ?" />
    </task:scheduled-tasks>
</beans>
```

**web.xml加载配置文件**

```xml
<!-- 监听器加载spring容器 -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<!--<param-value>classpath*:spring/applicationContext.xml</param-value>-->
	<param-value>
     	classpath*:spring/applicationContext-task1.xml
    </param-value>
</context-param>
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```



**2.示例：**



***

###spring data redis:

***

###spring data jpa :

环境搭建：

​	1.maven

​	2.配置文件

​	3.实体类映射表

​	4.接口继承接口

**配置文件:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/data/jpa
		http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

    <!-- 6. 配置包扫描-->
    <context:component-scan base-package="com.wxss">
        <!--忽略@Controller-->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    <!--spring 和 spring data jpa的配置-->

    <!-- 1.创建entityManagerFactory对象交给spring容器管理-->
    <bean id="entityManagerFactoty" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--配置的扫描的包（实体类所在的包） -->
        <property name="packagesToScan" value="com.wxss.pojo"/>
        <!-- jpa的实现厂家 -->
        <property name="persistenceProvider">
            <bean class="org.hibernate.jpa.HibernatePersistenceProvider"/>
        </property>

        <!--jpa的供应商适配器 -->
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
                <!--配置是否自动创建数据库表 -->
                <property name="generateDdl" value="false"/>
                <!--指定数据库类型 -->
                <property name="database" value="MYSQL"/>
                <!--数据库方言：支持的特有语法 -->
                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect"/>
                <!--是否显示sql -->
                <property name="showSql" value="true"/>
            </bean>
        </property>
        <!--jpa的方言 ：高级的特性 -->
        <property name="jpaDialect">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect"/>
        </property>
    </bean>

    <!--2.创建数据库连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
        <!--<property name="jdbcUrl" value="jdbc:mysql://192.168.171.30:3306/task_demo"></property>-->
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring_boot_demo"></property>
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    </bean>

    <!--3.整合spring dataJpa-->
    <jpa:repositories base-package="com.wxss.dao" transaction-manager-ref="transactionManager"
                      entity-manager-factory-ref="entityManagerFactoty"></jpa:repositories>

    <!--4.配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactoty"></property>
    </bean>

    <!-- 4.AOP事务-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="save*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!-- 5.aop-->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.wxss.service.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut"/>
    </aop:config>


</beans>
```



###spring jms:



###spring jdbcTemplate





##jar包相关问题

​	`aspectjweaver.jar` ：AspectJ提供的第三方与AOP相关的jar，可以使用@Aspect注解

```
在使用AOP时:
	1）如果使用xml方式，不需要任何额外的jar包。 
	2）如果使用@Aspect方式，你就可以在类上直接一个@Aspect就搞定，不用费事在xml里配了。但是这需要额外 
	   的jar包（ aspectjweaver.jar）。 
tips:
	无论是使用spring aop还是 aspectj都需要aspectjweaver.jar spring-aop.jar这两个jar包。 

```



