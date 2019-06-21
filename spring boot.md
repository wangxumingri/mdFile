# spring boot

[TOC]



## 简介：





### 配置文件：

spring boot 支持两种格式的配置文件：

  1. ***.properties  （key=value格式）

		2. ***.yml（Json格式）

     ```
     二者优先级：前者优先级更高，以.properties配置为准。（会先加载后者，再加载前者）
     使用@PropertySource注释无法加载YAML文件
     ```

     

     也可以通过创建属性类来 设置 配置文件中的属性

     

      

     **$\color{red}{.yml(.yaml)格式语法}$**

     ​	

     ==撒大声地==





### 常用注解：

 1. ```@SpringBootApplication```

    ```一般配置在spring boot项目的启动类上
    功能：
    	1.启用spring boot的自动配置机制 					（@EnableAutoConfiguration）
    	2.自动扫描，获取其他spring组件（所在包及其子包）	（@ComponentScan）
    	2.允许在上下文中注册额外的bean或导入其他配置类		（@Configuration）
    属性：
    位置：主类
    备注：
    	1.该注解相当于使用上述三个注解的默认属性。
    ```

    2. ```@Configuration```

    ```
    功能：允许在上下文中注册额外的bean或导入其他配置类
    属性：
    位置：类
    备注：
    	1.被该注解修饰的类，会被声明为一个【配置类】
    	2.该类仍可当做普通类使用
    ```

    

    3. ```@EnableAutoConfiguration```

    ```
    功能：根据您添加的jar依赖项自动配置Spring应用程序
    属性：
    位置：
    备注：
    ```

    4. ```@EnableTransactionManagement```

    ```xml
    功能：启动spring boot的自动注解事务管理
    属性：
    位置：启动类，或其他配置类上
    备注：
    	1.等同于xml配置方式的: <tx:annotation-driven />
    	2.被修饰的配置类需要实现 【TransactionManagementConfigurer】 事务管理器配置接口。
    ```

    

    4. ```@ComponentScan```

    ```
     功能：自动获取并装配指定包下的所有Spring组件（带Spring注解的类） 
     属性：
     	String[] value/basePackages; 要扫描的包
     	String[] basePackageClasses ; 配置类的字节码文件
     	Filter[] includeFilters：过滤出要加入spring容器的类
     	Filter[] excludeFilters：过滤出不用加入spring容器的类
     位置：类
     备注：
     	1.没有指定值时，默认扫描应用程序（被修饰类）所在的包及其子包...
     	2.在spring boot 的启动类上使用该注解，默认会覆盖@SpringBootApplication注解的包扫描作用
    ```

`tips:`

​	可替代配置文件中的：

```xml
<!--扫描com.wxss下所有的包-->
<context:component-scan base-package="com.wxss">
        <!--忽略@Controller-->
        <context:exclude-filter type="annotation" 		  
                                expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

><https://blog.csdn.net/neulily2005/article/details/83750027> 



1. 

2. `@Import`

   ```注释可以用于导入额外的配置类 
   功能：@Import注释可以用于导入额外的配置类
   属性：
   位置：配置类上
   备注：暂无
   ```

3. ```@EntityScan```

```
<context:property-placeholder location="classpath*:properties/*.properties"/>
```



$\color{red}{tips：}$

```
加载配置的几种方式：
    @Import可以导入额外的配置类
    @CompenScan可以自动获取指定包内的所有spring 组件
    @ImportResource用来加载xml配置文件
```

```html
spring boot 自动配置：
	1.Spring Boot自动配置尝试根据您添加的jar依赖项自动配置Spring应用程序。
	2.【@SpringBootApplication】或【@EnableAutoConfiguration】 注解都可以自动配置spring应用程序。
      通常建议您仅将或另一个添加到 【主@Configuration类】
	3.spring boot 的自动配置是非侵入性的。可以定义自己的配置以替换自动配置的特定部分
禁用特定的自动配置类:
	1.使用【@EnableAutoConfiguration】的【exclude】属性禁用它们
	2.如果类不在类路径上，则可以使用【excludeName】的属性并指定【完全限定名称】
	3.可以在注释级别和使用属性定义排除项。
```

```java
import org.springframework.boot.autoconfigure。*;
import org.springframework.boot.autoconfigure.jdbc。*;
import org.springframework.context.annotation。*;

