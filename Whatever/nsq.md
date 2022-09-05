### nsq是消息中间件



消息中间件的实现有很多，新贵kafka、RocketMQ、老牌RabbitMQ、ActiveMQ



---

###### 为什么要有消息中间件？？？

假设我们在淘宝下了一笔订单后，淘宝后台需要做这些事情：

1. 消息通知系统：通知商家，你有一笔新的订单，请及时发货
2. 推荐系统：更新用户画像，重新给用户推荐他可能感兴趣的商品
3. 会员系统：更新用户的积分和等级信息
4. …

<img src="https://pic4.zhimg.com/80/v2-d7dff8a2d5620bcd4fb05004be24509a_720w.jpg" alt="img" style="zoom: 50%;" />

这样的做法，显然很挫，至少有两个问题：

1. **过度耦合**：如果后面创建订单时，需要触发新的动作，那就得去改代码，在原有的创建订单函数末尾，再追加一行代码
2. **缺少缓冲**：如果创建订单时，会员系统恰好处于非常忙碌或者宕机的状态，那这时更新会员信息就会失败，我们需要一个地方，来暂时存放无法被消费的



<img src="https://pic4.zhimg.com/80/v2-518cb0c3a60644df252245485ae643af_720w.jpg" alt="img" style="zoom:50%;" />



当订单系统创建完订单时，它只需要往队列里，塞入（push）一条topic为“order_created”的消息。
接着，我们的nsq1.0，会把这条消息，**再推送给所有订阅了这个topic的消息的机器，告诉他们，“有新的订单，你们该干嘛干嘛”。**





---





**消费者组(channel)：**会员系统的三个实例，当它们收到消息时，要做的事情是一样的，并且只需要有有一个实例执行，那么它们就是一个消费者组里面的，要标识为同一个channel。



---



nsqlookup: 



一条消息是如何从生产到被消费的？？？

- 启动各个服务，生产者（producer）、消费者（consumer）、nsq、nsqlookup.

- 假设**consumer最先启动**，需要消费topic消息，此时consumer向nsqlookup调用lookup接口，试图获取对应topic的的nsq。nsq还未启动，获取失败。不过并不影响消费者的启动流程，因为每隔一段时间会尝试拉去新数据

- **启动了nsq和nsqlookup**，此时consumer可以调通nsqlookup接口，不过由于nsq上面没有任何topic，因此lookup返回的producer数组是空的，因此消费者仍然无法向任何nsq订阅消息

- 在**nsq上创建新的topic**，nsq创建完topic后，会自动向nsqlookup注册新的topic节点。

- 当consumer下次过来调用nsqlook过来nsqlookup时，接口会告诉它，已经有一台nsq，上面有topic指令。consumer拿到nsq的ip和端口，建立连接。订阅这台nsq上面的消息。

  注：**当消费者的channel不存在时，nsq将会创建一条新的channel**。和之前创建topic类似，创建完channel，nsq会向nsqlookup注册新的channel节点。不一样的是，**channel会在订阅时，自动创建，而topic，需要我们事先在nsq创建好。**

- **启动producer**，producer向nsq发布了topic为“order_created”的消息。

- nsq把消息复制到topic下的所有channel中，每个channel复制一份，接着channel向和它建立连接的其中一个consumer实例，推送这条消息。

---









# nsq架构

<img src="https://pic2.zhimg.com/v2-b266260a17702af4509f6c7d2ba40d4a_r.jpg" alt="preview" style="zoom:200%;" />

## topic消息的逻辑关键词

- **topic** 是 **NSQ** 消息发布的 **逻辑关键词** ，可以理解为人为定义的一种消息类型。当程序初次发布带 **topic** 的消息时,如果 **topic** 不存在,则会在 ***nsqd\***中创建。



## producer消息的生产者/发布者

- **producer** 通过 **HTTP API** 将消息发布到 **nsqd** 的指定 **topic** ，一般有 **pub/mpub** 两种方式， **pub** 发布一个消息， **mpub** 一个往返发布多个消息。
- **producer** 也可以通过 **nsqd客户端** 的 **TCP接口** 将消息发布给 **nsqd** 的指定 **topic** 。
- 当生产者 **producer** 初次发布带 **topic** 的消息给 **nsqd** 时,如果 **topic** 不存在，则会在 **nsqd** 中创建 **topic** 。



## channel消息传递的通道

- 当生产者每次发布消息的时候,消息会采用多播的方式被拷贝到各个 ***channel\*** 中, ***channel\*** 起到队列的作用。
- ***channel\*** 与 ***consumer(消费者)\*** 相关，是消费者之间的负载均衡,消费者通过这个特殊的channel读取消息。
- 在 ***consumer\*** 想单独获取某个 **topic** 的消息时，可以 ***subscribe(订阅)\***一个自己单独命名的 ***nsqd\***中还不存在的 ***channel\***, ***nsqd\***会为这个 ***consumer\***创建其命名的 ***channel\***
- ***Channel\*** 会将消息进行排列，如果没有 ***consumer\***读取消息，消息首先会在内存中排队，当量太大时就会被保存到磁盘中。可以在配置中配置具体参数。
- 一个 ***channel\*** 一般会有多个 ***consumer\*** 连接。假设所有已连接的 ***consumer\*** 处于准备接收消息的状态，每个消息将被传递到一个随机的 ***consumer\***。
- Go语言中的channel是表达队列的一种自然方式，因此一个NSQ的topic/channel，其核心就是一个存放消息指针的Go-channel缓冲区。缓冲区的大小由 --mem-queue-size 配置参数确定。



## consumer消息的消费者

- ***consumer\*** 通过 **TCP*****subscribe\*** 自己需要的 ***channel\***
  ***topic\*** 和 ***channel\*** 都没有预先配置。 ***topic\*** 由第一次发布消息到命名 ***topic\*** 的 ***producer\*** 创建 ***或\*** 第一次通过 ***subscribe\*** 订阅一个命名 ***topic\*** 的 ***consumer\*** 来创建。 ***channel\*** 被 ***consumer\*** 第一次 ***subscribe\*** 订阅到指定的 ***channel\*** 创建。
- 多个 ***consumer\******subscribe\***一个 ***channel\***，假设所有已连接的客户端处于准备接收消息的状态，每个消息将被传递到一个 **随机** 的 ***consumer\***。
- NSQ 支持延时消息， ***consumer\*** 在配置的延时时间后才能接受相关消息。
- Channel在 ***consumer\*** 退出后并不会删除，这点需要特别注意。

----

