# 日志

**参考：**

​	<https://mp.weixin.qq.com/s/vCixKVXys5nTTcQQnzrs3w>   日志门面

​	<https://www.cnblogs.com/dengyungao/p/7524902.html>  log4j组件

​	<https://blog.csdn.net/chenruijia170707/article/details/81298581>  好文

> 眼下java应用日志收集都是採用日志框架(slf4j、apache commons logging即jcl)+日志系统（log4j、log4j2、LogBack、JUL等）的方式。
>
> 而针对在分布式环境须要实时分析统计的日志，一般採用apache flume、facebook scribe等分布式日志收集系统。

**日志门面**：提供日志调用的接口，实际的日志输出托付给日志系统实现。

- JCL(Jakarta Commons Logging)：比較流行的日志框架，非常多框架都依赖JCL，比如==Spring==等。
- SLF4j：提供新的API，初衷是配合Logback使用，但同一时候兼容Log4j。

**日志系统**：负责输出日志

- Log4j：经典的一种日志解决方式。内部把日志系统抽象封装成Logger 、appender 、pattern 等实现。我们能够通过配置文件轻松的实现日志系统的管理和多样化配置。
- Log4j2：Log4j的2.0版本号。对Log4j进行了优化。比方支持參数API、支持异步appender、插件式架构等
- Logback：Log4j的替代产品。须要配合日志框架SLF4j使用
- JUL(java.util.logging)：JDK提供的日志系统。较混乱，不经常使用

## 日志框架

## 日志门面

## 



### log4j:

#### 日志输出级别：

log4j共有5种输出级别，从高到低，依次为：

```
off 最高等级，用于关闭所有日志记录。
fatal 指出每个严重的错误事件将会导致应用程序的退出。
error 指出虽然发生错误事件，但仍然不影响系统的继续运行。
warn 表明会出现潜在的错误情形。
info 一般和在粗粒度级别上，强调应用程序的运行全程。
debug 一般用于细粒度级别上，对调试应用程序非常有帮助。
all 最低等级，用于打开所有日志记录。
```

1.Log4j只建议使用4个级别，优先级从高到低分别是error,warn,info和debug。 

2.**只会输出比设置的级别更高(包括当前级别)的日志信息**，如配置文件中，配置的是info，那么在使用时，级别比info低的debug的日志信息，就不会输出

#### 三大组件：

| Appender   | 负责日志的输出         |
| ---------- | ---------------------- |
| **Logger** | 负责收集处理日志记录   |
| Layout     | 负责对输出的日志格式化 |

>- Appenders：也被称为Handlers。负责将日志事件记录到目标位置。在将日志事件输出之前，Appenders使用Layouts来对事件进行格式化处理。
>- Layouts：也被称为Formatters，它负责对日志事件中的数据进行转换和格式化。Layouts决定了数据在一条日志记录中的终于形式。
>- Loggers：Logger负责捕捉事件并将其发送给合适的Appender。
>  - 总是存在一个rootLogger，即使没有显示配置也是存在的，而且默认输出级别为DEBUG。其他的logger都继承自这个rootLogger。 

**logcontext:**

>各个logger 都被关联到一个 LoggerContext，LoggerContext负责制造logger，也负责以树结构排列各 logger。其他所有logger也通过org.slf4j.LoggerFactory 类的静态方法getLogger取得。 getLogger方法以 logger 名称为参数。用同一名字调用LoggerFactory.getLogger 方法所得到的永远都是同一个logger对象的引用。 



#### 配置文件：

log4j支持两种形式的配置文件：

​	1.java 的 属性集文件

​	2.xml文件

`log4j.properties:`

>```properties
># log4j.properties:
># 设置输出级别为error和输出方式：控制台和文件
>log4j.rootCategory = ERROR,CONSOLE,LOGFILE
># 指定需要日志输出的范围：log4j.logger.范围 = 级别
># 如，需要记录com包下A类的日志信息:
>log4j.logger.com.A = DEBUG
>
>### 指定级别的日志信息输出到控制抬 ###
>log4j.appender.CONSOLE = org.apache.log4j.ConsoleAppender
># 输出日志到控制台的方式： System.err或  System.out
>log4j.appender.CONSOLE.Target = System.err
>#日志级别，info及以上级别的均会输出。写成INFO也行
>log4j.appender.CONSOLE.Threshold=日志级别
>log4j.appender.CONSOLE.ImmediateFlush=true
>log4j.appender.CONSOLE.Encoding=UTF-8
>log4j.appender.CONSOLE.layout = org.apache.log4j.PatternLayout
>log4j.appender.CONSOLE.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n
>
>### 输出DEBUG 级别以上的日志到=E://logs/error.log ###
>log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
>#日志文件输出路径
>log4j.appender.D.File = E://logs/log.log
>log4j.appender.errorFile.DatePattern = '.'yyyy-MM-dd-HH  
># 设定信息是增加而不是覆盖  
>log4j.appender.D.Append = true
># 级别：>=debug
>log4j.appender.D.Threshold = DEBUG 
># 设置日志内容格式
>log4j.appender.D.layout = org.apache.log4j.PatternLayout
>log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
>```
>
>上述配置中：
>
>​	1.全局输出级别为error：只有输出级别大于等于ERROR的，日志信息才会被记录（输出）
>
>​	2.指定范围的：com包下的A类中的所有大于等于DEBUG级别的日志信息都会被记录（输出）