@Configuration 
@EnableAutoConfiguration（exclude = {DataSourceAutoConfiguration.class}）
 public  class MyConfiguration {
}
```

### 运行程序

常用运行方式：

      ```
1.IDE运行
2.使用maven插件：
	mvn spring-boot : run
	export MAVEN_OPTS = -Xmx1024m [可选项:配置系统环境变量]
3.Gradle插件
      ```



### 热部署



### 自定义SpringApplication

tips：

```
如果需要在启动后，运行特定代码,则可以实现ApplicationRunner 或 CommandLineRunner 接口
两个接口以相同的方式工作并提供单个run方法，该方法在SpringApplication.run(…)完成之前调用 。

所述CommandLineRunner接口提供访问的应用程序的参数作为一个简单的字符串数组，而ApplicationRunner使用了ApplicationArguments前面所讨论的接口。以下示例显示了CommandLineRunner一个run方法：
如果定义了必须按特定顺序调用的多个CommandLineRunner或ApplicationRunnerbean，则可以另外实现 org.springframework.core.Ordered接口或使用 org.springframework.core.annotation.Order注释
```

```java
import org.springframework.boot.*;
import org.springframework.stereotype.*;

@Component
public class MyBean implements CommandLineRunner {

	public void run(String... args) {
		// Do something...
	}
}
```

***
### 部署

>`spring-boot-starter-web` 包含了 Spring MVC 的相关依赖（包括 Json 支持的 Jackson 和数据校验的 Hibernate Validator）和一个内置的 Tomcat 容器，这使得在开发阶段可以直接通过 `main`方法或是`JAR` 包独立运行一个 `WEB` 项目。而在部署阶段也可以打成 WAR 包放到生产环境运行。
>
>在拥有 `@SpringBootApplication` 注解的类中，使用 `SpringApplication` 的 `run` 方法可以通过`JAR`启动项目。
>
>继承 `SpringBootServletInitializer` 类并实现 `configure` 方法，使用 `application` 的 `sources` 方法可以通过`WAR`启动项目：

```JAVA
@SpringBootApplication
public class Application extends SpringBootServletInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Application.class, args);
    }
}
```



## 整合其他技术：

### spring boot 整合 mybatis

​	

```
纯注解方式:
	1.在application.yml中配置 mybatis配置别名
	2.在启动类上使用@MapperScan注解 或 在每个dao接口上使用@Mapper注解
	3.在dao接口中使用mybatis提供的crud相关的注解
tips：
	springboot会自动加载spring.datasource.*相关配置，数据源就会自动注入sqlSessionFactory	 中，sqlSessionFactory会自动注入到Mapper中。
