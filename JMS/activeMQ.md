#activeMQ:

**安装：**

​	1.官网下载安装包：`apache-activemq-5.15.9-bin.zip`

​	2.解压

​	3.进入bin 目录，启动activeMQ： `activeMQ start`

>启动成功后，可通过浏览器访问：  `localhost:8161/admin`, 进入可视化界面
>
>​	账号：admin
>
>​	密码：admin

配置：****

**Demo:**

**环境搭建：**

* jms服务端:   `apache-activemq-5.15.9-bin.zip`
* jar包:

```xml
<dependency>
     <groupId>org.apache.activemq</groupId>
     <artifactId>activemq-client</artifactId>
     <version>5.15.9</version>
</dependency>
```

* 编写客户端程序

  客户端采用：`tcp:IP地址:端口号` 连接服务端 【默认端口：61616】

  activeMQ的两种消息订阅模式，使用方法类似，下面以  `PTP为例`:

**生产者：**

```java
package producer;

import org.apache.activemq.ActiveMQConnectionFactory;

import javax.jms.*;

/**
 * queue消息的生产者
 */
public class QueueProducer {
    public static void main(String[] args) throws JMSException {
        // 连接工厂
        ConnectionFactory connectionFactory = new 
            ActiveMQConnectionFactory("tcp://127.0.0.1:61616");
        // 创建连接
        Connection connection = connectionFactory.createConnection();
        // 开启连接
        connection.start();
        // 创建session
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        // 创建目标对象
        Queue queue = session.createQueue("test-queue");
        // 创建生产者
        MessageProducer producer = session.createProducer(queue);
        // 创建消息对象
        TextMessage textMessage = session.createTextMessage("hello,i am producer");
        // 发送消息
        producer.send(textMessage);

        // 释放资源
        producer.close();
        session.close();
        connection.close();
    }
}

```



**消费者：**

```java
package consumer;

import org.apache.activemq.ActiveMQConnectionFactory;

import javax.jms.*;
import java.io.IOException;

public class QueueConsumer {
    public static void main(String[] args) throws JMSException, IOException {
        // 连接工厂
        ConnectionFactory connectionFactory = new 
            				ActiveMQConnectionFactory("tcp://127.0.0.1:61616");
        // 创建连接
        Connection connection = connectionFactory.createConnection();
        // 开启连接
        connection.start();
        // 创建session
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        // 创建目标对象
        Queue queue = session.createQueue("test-queue");
        // 创建消费者
        MessageConsumer consumer = session.createConsumer(queue);
        // 接收消息
        consumer.setMessageListener(new MessageListener() {
            @Override
            public void onMessage(Message message) {
                if (message instanceof TextMessage){
                    TextMessage textMessage = (TextMessage) message;
                    try {
                        System.out.println(textMessage.getText());
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
            }
        });

        // 等待消息
        System.in.read();

        // 释放资源
        consumer.close();
        session.close();
        connection.close();
    }
}

```



##spring-data-jms:

**Demo:**

**环境搭建**

* 启动activeMQ服务端

* 导入maven:

  * ```xml
    <!--spring相关依赖-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <!--spring整合jms-->
    <dependency>
    	<groupId>org.springframework</groupId>
        <artifactId>spring-jms</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <!--activeMQ客户端-->
    <dependency>
    	<groupId>org.apache.activemq</groupId>
    	<artifactId>activemq-client</artifactId>
    	<version>5.15.9</version>
    </dependency>
    <!--不加这个会,在创建spring管理MQ的连接工厂时会报错-->
    <dependency>
    	<groupId>javax.jms</groupId>
    	<artifactId>javax.jms-api</artifactId>
    	<version>2.0.1</version>
     </dependency>
    ```

* 编写客户端程序：

###生产者：

​	**编码：**注入JmsTemplate模板类 和 Destination目标；然后使用模板调用send()方法即可

​	**配置文件：**

​		JMS连接

​		spring 管理 JMS连接的工厂

​		模板类

​		消息及其目的地