`xml方式：`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j='http://jakarta.apache.org/log4j/' >
 
    <appender name="myConsole" class="org.apache.log4j.ConsoleAppender">
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="[%d{yyyy-MM-dd HH:mm:ss:SSS}] [%p] - %m - %l%n" />
        </layout>
        <!--过滤器设置输出的级别-->   
        <filter class="org.apache.log4j.varia.LevelRangeFilter">
            <param name="levelMin" value="debug" />
            <param name="levelMax" value="warn" />
            <param name="AcceptOnMatch" value="true" />
        </filter>
    </appender>
 
    <appender name="myFile" class="org.apache.log4j.RollingFileAppender">
        <param name="File" value="D:/output.log" /><!-- 设置日志输出文件名 -->
        <!-- 设置是否在重新启动服务时，在原有日志的基础添加新日志 -->
        <param name="Append" value="true" />
        <param name="MaxBackupIndex" value="10" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%p (%c:%L)- %m%n" />
        </layout>
    </appender>
 
    <appender name="activexAppender" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="E:/activex.log" />
        <param name="DatePattern" value="'.'yyyy-MM-dd'.log'" />
        <layout class="org.apache.log4j.PatternLayout">
         <param name="ConversionPattern" value="[%d{yyyy-MM-dd HH:mm:ss:SSS}] [%p] - %m - %l%n" />
        </layout>
    </appender>
 
 
    <!-- 指定logger的设置，additivity指示是否遵循缺省的继承机制-->
    <logger name="com.runway.bssp.activeXdemo" additivity="false">
        <priority value ="info"/>
        <appender-ref ref="activexAppender" />
    </logger>
 
    <!-- 根logger的设置-->
    <root>
        <priority value ="debug"/>
        <appender-ref ref="myConsole"/>
        <appender-ref ref="myFile"/>
    </root>
 
</log4j:configuration>
```

##### **1.logger**

`log4j.rootLogger `或 `log4j.rootCategory`

默认存在一个根logger，且级别是DEBUG，

```properties
log4j.rootLogger = DEBUG,CONSOLE,FILE,file-error,file-debug
```

没有配置，日志记录失败：

```
log4j:WARN No appenders could be found for logger (com.wxss.log.LogTest).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
```

log4j还可以指定输出内容的范围：（包，类，方法）

==**指定包和类：**==

	>随着项目规模的越来越大，会不断的引入新的模块，不同的模块都会打印自己的日志，最后就造成日志难以查看
	
	>可以将不同的日志加以分类输出，这样相互的日志不影响，尤其重要的接口访问日志，能够很方便的定位和排查问题。

 如下，指定com.wxss.log2包下的日志记录方式为：

```properties
# 日志级别为warn,且appender的name为test
log4j.logger.com.wxss.log2 = warn,test
# 设置该包下的日志记录不会在父logger中记录
log4j.additivity.com.wxss.log2=false
# test使用的appender类
log4j.appender.test=org.apache.log4j.FileAppender
# 日志保存的文件目录
log4j.appender.test.File=F://logs/log-debug111.log
# 每次记录采用追加的方式
log4j.appender.test.Append=true
# 布局
log4j.appender.test.layout=org.apache.log4j.PatternLayout
log4j.appender.test.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%-5p][%c{1}] [%t] - %m%n
# 编码
log4j.appender.test.encoding=UTF-8
```

`Tips`:

​	`log4j.logger.com.wxss.log2 = warn,test`  和

​	`log4j.rootLogger = DEBUG,CONSOLE,FILE,file-error,file-debug`

​	==第一个为日志级别，后面的是appender的name==

##### **2.Appender**

```properties
### 输出信息到控制抬 ###
log4j.appender.CONSOLE = org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.Target = System.err
log4j.appender.CONSOLE.Threshold=TRACE
log4j.appender.CONSOLE.ImmediateFlush=true
log4j.appender.CONSOLE.layout = org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n
```

`Tips：`

​	**log4j提供的appender：**

```
org.apache.log4j.ConsoleAppender（控制台），  
org.apache.log4j.FileAppender（文件），  
org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件），  
org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件），  
org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
```

##### **3.layout**

```properties
log4j.appender.CONSOLE.layout = org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} 
```

`Tips:`

​	**1.log4j提供的布局：**

```properties
org.apache.log4j.HTMLLayout（以HTML表格形式布局），  
# 常用PatternLayout
org.apache.log4j.PatternLayout（可以灵活地指定布局模式），  
org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），  
org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）
```

​	**2. 日志布局表达式：**

```properties
常用表达式：(区分大小写)
	%C : 输出调用Logger的类的全限定类名
		%d{yyyy-MM-dd HH:mm:ss}
	%L : 显示调用logger的代码行
	%m : 显示输出信息：在程序中自定义的信息（最重要的）
	%M : 显示调用logger的方法名
	%P : 显示该日志的优先级
	%p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL  
	%r 输出自应用启动到输出该log信息耗费的毫秒数  
	%t 输出产生该日志事件的线程名  
	%n 当前平台的换行符，Windows平台为“rn”，Unix平台为“n”  
	%d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd 	
		HH:mm:ss,SSS}，输出类似：2002年10月18日 22：10：28，921  
	%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：	
	Testlog4.main(TestLog4.java:10)
