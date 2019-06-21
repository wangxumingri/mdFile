# JMS：

> (1) ConnectionFactory
>
> ​              创建Connection对象的工厂，针对两种不同的jms消息模型，分别有QueueConnectionFactory和TopicConnectionFactory两种。可以通过JNDI来查找ConnectionFactory对象。
>
> ​            (2) Destination
>
> ​            Destination的意思是消息生产者的消息发送目标或者说消息消费者的消息来源。对于消息生产者来说，它的Destination是某个队列（Queue）或某个主题（Topic）;对于消息消费者来说，它的Destination也是某个队列或主题（即消息来源）。
>
> ​            所以，Destination实际上就是两种类型的对象：Queue、Topic可以通过JNDI来查找Destination。
>
> ​            (3) Connection
>
> ​            Connection表示在客户端和JMS系统之间建立的链接（对TCP/IP socket的包装）。Connection可以产生一个或多个Session。跟ConnectionFactory一样，Connection也有两种类型：QueueConnection和TopicConnection。
>
> ​            (4) Session
>
> ​            Session是我们操作消息的接口。可以通过session创建生产者、消费者、消息等。Session提供了事务的功能。**当我们需要使用session发送/接收多个消息时，可以将这些发送/接收动作放到一个事务中。**同样，也分QueueSession和TopicSession。
>
> ​            (5) 消息的生产者
>
> ​            消息生产者由Session创建，并用于将消息发送到Destination。同样，消息生产者分两种类型：QueueSender和TopicPublisher。可以调用消息生产者的方法（send或publish方法）发送消息。
>
> ​            (6) 消息消费者
>
> ​            消息消费者由Session创建，用于接收被发送到Destination的消息。两种类型：QueueReceiver和TopicSubscriber。可分别通过session的createReceiver(Queue)或createSubscriber(Topic)来创建。当然，也可以session的creatDurableSubscriber方法来创建持久化的订阅者。
>
> ​            (7) MessageListener
>
> ​           消息监听器。如果注册了消息监听器，一旦消息到达，将自动调用监听器的onMessage方法。EJB中的MDB（Message-Driven Bean）就是一种MessageListener。



# AMQP：

> AMQP，即Advanced Message Queuing Protocol，高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计。消息中间件主要用于组件之间的解耦，消息的发送者无需知道消息使用者的存在，反之亦然。 AMQP的主要特征是面向消息、队列、路由（包括点对点和发布/订阅）、可靠性、安全 



> 1）信息的发送者和接收者如何维持这个连接，如果一方的连接中断，这期间的数据如何方式丢失？
>
> 2）如何降低发送者和接收者的耦合度？ 
>
> 3）如何让Priority高的接收者先接到数据？ 
>
> 4）如何做到load balance？有效均衡接收者的负载？ 
>
> 5）如何有效的将数据发送到相关的接收者？也就是说将接收者subscribe 不同的数据，如何做有效的filter。 
>
> 6）如何做到可扩展，甚至将这个通信模块发到cluster上？ 
>
> 7）如何保证接收者接收到了完整，正确的数据？ 