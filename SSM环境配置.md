#配置文件：

**web.xml**

```XML
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- 1.前端控制器（加载classpath:springmvc.xml 服务器启动创建servlet） -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置初始化参数，创建完DispatcherServlet对象，加载springmvc.xml配置文件 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:mvc/spring-mvc.xml</param-value>
        </init-param>
        <!-- 服务器启动的时候，让DispatcherServlet对象创建 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>

    <!--2.监听器-->
    <!-- 加载类路径下的spring整合mybatis的配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:spring/applicationContext.xml</param-value>
    </context-param>
    <!-- 配置监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--3.解决中文乱码过滤器 -->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
```

**spring.mvc**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop.xsd
           ">
    <!--1.配置注解扫描：-->
    <!--可否直接设置base-package的值为controller所在包-->
    <context:component-scan base-package="controller">
        <!--&lt;!&ndash; 扫描controller的注解，别的不扫描 &ndash;&gt;-->
        <!--<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>-->
    </context:component-scan>


    <!--&lt;!&ndash;fastjson的转换器&ndash;&gt;-->
    <!--<mvc:annotation-driven>-->
        <!--<mvc:message-converters register-defaults="true">-->
            <!--<bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">-->
                <!--<property name="supportedMediaTypes" value="application/json"/>-->
                <!--<property name="features">-->
                    <!--<array>-->
                        <!--<value>WriteMapNullValue</value>-->
                        <!--<value>WriteDateUseDateFormat</value>-->
                    <!--</array>-->
                <!--</property>-->
            <!--</bean>-->
        <!--</mvc:message-converters>-->
    <!--</mvc:annotation-driven>-->


    <!--2.配置视图解析器（可选） -->
    <!--一般用于mvc的返回值是字符串类型，然后可以通过配置视图解析器，找到指定的视图-->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- JSP文件所在的目录 -->
        <property name="prefix" value="/static/" />
        <!-- 文件的后缀名：mvc的String类型返回值+后缀： list(MVC返回值).jsp(后缀) -->
        <property name="suffix" value=".jsp" />
    </bean>

    <!--3.设置静态资源不过滤 -->
    <!--如果web.xml中前端控制器内的拦截路径不是拦截所有,比如配的是 *.do ,那么此处可不配，因为，前端控制器只会拦截*.do的资源-->
    <!--如果使用spring security,需要在spring security的配置文件中配置 ，不拦截的静态资源和 登录注册等页面-->
    <!--<mvc:resources location="/css/" mapping="/css/**" />-->
    <!--<mvc:resources location="/img/" mapping="/img/**" />-->
    <!--<mvc:resources location="/js/" mapping="/js/**" />-->
    <!--<mvc:resources location="/plugins/" mapping="/plugins/**" />-->

    <!--4.开启对SpringMVC注解的支持 -->
    <mvc:annotation-driven />

    <!--
        支持AOP的注解支持，AOP底层使用代理技术
        JDK动态代理，要求必须有接口
        cglib代理，生成子类对象，proxy-target-class="true" 默认使用cglib的方式
    -->
    <!--5.对spring AOP注解支持-->
    <!--默认为false，使用cglib代理，为true时，使用 JDK代理-->
    <aop:aspectj-autoproxy proxy-target-class="true"/>

</beans>
```

**mybatis:**

全局：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--实体类别名-->
    <typeAliases>
        <!--设置一个类的别名-->
        <!--<typeAlias type="pojo.Goods" alias="goods"/>-->

        <!--设置一个包下所有的实体类的别名：默认类名首字母小写-->
        <package name="pojo"/>
    </typeAliases>

    <!--PageHelper插件-->
    <!--<plugins>-->
        <!--<plugin interceptor="com.github.pagehelper.PageHelper">-->
            <!--<property name="dialect" value="mysql"/>-->
            <!--<property name="offsetAsPageNum" value="false"/>-->
            <!--<property name="rowBoundsWithCount" value="false"/>-->
            <!--<property name="pageSizeZero" value="true"/>-->
            <!--<property name="reasonable" value="false"/>-->
            <!--<property name="supportMethodsArguments" value="false"/>-->
            <!--<property name="returnPageInfo" value="none"/>-->
        <!--</plugin>-->
    <!--</plugins>-->
</configuration>
```