```

##### **4.level:**

>level为logger服务，用来定义日志的级别，他的值可以是：
>
>​	OFF（关闭）
>
>​	FATAL（致命的）
>
>​	ERROR（错误）
>
>​	WARN（警告） 
>
>​	INFO（信息）
>
>​	DEBUG （调试） 
>
>​	ALL（所有）
>
>输出日志的策略由此Level决定，
>
>例如：如果logger的Level设置为INFO，那么系统只输出INFO以及以上（WARN、ERROR、FATAL）信息。如果当前logger没有设定Level，那么它在输出日志时采用的策略是：它会使用它继承的Logger的Level作为它自己的Level来处理。如果它的上级也没有设置Level，那么就找上上级，几次类推，直到root为止，（root的Level是不能设为空的，所以最终一定会找到一个Level）**。**
>
>**默认root的Level是DEBUG，其他logger的Level默认都是null，需要手动指定。**

#####  **5.additivity**

>additivity是 子Logger 是否继承 父Logger 的 输出源（appender） 的标志位。
>
>==默认情况下 ,子Logger 会继承 父Logger 的appender（additivity=true）==，也就是说 子Logger 会在 父Logger 的appender里输出。==additivity=false时，子logger的日志不会在父logge配置的appender中输出==

***

#### Demo：

##### log4j:

maven:

```xml
<dependencys>		
    	<!--日志门面-->
		<dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
    	<!--适配器-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
		</dependency>
    	<!--日志框架-->
		<dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
    	<!--lombok：非必须-->
    	 <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.6</version>
        </dependency>
</dependencys>
```

编码：

```java
package com.wxss.log;

import lombok.extern.slf4j.Slf4j;
import org.junit.Test;

@Slf4j // lombok的注解
public class LogTest {
    @Test
    public void testLog4j(){
        log.trace("trace:"+ com.wxss.log.LogTest.class.getPackage());
        log.debug("debug:"+ com.wxss.log.LogTest.class.getPackage());
        log.info("info:"+ com.wxss.log.LogTest.class.getPackage());
        log.warn("warn:"+ com.wxss.log.LogTest.class.getPackage());
        log.error("error:"+ com.wxss.log.LogTest.class.getPackage());
    }
}
```

`Tips`:

​	1.也可以只导入适配器    `slf4j-log4j12`的jar，它会自动将门面和对应的日志框架导入

​	2.导入        `lombok.jar`可以使用 `@Slf4j`注解，直接 `log.info(str)` 来打印日志，不导，可使用门面：

​	   `private final Logger log = LoggerFactory.getLogger(LogTest.class);`

​	   推荐使用门面的方法，来获取Logger，而不是其实现

##### spring使用log4j:

* **maven:**

```xml
<dependencys>		
    	<!--日志门面-->
		<dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
    	<!--适配器-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
		</dependency>
    	<!--日志框架-->
		<dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