```



```properties
xml方式：
	1.application.properties(或.yml)：
		配置mybatis全局mybatis-config.xml配置文件的位置
		配置dao层XXXMapper.xml文件的位置
		配置实体类的别名：实体类所在包			(也可在全局配置文件中配置)
		mybatis.config-locations=classpath:mybatis/mybatis-config.xml
		mybatis.mapper-locations=classpath:mybatis/mapper/*.xml
		mybatis.type-aliases-package: com.wxss.shop.pojo
tips：
	将sql语句写到实体类映射文件中
```



```html
java 配置类方式+xml配置文件方式：
	主要就是使用 【@Configuration】注解创建一些配置类，在配置类中完成对mybatis的整合 
```

**tips：**

```
    mapper接口 			    service层 				     启动类				

接口@Mapper注解      +   service层@Resource进行DI    +          无               
接口@Repository注解  +   service层@Autowired进行DI   +     启动类：@MapperScan
接口无               +   service层@Resource进行DI    +     启动类@MapperScan
接口无               +   service层@Autowired进行DI    +     启动类@MapperScan
接口无               +   service层@Resource进行DI    +     启动类@MapperScan
接口@Repository注解  +    随意					+  没扫描 就会报错
		----------------------------------------------------------------	
			1.不能只用@Repository注解，否则在service层注入的时候会失败
			2.通过@Resource注解DI时，会爆红，不用管
			3.方式4会提示无法自动注入，不用管
		----------------------------------------------------------------
Tips:
	@Mapper接口是mybatis提供的，所以在通过spring的@Auowired注解注入时，会提示无法注入
	@Resource注解是JDK提供的，默认根据名称注入（字段名或setter方法的属性名，），
	   找不到名称，才根据类型注入
	   如果设置了name属性，只会根据name注入
	   @Resource装配顺序
　　1. 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常
　　2. 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常
　　3. 如果指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常
　　4. 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配；

```





**对比：**

> 两种模式各有特点:
>
> ​	1.注解版适合简单快速的模式，其实像现在流行的这种微服务模式，一个微服务就会对应一个
>
> ​	自已的数据库，多表连接查询的需求会大大的降低，会越来越适合这种模式。
>
> ​	2.老传统模式比适合大型项目，可以灵活的动态生成SQL，方便调整SQL，也有痛痛快快，洋洋	  			   
>
> ​	洒洒的写SQL的感觉。
>
> **当然二者可以根据需求结合使用**
>
> 

***

### spring boot 整合 JPA

**环境搭建:**

>1. jar包:   spring boot 一些基本的包 还有 下方的整合JPA的包和数据库驱动的包
>2. 修改application.properties(或.yml)配置文件
>3. dao层接口继承JPA的两个接口
>4. 实体类映射：完成实体类和数据库中的表的映射



**maven部分依赖：**

```xml
<!--spring boot 整合 jpa-->
<dependency>    
    <groupId>org.springframework.boot</groupId>    
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<!--数据库驱动-->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.37</version>
	<scope>runtime</scope>
</dependency>
<!--数据库源：可选-->
```

**application.properties配置文件：**

```properties

# 配置jpa：
# 常用配置：
# 数据库类型
spring.jpa.database=mysql 
# 
spring.jpa.hibernate.ddl-auto=update
#
spring.jpa.show-sql=true
#
spring.jackson.serialization.indent_output=true
```

**用户dao接口继承spring data  JPA 提供的两个接口，根据需要继承序列化接口：**

```java
package com.wxss.transactiondemo.dao;

import com.wxss.transactiondemo.pojo.Goods;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;

/**
 * 不需@Repository注解或@Component注解，也不需要在启动类配置包扫描，将该接口注册成bean
 */
public interface GoodsDao extends JpaRepository<Goods,Integer> ,              
							   JpaSpecificationExecutor<Goods> {
    
    /*
     *  1.我们自己的接口继承spring data jpa 提供的两个接口后,
     *    可以 【直接】 使用父接口中的方法，进行SQL操作：
     		JpaRepository<T,ID> :提供简单操作
     		JpaSpecificationExecutor<T> ：提供复杂操作
     *  2.我们还可以自定义方法：（符合JPA方法命名规则）
     */
    
}
```





***

### spring boot 事务管理

​	 **数据库表引擎为`InnoDB`时才支持事务，`MyISAM`引擎不支持**

​    **默认配置下** Spring 只会回滚运行时、未检查异常（继承自 RuntimeException 的异常）或者 Error。 

```
注解方式：
	1.使用myabtis时，spring boot会自动配置一个【DataSourceTransactionManager】，只需在   		service类或方法上使用【@Transactional】注解， 无需其他配置。
	2.也可以在【启动类】上再加上【@EnableTransactionManagement注解】	
xml配置方式：
	配置AOP切面
java配置类方式：

```

```
不管是JPA还是JDBC等都实现自接口 【PlatformTransactionManager】 
	使用spring-boot-starter-jdbc依赖，框架会默认注入 DataSourceTransactionManager实例。
	使用spring-boot-starter-data-jpa 依赖，框架会默认注入 JpaTransactionManager 实例。

	【org.springframework.jdbc.datasource.DataSourceTransactionManager】
	【org.springframework.orm.jpa.JpaTransactionManager】】
可在启动类中增加下列代码,查看使用的是事务实例：
```

​	

```java
package com.wxss.transactiondemo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.transaction.PlatformTransactionManager;

@SpringBootApplication
public class TransactiondemoApplication {
    @Bean
    public Object testBean(PlatformTransactionManager platformTransactionManager){
        System.out.println(">>>>>>>>>>" +           
                           platformTransactionManager.getClass().getName());
   
        return new Object();
    }

