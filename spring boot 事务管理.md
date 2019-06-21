# spring boot 事务管理

​	 **数据库表引擎为`InnoDB`时才支持事务，`MyISAM`引擎不支持**

**默认配置下** Spring 只会回滚运行时、未检查异常（继承自 RuntimeException 的异常）或者 Error。 

***



```
注解方式：
	1.使用myabtis时，spring boot会自动配置一个【DataSourceTransactionManager】，只需在   		service类或方法上使用【@Transactional】注解， 无需其他配置。
	2.也可以在【启动类】上再加上【@EnableTransactionManagement注解】	
xml配置方式：
	

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

```Java
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

##### Java Config 方式：

​	spring boot的**自动配置机制**，会根据 **导入的jar，和applicationXXX配置文件**【spring boot的】	自动配置，这些对我们并不透明，如果你项目做的比较大，添加的持久化依赖比较多，我们还是会选择人为的指定使用哪个事务管理器。

> 在Spring容器中，我们手工注解@Bean 将被优先加载，框架不会重新实例化其他的 PlatformTransactionManager 实现类。
>
> 然后在Service中，被 @Transactional 注解的方法，将支持事务。如果注解在类上，则整个类的所有方法都默认支持事务。
>
> 1.多数据源问题
>
> 2.对于同一个工程中存在多个事务管理器要怎么处理



```
1.DataSource 配置类

	
```