</dependencys>
```

>slf4j-api.jar：Java的简单日志门面。
>
>slf4j-log4j12.jar：类似于中间件，一边对应底层的接口，一边可以根据偏好接入不同的日志系统。
>
>log4j.jar ： 具体的日志框架。是日志门面接口的实现， 

##### mybatis使用log4j：

##### spring boot整合log4j:

因为springboot 默认使用logback，所以需要==先排除logback的依赖,再导入log4j相关依赖==

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		 	<!--排除logback-->
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-logging</artifactId>
				</exclusion>
			</exclusions>
</dependency>
<!--导入spring boot 整合log4j-->
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-log4j</artifactId>
</dependency>

```

`tips:`

* 上述pom.xml中也可替换为 三剑客
* 用法相同

打印sql信息：

```xml

 <logger name="org.hibernate.type.descriptor.sql.BasicBinder"> 
    <level value="trace" /> 
   </logger>
   <logger name="org.hibernate.type.descriptor.sql.BasicExtractor"> 
    <level value="trace" /> 
   </logger>
   <logger name="org.hibernate.sql">     
    <level value="trace" /> 
   </logger>   
   <logger name="org.hibernate.type">     
    <level value="trace" /> 
   </logger>   
```



***

### log4j2

log4j是通过一个.properties的文件作为主配置文件的，而现在的log4j2则已经弃用了这种方式，采用的是.xml，.json或者.jsn这种方式来做

`Tips`

​	spring boot 官方推荐使用 XXX-spring.xml方式命名其他配置文件。

如果想自定义，可以在主配置文件指定外部配置文件的位置：

```
logging.config=classpath:logging-config.xml
```

根节点：`configuration`

```
属性：
	monitorInterval : 自动检测修改配置的时间
	status：用来指定log4j本身的打印日志的级别.
子节点：
	Appenders : 定义 多个 appender
	Loggers ： 
```

>**==Appenders 节点有三个子节点：==**
>
>1.**Console节点用来定义输出到控制台的Appender**.
>
>- name:指定Appender的名字.
>- target:SYSTEM_OUT 或 SYSTEM_ERR,一般只设置默认:SYSTEM_OUT.
>- PatternLayout:输出格式，不设置默认为:%m%n.
>
>2.**File节点用来定义输出到指定位置的文件的Appender**.
>
>- name:指定Appender的名字.
>- fileName:指定输出日志的目的文件带全路径的文件名.
>- PatternLayout:输出格式，不设置默认为:%m%n.
>
>3.**RollingFile节点用来定义超过指定条件自动删除旧的创建新的Appender**.
>
>- name:指定Appender的名字.
>- fileName:指定输出日志的目的文件带全路径的文件名.
>- PatternLayout:输出格式，不设置默认为:%m%n.
>- filePattern : 指定当发生Rolling时，文件的转移和重命名规则.
>- Policies:指定滚动日志的策略，就是什么时候进行新建日志文件输出日志.
>- TimeBasedTriggeringPolicy:Policies子节点，基于时间的滚动策略，interval属性用来指定多久滚动一次，默认是1 hour。modulate=true用来调整时间：比如现在是早上3am，interval是4，那么第一次滚动是在4am，接着是8am，12am...而不是7am.
>- SizeBasedTriggeringPolicy:Policies子节点，基于指定文件大小的滚动策略，size属性用来定义每个日志文件的大小.
>- DefaultRolloverStrategy:用来指定同一个文件夹下最多有几个日志文件时开始删除最旧的，创建新的(通过max属性)。
>
>==**Loggers节点，常见的有两种:Root和Logger**.==
>Root节点用来指定项目的根日志，如果没有单独指定Logger，那么就会默认使用该Root日志输出
>
>- level:日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < AppenderRef：Root的子节点，用来指定该日志输出到哪个Appender.
>- ==Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。==
>- level:日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF.
>- name:用来指定该Logger所适用的类或者类所在的包全路径,继承自Root节点.
>- AppenderRef：Logger的子节点，用来指定该日志输出到哪个Appender,如果没有指定，就会默认继承自Root.如果指定了，那么会在指定的这个Appender和Root的Appender中都会输出，此时我们可以设置Logger的additivity="false"只在自定义的Appender中进行输出。