    public static void main(String[] args) {
        SpringApplication.run(TransactiondemoApplication.class, args);
    }
}
```



```
@Transactional 注解：
作用：事务控制
属性：
位置：接口、接口方法、类以及类方法上。
备注：
	@Transactional 可以作用于接口、接口方法、类以及类方法上：
	1.当作用于类上时，该类的所有 public 方法将都具有该类型的事务属性，
	2.同时，我们也可以在方法级别使用该标注来覆盖类级别的定义。
	3.虽然 @Transactional 注解可以作用于接口、接口方法，但是 Spring 建议不要这样做。
	  因为这只有在使用【基于接口的代理（JDK）时它才会生效】。
	4.@Transactional 注解只被应用到 【public】 方法上，这是由 Spring AOP 的本质决定的。  		如果你在 【protected、private 或者默认可见性的方法】上使用 @Transactional 注解，
	  【这将被忽略，也不会抛出任何异常】。
	  
```



***



> spring boot的**自动配置机制**，会根据 **导入的jar，和applicationXXX配置文件**【spring boot的】	自动配置.
>
> 这些对我们并不透明，如果你项目做的比较大，添加的持久化依赖比较多，我们还是会选择人为的指定使用哪个事务管理器。
>
> 在Spring容器中，我们手工注解@Bean 将被优先加载，框架不会重新实例化其他的 PlatformTransactionManager 实现类。
>
> 然后在Service中，被 @Transactional 注解的方法，将支持事务。如果注解在类上，则整个类的所有方法都默认支持事务。
>
> > 1.多数据源问题
>
> > 2.对于同一个工程中存在多个事务管理器要怎么处理
> >
> > 这种方式，是不是spring boot 不会再自动配置mybatis
> >
> > ​    定义在application.yml中的 实体类别名，没有生效
> >
> > https://blog.csdn.net/qq_37598011/article/details/80669608



**application.yml配置文件：**

```yaml
# server config
server:
  port: 8088
spring:
  # json
  jackson:
    time-zone: GMT+8
    date-format: yyyy-MM-dd HH:mm:ss
  # datasource
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/spring_boot_demo?characterEncoding=utf-8
    driver-class-name: com.mysql.jdbc.Driver
    # 使用的Druid连接池
    type: com.alibaba.druid.pool.DruidDataSource
# mybatis
mybatis:
# 从src/main/resource开始
  mapper-locations: classpath:mybatis/mapper/*Mapper.xml
  # 实体类的别名
  type-aliases-package: com.wxss.demo.entity
  config-location: mybatis/mybatis-config.xml



```



**mybatis-config.xml全局配置文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
   <!--实体类别名-->
    <typeAliases>
        <package name="com.wxss.demo.entity"/>
    </typeAliases>
</configuration>
```



```java
package com.wxss.demo.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.core.env.Environment;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;

@Configuration
@EnableTransactionManagement
@PropertySource("classpath:application.yml")
@MapperScan(basePackages = "com.wxss.demo.mapper")
public class MybatisConfig {

//    @Autowired
//    private Environment env;

    @Value("${spring.datasource.driver-class-name}")
    private String driverClassName;

    @Value("${spring.datasource.url}")
    private String url;

    @Value("${spring.datasource.username}")
    private String username;

    @Value("${spring.datasource.password}")
    private String password;

    @Value("${mybatis.mapper-locations}")
    private String MapperLocations;

    @Value("${mybatis.config-location}")
    private String mybatisConfig;

    /**
     * 设置数据源
     * @return
     */
    @Bean
    public DataSource dataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driverClassName);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);

        return druidDataSource;
    }

    /**
     * 创建SqlSessionFactory
     * @param dataSource
     * @return
     * @throws Exception
     */
    @Bean
    public SqlSessionFactory mybatisMasterSqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        // 设置全局配置文件：（java config class 方式 application.yml的不起作用）
        bean.setConfigLocation(new ClassPathResource(mybatisConfig));
        // 实体类映射配置文件的位置
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(MapperLocations));

        return bean.getObject();
    }

    /**
     * 事务支持
     * @param dataSource
     * @return
     */
    @Bean
    public DataSourceTransactionManager mybatisMasterTransactionManager(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

//    @Bean
//    public SqlSessionTemplate mybatisMasterSqlSessionTemplate(SqlSessionFactory sqlSessionFactory) throws Exception {
//        return new SqlSessionTemplate(sqlSessionFactory);
//    }
}

```

### spring boot 整合 Redis







***

###spring boot 整合 freemarker