**实体类映射文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.wxss.shop.mapper.GoodsMapper" >
  <!--没有配置实体类的别名时，需要使用全限定路径-->
  <resultMap id="BaseResultMap" type="com.wxss.shop.pojo.Goods" >

  <!--配置后，默认别名为类名：首字母小写，后续不变-->
  <!--<resultMap id="BaseResultMap" type="goods" >-->
    <id column="id" property="id" jdbcType="INTEGER" />
    <result column="goods_name" property="goodsName" jdbcType="VARCHAR" />
    <result column="title" property="title" jdbcType="VARCHAR" />
    <result column="price" property="price" jdbcType="DECIMAL" />
    <result column="count" property="count" jdbcType="INTEGER" />
    <result column="status" property="status" jdbcType="INTEGER" />
    <result column="goodsDesc" property="goodsdesc" jdbcType="VARCHAR" />
    <result column="isDelete" property="isdelete" jdbcType="INTEGER" />
    <result column="create_time" property="createTime" jdbcType="TIMESTAMP" />
    <result column="update_time" property="updateTime" jdbcType="TIMESTAMP" />
  </resultMap>
  <sql id="Example_Where_Clause" >
    <where >
      <foreach collection="oredCriteria" item="criteria" separator="or" >
        <if test="criteria.valid" >
          <trim prefix="(" suffix=")" prefixOverrides="and" >
            <foreach collection="criteria.criteria" item="criterion" >
              <choose >
                <when test="criterion.noValue" >
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue" >
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue" >
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue" >
                  and ${criterion.condition}
                  <foreach collection="criterion.value" item="listItem" open="(" close=")" separator="," >
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Update_By_Example_Where_Clause" >
    <where >
      <foreach collection="example.oredCriteria" item="criteria" separator="or" >
        <if test="criteria.valid" >
          <trim prefix="(" suffix=")" prefixOverrides="and" >
            <foreach collection="criteria.criteria" item="criterion" >
              <choose >
                <when test="criterion.noValue" >
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue" >
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue" >
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue" >
                  and ${criterion.condition}
                  <foreach collection="criterion.value" item="listItem" open="(" close=")" separator="," >
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Base_Column_List" >
    id, goods_name, title, price, count, status, goodsDesc, isDelete, create_time, update_time
  </sql>
  <select id="selectByExample" resultMap="BaseResultMap" parameterType="com.wxss.shop.pojo.GoodsExample" >
    select
    <if test="distinct" >
      distinct
    </if>
    <include refid="Base_Column_List" />
    from goods
    <if test="_parameter != null" >
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null" >
      order by ${orderByClause}
    </if>
  </select>
  <select id="selectByPrimaryKey" resultMap="BaseResultMap" parameterType="java.lang.Integer" >
    select 
    <include refid="Base_Column_List" />
    from goods
    where id = #{id,jdbcType=INTEGER}
  </select>
  <delete id="deleteByPrimaryKey" parameterType="java.lang.Integer" >
    delete from goods
    where id = #{id,jdbcType=INTEGER}
  </delete>
  <delete id="deleteByExample" parameterType="com.wxss.shop.pojo.GoodsExample" >
    delete from goods
    <if test="_parameter != null" >
      <include refid="Example_Where_Clause" />
    </if>
  </delete>
  <insert id="insert" parameterType="com.wxss.shop.pojo.Goods" >
    insert into goods (id, goods_name, title, 
      price, count, status, 
      goodsDesc, isDelete, create_time, 
      update_time)
    values (#{id,jdbcType=INTEGER}, #{goodsName,jdbcType=VARCHAR}, #{title,jdbcType=VARCHAR}, 
      #{price,jdbcType=DECIMAL}, #{count,jdbcType=INTEGER}, #{status,jdbcType=INTEGER}, 
      #{goodsdesc,jdbcType=VARCHAR}, #{isdelete,jdbcType=INTEGER}, #{createTime,jdbcType=TIMESTAMP}, 
      #{updateTime,jdbcType=TIMESTAMP})
  </insert>
  <insert id="insertSelective" parameterType="com.wxss.shop.pojo.Goods" >
    insert into goods
    <trim prefix="(" suffix=")" suffixOverrides="," >
      <if test="id != null" >
        id,
      </if>
      <if test="goodsName != null" >
        goods_name,
      </if>
      <if test="title != null" >
        title,
      </if>
      <if test="price != null" >
        price,
      </if>
      <if test="count != null" >
        count,
      </if>
      <if test="status != null" >
        status,
      </if>
      <if test="goodsdesc != null" >
        goodsDesc,
      </if>
      <if test="isdelete != null" >
        isDelete,
      </if>
      <if test="createTime != null" >
        create_time,
      </if>
      <if test="updateTime != null" >
        update_time,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides="," >
      <if test="id != null" >
        #{id,jdbcType=INTEGER},
      </if>
      <if test="goodsName != null" >
        #{goodsName,jdbcType=VARCHAR},
      </if>
      <if test="title != null" >
        #{title,jdbcType=VARCHAR},
      </if>
      <if test="price != null" >
        #{price,jdbcType=DECIMAL},
      </if>
      <if test="count != null" >
        #{count,jdbcType=INTEGER},
      </if>
      <if test="status != null" >
        #{status,jdbcType=INTEGER},
      </if>
      <if test="goodsdesc != null" >
        #{goodsdesc,jdbcType=VARCHAR},
      </if>
      <if test="isdelete != null" >
        #{isdelete,jdbcType=INTEGER},
      </if>
      <if test="createTime != null" >
        #{createTime,jdbcType=TIMESTAMP},
      </if>
      <if test="updateTime != null" >
        #{updateTime,jdbcType=TIMESTAMP},
      </if>
    </trim>
  </insert>
  <select id="countByExample" parameterType="com.wxss.shop.pojo.GoodsExample" resultType="java.lang.Integer" >
    select count(*) from goods
    <if test="_parameter != null" >
      <include refid="Example_Where_Clause" />
    </if>
  </select>
  <update id="updateByExampleSelective" parameterType="map" >
    update goods
    <set >
      <if test="record.id != null" >
        id = #{record.id,jdbcType=INTEGER},
      </if>
      <if test="record.goodsName != null" >
        goods_name = #{record.goodsName,jdbcType=VARCHAR},
      </if>
      <if test="record.title != null" >
        title = #{record.title,jdbcType=VARCHAR},
      </if>
      <if test="record.price != null" >
        price = #{record.price,jdbcType=DECIMAL},
      </if>
      <if test="record.count != null" >
        count = #{record.count,jdbcType=INTEGER},
      </if>
      <if test="record.status != null" >
        status = #{record.status,jdbcType=INTEGER},
      </if>
      <if test="record.goodsdesc != null" >
        goodsDesc = #{record.goodsdesc,jdbcType=VARCHAR},
      </if>
      <if test="record.isdelete != null" >
        isDelete = #{record.isdelete,jdbcType=INTEGER},
      </if>
      <if test="record.createTime != null" >
        create_time = #{record.createTime,jdbcType=TIMESTAMP},
      </if>
      <if test="record.updateTime != null" >
        update_time = #{record.updateTime,jdbcType=TIMESTAMP},
      </if>
    </set>
    <if test="_parameter != null" >
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByExample" parameterType="map" >
    update goods
    set id = #{record.id,jdbcType=INTEGER},
      goods_name = #{record.goodsName,jdbcType=VARCHAR},
      title = #{record.title,jdbcType=VARCHAR},
      price = #{record.price,jdbcType=DECIMAL},
      count = #{record.count,jdbcType=INTEGER},
      status = #{record.status,jdbcType=INTEGER},
      goodsDesc = #{record.goodsdesc,jdbcType=VARCHAR},
      isDelete = #{record.isdelete,jdbcType=INTEGER},
      create_time = #{record.createTime,jdbcType=TIMESTAMP},
      update_time = #{record.updateTime,jdbcType=TIMESTAMP}
    <if test="_parameter != null" >
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByPrimaryKeySelective" parameterType="com.wxss.shop.pojo.Goods" >
    update goods
    <set >
      <if test="goodsName != null" >
        goods_name = #{goodsName,jdbcType=VARCHAR},
      </if>
      <if test="title != null" >
        title = #{title,jdbcType=VARCHAR},
      </if>
      <if test="price != null" >
        price = #{price,jdbcType=DECIMAL},
      </if>
      <if test="count != null" >
        count = #{count,jdbcType=INTEGER},
      </if>
      <if test="status != null" >
        status = #{status,jdbcType=INTEGER},
      </if>
      <if test="goodsdesc != null" >
        goodsDesc = #{goodsdesc,jdbcType=VARCHAR},
      </if>
      <if test="isdelete != null" >
        isDelete = #{isdelete,jdbcType=INTEGER},
      </if>
      <if test="createTime != null" >
        create_time = #{createTime,jdbcType=TIMESTAMP},
      </if>
      <if test="updateTime != null" >
        update_time = #{updateTime,jdbcType=TIMESTAMP},
      </if>
    </set>
    where id = #{id,jdbcType=INTEGER}
  </update>
  <update id="updateByPrimaryKey" parameterType="com.wxss.shop.pojo.Goods" >
    update goods
    set goods_name = #{goodsName,jdbcType=VARCHAR},
      title = #{title,jdbcType=VARCHAR},
      price = #{price,jdbcType=DECIMAL},
      count = #{count,jdbcType=INTEGER},
      status = #{status,jdbcType=INTEGER},
      goodsDesc = #{goodsdesc,jdbcType=VARCHAR},
      isDelete = #{isdelete,jdbcType=INTEGER},
      create_time = #{createTime,jdbcType=TIMESTAMP},
      update_time = #{updateTime,jdbcType=TIMESTAMP}
    where id = #{id,jdbcType=INTEGER}
  </update>
</mapper>
```

**spring**

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">

        <!--1.引入数据源配置文件-->
        <!--方式1：-->
        <context:property-placeholder location="classpath:jdbc.properties"/>
        <!--方式2: -->
        <!--<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">-->
                <!--<property name="location" value="classpath:jdbc.properties" />-->
        <!--</bean>-->


        <context:component-scan base-package="service"/>
        <context:component-scan base-package="mapper"/>


        <!--2.配置数据源：阿里的druid连接池-->
        <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
                <property name="driverClassName" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
        </bean>

        <!--3.配置spring 管理mybatis 的 SqlSessionFactoryBean；注入数据源-->
        <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
                <property name="dataSource" ref="druidDataSource"/>
                <!--指定mybatis全局配置文件的位置-->
                <property name="configLocation" value="classpath:mybatis/mybatis-config.xml"/>
                <!--指定实体映射配置位置: 路径上**迭代查找：可在全局配置中配置-->
                <property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
                <!--配置实体类别名：可在全局配置中配置-->
                <!--<property name="typeAliasesPackage" value="pojo"/>-->
                <!-- 传入PageHelper的插件 -->
                <property name="plugins">
                        <array>
                                <bean class="com.github.pagehelper.PageInterceptor">
                                        <property name="properties">
                                                <props>
                                                        <prop key="helperDialect">mysql</prop>
                                                        <prop key="reasonable">true</prop>
                                                </props>
                                        </property>
                                </bean>
                        </array>
                </property>
        </bean>

        <!--4.扫描dao接口,目的是生成代理对象 -->
        <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
                <property name="basePackage" value="mapper"/>
        </bean>

        <!--5.配置Spring的声明式事务管理 -->
        <!--5.1 配置事务管理器 -->
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
                <property name="dataSource" ref="druidDataSource"/>
        </bean>

        <!--5.2 事务注解支持-->
        <tx:annotation-driven transaction-manager="transactionManager"/>

</beans>
```

#配置类方式：

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

**配置类：**

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