#### 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
<configuration monitorInterval="5">
    <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->

    <!--变量配置-->
    <Properties>
        <!-- 格式化输出：%date表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度 %msg：日志消息，%n是换行符-->
        <!-- %logger{36} 表示 Logger 名字最长36个字符 -->
        <property name="LOG_PATTERN" value="%date{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n" />
        <!-- 定义日志存储的路径，不要配置相对路径 -->
        <property name="FILE_PATH" value="D:/log" />
        <property name="FILE_NAME" value="uehd" />
    </Properties>

    <appenders>
        <console name="Console" target="SYSTEM_OUT">
            <!--输出日志的格式-->
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
        </console>

        <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，适合临时测试用-->
        <File name="Filelog" fileName="${FILE_PATH}/test.log" append="false">
            <PatternLayout pattern="${LOG_PATTERN}"/>
        </File>

        <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileInfo" fileName="${FILE_PATH}/info.log" filePattern="${FILE_PATH}/${FILE_NAME}-INFO-%d{yyyy-MM-dd}_%i.log.gz">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，默认是1 hour-->
                <TimeBasedTriggeringPolicy interval="1"/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
            <DefaultRolloverStrategy max="15"/>
        </RollingFile>

        <!--&lt;!&ndash; 这个会打印出所有的warn及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档&ndash;&gt;-->
        <RollingFile name="RollingFileWarn" fileName="${FILE_PATH}/warn.log" filePattern="${FILE_PATH}/${FILE_NAME}-WARN-%d{yyyy-MM-dd}_%i.log.gz">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，默认是1 hour-->
                <TimeBasedTriggeringPolicy interval="1"/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
            <DefaultRolloverStrategy max="15"/>
        </RollingFile>

        <!-- 这个会打印出所有的error及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileError" fileName="${FILE_PATH}/error.log" filePattern="${FILE_PATH}/${FILE_NAME}-ERROR-%d{yyyy-MM-dd}_%i.log.gz">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，默认是1 hour-->
                <TimeBasedTriggeringPolicy interval="1"/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
            <DefaultRolloverStrategy max="15"/>
        </RollingFile>

    </appenders>

    <!--Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。-->
    <!--然后定义loggers，只有定义了logger并引入的appender，appender才会生效-->
    <loggers>

        <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
        <logger name="org.mybatis" level="debug" additivity="false">
            <AppenderRef ref="Console"/>
        </logger>
        <!--监控系统信息-->
        <!--若是additivity设为false，则 子Logger 只会在自己的appender里输出，而不会在 父Logger 的appender里输出。-->
        <Logger name="org.springframework" level="info" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>

        <root level="info">
            <appender-ref ref="Console"/>
            <appender-ref ref="Filelog"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
        </root>
    </loggers>

</configuration>
```

### logback

<https://www.jianshu.com/p/f67c721eea1b> 

`Spring Boot` 默认的日志框架 `logback` ，不需要导入额外jar包，已经由  `spring-boot-starter`自动导入

`Spring Boot`中默认配置`ERROR`、`WARN`和`INFO`级别的日志输出到控制台。

==默认只会输出到控制台，而不是文件==

#### 主配置文件：

```properties
### application.properties
## 开启debug级别
debug = true 
## 全局日志级别
logging.level.root=INFO
## 指定某个包的日志级别
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate=ERROR

## 可屏蔽某包下的所有日志输出：单引必须 
logging.level.com.text= 'off'

## 配置日志输出到文件：（默认只输出到控制台）
#日志文件所在目录
logging.path=F:\\demo
# 日志文件名
logging.file=demo.log

## 设置外部日志配置文件的位置
logging.config=logback.xml
```

`tips:`

* ==配置了输入到文件，并不是替换控制台，而是同时开启==

#### 外部配置文件：

resource/logback.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false">
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_HOME" value="./logs" />
    <!-- 控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg  %n</pattern>
        </encoder>
    </appender>
    <!-- 按照每天生成日志文件 -->
    <appender name="FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名-->
            <FileNamePattern>${LOG_HOME}/runtime.log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
        <!--日志文件最大的大小-->
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <MaxFileSize>10MB</MaxFileSize>
        </triggeringPolicy>
    </appender>

    <!-- 日志输出级别 -->
    <root level="INFO">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

 

您还可以通过启动您的应用程序 `--debug` 标志来启用“调试”模式（开发的时候推荐开启）,以下两种方式皆可：

- 在运行命令后加入`--debug`标志，如：`$ java -jar springTest.jar --debug` 
- 在`application.properties`中配置`debug=true`，该属性置为`true`的时候，核心`Logger`（包含嵌入式容器、hibernate、spring）会输出更多内容，但是你**自己应用的日志并不会输出为DEBUG级别**。

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```

日志级别：

>TRACE、DEBUG、INFO、WARN 和 ERROR，定义于 ch.qos.logback.classic.Level类。
>
>如果 logger没有被分配级别，那么它将从有被分配级别的最近的祖先那里继承级别。
>
>root logger 默认级别是 DEBUG。 

log context





## log4j、logback、log4j2的对比：



***

## 日志系统的迁移：



切换适配包+导入新的日志框架的包