​	**web.xml** :加载配置文件

  ```java
  @Autowired
      private JmsTemplate jmsTemplate; // jms模板类
  
      @Autowired
      private Destination destination; // 目标对象
      
      @Override
      public void save(Goods goods) {
          goodsMapper.saveGoods(goods);
          jmsTemplate.send(destination, new MessageCreator() {
              @Override
              public Message createMessage(Session session) throws JMSException {
                  String jsonString = JSONArray.toJSONString(goods); 
                  TextMessage textMessage = session.createTextMessage(jsonString);
                  return textMessage;
              }
          });
      }
  ```

  **配置文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd ">
	
	<!-- Spring提供的JMS工具类，它可以进行消息发送、接收等 -->
	<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
		<!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->
		<property name="connectionFactory" ref="jmsConnectionFactory"/>
	</bean>

	<!-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供-->
	<bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
		<property name="brokerURL" value="tcp://127.0.0.1:61616"/>
	</bean>

	<!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->
	<bean id="jmsConnectionFactory" 
          class="org.springframework.jms.connection.SingleConnectionFactory">
		<property name="targetConnectionFactory" ref="targetConnectionFactory"/>
	</bean>

	<!--这个是队列目的地，点对点的  文本信息-->
	<bean id="queueTextDestination" class="org.apache.activemq.command.ActiveMQQueue">
		<constructor-arg value="goods_save"/>
	</bean>
	<!--这个是订阅模式  文本信息-->
	<bean id="topicTextDestination" class="org.apache.activemq.command.ActiveMQTopic">
		<constructor-arg value="topic_text"/>
	</bean>
</beans>
```

###消费者：

​	**编码：**消费者只需实现 MessageListener接口，重写onMessage（）方法

​	**配置文件：**大体和生产者类似，只是不需要配置模板类，但需要新增一个监听容器。

​	**web.xml**：加载配置文件

```java
package consumer;

import com.alibaba.fastjson.JSONObject;
import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;

public class SpringDemoConsumerListener implements MessageListener {
    @Override
    public void onMessage(Message message) {
        TextMessage textMessage = (TextMessage) message;
        try {
            Object parse = JSONObject.parse(textMessage.getText());
            parse.toString();
            System.out.println("接收到消息");
        } catch (JMSException e) {
            e.printStackTrace();
        }
    }
}
```

**配置文件：**

```xml
<!--省略和生产者相同的部分-->
<!--这个是队列目的地，点对点的  文本信息-->
<bean id="queueTextDestination" class="org.apache.activemq.command.ActiveMQQueue">
	<constructor-arg value="goods_save"/>
</bean>
<!-- 我的监听类 -->
<bean id="myMessageListener" class="consumer.SpringDemoConsumerListener"/>
<!-- 消息监听容器 -->
<bean class="org.springframework.jms.listener.DefaultMessageListenerContainer">
	<property name="connectionFactory" ref="jmsConnectionFactory"/>
	<property name="destination" ref="queueTextDestination"/>
	<property name="messageListener" ref="myMessageListener"/>
</bean>
```

`Tips:`

​	1.消费者配置的消息目的地 必须 和生产者中配置的消息目的地一致

​	2.

### BUG:

1.在创建spring 管理jms连接的工厂时：

```xml
<bean id="jmsConnectionFactory" 	
      class="org.springframework.jms.connection.SingleConnectionFactory">
		<!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->
	 <property name="targetConnectionFactory" ref="targetConnectionFactory"/>
</bean>
```

报错：

​	`Caused by: java.lang.NoClassDefFoundError: javax/jms/JMSContext`

原因：

	>使用的spring版本是5.0.4.RELEASE，activemq-core版本5.7.0。这个版本的Spring需要使用JMS 2.0版本，但spring-jms的依赖没有自动导入JMS 2.0，而activemq-core会导入JMS 1.1的依赖，这就导致出现版本问题， 

解决：

​	导入依赖：

```xml
<dependency>
	<groupId>javax.jms</groupId>
	<artifactId>javax.jms-api</artifactId>
	<version>2.0.1</version>
</dependency>
```
