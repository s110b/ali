
技术点 - [科普介绍]
从zuul1 -> springCloudGateway           5分钟   

# API网关
微服务架下，服务之间容易形成网状的调用关系，这种网状的调用关系不便管理和维护，这种场景下API网关应运而生。作为后端服务的入口，API网关在微服务架构中尤其重要，在对外部系统提供API入口的要求下，API网关应具备路由转发、负载均衡、限流熔断、权限控制、轨迹追踪和实时监控等功能。
![](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653298581254-e6b9117e-693d-408f-8135-36b27419f5bd.png#clientId=u1c785dbd-d8dc-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u4ccc58e3&margin=%5Bobject%20Object%5D&originHeight=378&originWidth=725&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u6740e91c-002d-424c-9c4c-59f5a704665&title=)

目前，很多微服务都基于的Spring Cloud生态构建。Spring Cloud生态为我们提供了两种API网关产品，分别是Netflix开源的Zuul1和Spring自己开发的Spring Cloud Gateway（下边简称为Gateway）。Spring Cloud以Finchley版本为分界线，Finchley版本发布之前使用Zuul1作为API网关，之后更推荐使用Gateway。

虽然Netflix已经在2018年5月开源了Zuul2，但是Spring Cloud已经推出了Gateway，并且在github上表示没有集成Zuul2的计划。所以从Spring Cloud发展的趋势来看，Gateway代替Zuul是必然的。

## Zuul1简介
Zuul1是Netflix在2013年开源的网关组件，大规模的应用在Netflix的生产环境中，经受了实践考验。它可以与Eureka、Ribbon、Hystrix等组件配合使用，实现路由转发、负载均衡、熔断等功能。Zuul1的核心是一系列过滤器，过滤器简单易于扩展，已经有一些三方库如spring-cloud-zuul-ratelimit等提供了过滤器支持。

Zuul1基于Servlet构建，使用的是阻塞的IO，引入了线程池来处理请求。每个请求都需要独立的线程来处理，从线程池中取出一个工作线程执行，下游微服务返回响应之前这个工作线程一直是阻塞的。

## Spring Cloud Gateway简介
Spring Cloud Gateway 是Spring Cloud的一个全新的API网关项目，目的是为了替换掉Zuul1。Gateway可以与Spring Cloud Discovery Client（如Eureka）、Ribbon、Hystrix等组件配合使用，实现路由转发、负载均衡、熔断等功能，并且Gateway还内置了限流过滤器，实现了限流的功能。

Gateway基于Spring 5、Spring boot 2和Reactor构建，使用Netty作为运行时环境，比较完美的支持异步非阻塞编程。Netty使用非阻塞的IO，线程处理模型建立在主从Reactors多线程模型上。其中Boss Group轮询到新连接后与Client建立连接，生成NioSocketChannel，将channel绑定到Worker；Worker Group轮询并处理Read、Write事件。

# 对比
## 2.0 产品对比
下边以表格形式对Zuul1和Gateway作简单对比：
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653015529964-83fd95bf-7d7b-4b28-af31-75dd78ab0c01.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=539&id=uc2376ee4&margin=%5Bobject%20Object%5D&name=image.png&originHeight=668&originWidth=1225&originalType=binary&ratio=1&rotation=0&showTitle=false&size=55814&status=done&style=none&taskId=udb307b81-c502-4bb6-bd86-477d49c40b8&title=&width=988.2352624385012)
:::info
**表格中的四个问题大家考虑下**
什么是非阻塞？
gateway如何实现大流量项目？
如何实现限流？
门槛高在哪里？
长连接？
:::

##  性能对比
### 低并发场景
不同的tps，同样的请求时间（50s），对两种网关产品进行压力测试，结果如下：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653015838652-a3ff8421-8517-453b-96d2-96ae63bbc3b9.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=165&id=u98fee56d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=205&originWidth=1234&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25330&status=done&style=none&taskId=u20717c66-7115-4ba6-a8da-b1fa0c33ba7&title=&width=995.4957664074371)

并发较低的场景下，两种网关的表现差不多

### 高并发场景
配置同样的线程数（2000），同样的请求时间（5分钟），后端服务在不同的响应时间（休眠时间），对两种网关产品进行压力测试，结果如下：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653015856391-3220b5d7-b9e9-4725-9ec2-14162512c8b0.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=336&id=u8efce5fe&margin=%5Bobject%20Object%5D&name=image.png&originHeight=417&originWidth=1224&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43950&status=done&style=none&taskId=u25b287a8-45d2-4778-88b8-a779ed9d9c6&title=&width=987.4285397752861)

Zuul网关的tomcat最大线程数为400，hystrix超时时间为100000。

Gateway在高并发和后端服务响应慢的场景下比Zuul1的表现要好。

### 官方性能对比
Spring Cloud Gateway的开发者提供了benchmark项目用来对比Gateway和Zuul1的性能，官方提供的性能对比结果如下：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653015909195-19b714b8-37b4-4dea-97ef-99cd60debd9f.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=184&id=ufe333ba8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=228&originWidth=1233&originalType=binary&ratio=1&rotation=0&showTitle=false&size=12245&status=done&style=none&taskId=ua634d075-7ec7-4a27-a8ae-11a285281f4&title=&width=994.6890437442221)
测试工具为wrk，测试时间30秒，线程数为10，连接数为200。
从官方的对比结果来看，Gateway的RPS是Zuul1的1.55倍，平均延迟是Zuul1的一半。
# 总结
Zuul1的开源时间很早，Netflix、Riot、携程、拍拍贷等公司都已经在生产环境中使用，自身经受了实践考验，是生产级的API网关产品。
Gateway在2019年离开Spring Cloud孵化器，应用于生产的案例少，稳定性有待考证。
从性能方面比较，两种产品在流量小的场景下性能表现差不多；并发高的场景下Gateway性能要好很多。从开发方面比较，Zuul1编程模型简单，易于扩展；Gateway编程模型稍难，代码阅读难度要比Zuul高不少，扩展也稍复杂一些。

响应式编程（网关所用的框架webflux简介）    【基石】                                            5分钟
# 响应式编程框架
	RxJava2
	webflux
	集成 Netty 作为默认的 web 服务器，支持 reactive 应用
	• WebFlux 默认运⾏行行在 Netty上

# 什么是响应式编程
## 响应式编程定义
        reactive programming is a declarative programming paradigm concerned with data streams and the propagation of change。而这个定义也约定了

        对于响应式编程，有很多种解释，比如很多人举例的excel表格的例子，Excel有函数Avg和Sum，当某一列的数据变化後，其对应的Sum函数也会相应的变化，也就是变化传递的思想。类似，一个数据流，当一个数据改变之后，其对应的函数值也会相应变化。举个例子，java8中的IntStream.of(nums).parallel().sum()这种函数，这是一种数据流式处理的思想，但是这是缺少响应式编程定义的propagation of change变化传递，因此不能算响应式。

        java9中新增了响应式编程支持，这里响应式流(Reactive Streams)通过定义一组实体，接口和互操作方法，给出了实现异步非阻塞背压的标准。第三方遵循这个标准来实现具体的解决方案，常见的有Reactor，RxJava，
## Spring对Reactive的解释
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653016826510-ce5687a3-ea14-465a-92b0-09e476a63e0c.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u401b2830&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1234&originWidth=2452&originalType=url&ratio=1&rotation=0&showTitle=false&size=354465&status=done&style=none&taskId=ud25a7f56-b35d-49ef-a47f-8745b92554f&title=)
 
## Reactivestream中响应式编程
        org.reactivestreams中reactive-stream对响应式编程有了关键的接口定义。先上类结构图，从下面的类图中可以发现，这个思想怎么和消息中间件有点类似呢，一个发布者Publisher，一个订阅者Subscriber，一个Processor和Kafka中的broker也可以对应起来，这里在细看Processor的接口定义会发现，其就是将Subscriber和Publisher串起来的关键角色。
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653016843048-96ec5ab7-7a5a-4933-a713-e6832eced205.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u6651e4a5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=976&originWidth=1264&originalType=url&ratio=1&rotation=0&showTitle=false&size=152327&status=done&style=none&taskId=udc3e938d-07e8-4c81-906c-dad9d5ef30c&title=)
:::info

- Publisher（发布者)相当于生产者(Producer)
- Subscriber(订阅者)相当于消费者(Consumer)
- Processor就是在发布者与订阅者之间处理数据用的

        在响应式流上提到了back pressure（背压)这么一个概念，其实非常好理解。在响应式流实现异步非阻塞是基于生产者和消费者模式的，而生产者消费者很容易出现的一个问题就是：生产者生产数据多了，就把消费者给压垮了。

而背压说白了就是：消费者能告诉生产者自己需要多少量的数据。这里就是Subscription接口所做的事。
:::
 
## java9中响应式编程
java9中引入了Flow类，这个类就是对响应式编程规范的具体支持，其组合关系和内部方法如下，可以看到和reactivestream几乎一模一样，这里就不做过多描述。
 ![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653016910227-f8df3df3-683b-4a27-b9b5-8edd3accca17.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u19e2380d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=788&originWidth=1738&originalType=url&ratio=1&rotation=0&showTitle=false&size=127318&status=done&style=none&taskId=u03b4be09-af02-453d-9a5f-0b586ebcc01&title=)
什么是Reactive
        看过SpringGateway的源码会发现，里面大量使用reactive思路和reactivestreams里面的类。

        而reactor.core是对reactivestreams中定义的接口进行实际实现，核心的几个类如Mono、Flux，待会我们介绍，先看一下类图
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653016933023-5309ef00-68e9-4b44-89d2-2609e0fcc017.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u5f215917&margin=%5Bobject%20Object%5D&name=image.png&originHeight=778&originWidth=1524&originalType=url&ratio=1&rotation=0&showTitle=false&size=112284&status=done&style=none&taskId=uf28c54cc-7049-4889-a3bc-9bf4527db8b&title=)
Reactive流的主要特征是“back pressure后压”：也就是说，系统会在它的请求buffer被充满时，将其推送会给发送者，让发送者稍后再试，或者使用其他接收器，这就能确保发送者和接收者之间的管道不会被充满，这样才有机会获得一个响应式系统。
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653016944454-e3eb75be-84f4-4857-b38b-fbf727a3be4e.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=ue9099a24&margin=%5Bobject%20Object%5D&name=image.png&originHeight=316&originWidth=1312&originalType=url&ratio=1&rotation=0&showTitle=false&size=215210&status=done&style=none&taskId=u65c74e1a-dd62-4f62-99a4-71a6d61305e&title=)

## Reactive之Mono类
Mono 是一个发出(emit)0-1个元素的Publisher<T>,可以被onComplete信号或者onError信号所终止。 
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653016958002-731324b0-05da-4dfe-be01-aaa60989ce4c.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u6cff95a8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=419&originWidth=890&originalType=url&ratio=1&rotation=0&showTitle=false&size=42065&status=done&style=none&taskId=u94f4ce8e-a933-424c-b62a-aa5c6ad83f1&title=)
Reactive之Flux类
        Flux 是一个发出(emit)0-N个元素组成的异步序列的Publisher<T>,可以被onComplete信号或者onError信号所终止。在响应流规范中存在三种给下游消费者调用的方法 onNext, onComplete, 和onError。下面这张图表示了Flux的抽象模型：
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653016973108-cb06d38b-584b-4bdd-9a53-ab307b34d0cc.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u5aab3b09&margin=%5Bobject%20Object%5D&name=image.png&originHeight=419&originWidth=890&originalType=url&ratio=1&rotation=0&showTitle=false&size=42065&status=done&style=none&taskId=ufa0ed3ec-ee0f-400a-94fe-a902b79aa57&title=)
# 什么是WebFlux
        Spring 5新加入的响应式流编程技术栈是其主打核心特性，最低Springbooot2.0，底层使用Netty，这个从搭建好项目启动日志中能看出来。

## WebFlux的几个关键概念
1.Router Functions: 对标@Controller，@RequestMapping等标准的Spring MVC注解，提供一套函数式风格的API，用于创建Router，Handler和Filter。

2.WebFlux: 核心组件，协调上下游各个组件提供响应式编程支持。

3.Reactive Streams: 一种支持背压（Backpressure）的异步数据流处理标准，主流实现有RxJava和Reactor，Spring WebFlux默认集成的是Reactor,
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653017005280-c4f9aa14-20da-4f01-877f-a6ed0791329f.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=uc030b63f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1176&originWidth=1558&originalType=url&ratio=1&rotation=0&showTitle=false&size=176328&status=done&style=none&taskId=ud49848a0-ac47-46b2-8f1e-58c6c203f86&title=)
## 为什么需要WebFlux
        很多朋友会说我们有了异步非阻塞，为什么还需要webFlux，这里的具体原因有以下几点。

        WebFlux使用的是Reactor响应式流，里边提供了一系列的API供我们去处理逻辑，而WebFlux使用起来可以像使用SpringMVC一样，同时WebFlux也可以使用Functional Endpoints方式编程，总的来说还是要比回调/CompletableFuture要简洁和易编写。

        Spring WebFlux在应对高并发的请求时，借助于异步IO，能够以少量而稳定的线程处理更高吞吐量的请求，尤其是当请求处理过程如果因为业务复杂或IO阻塞等导致处理时长较长时，对比更加显著。

        使用WebFlux的好处是只需要在程序内启动少量线程扩展，而不是水平通过集群扩展。异步能够规避文件IO/网络IO阻塞所带来的线程堆积。
## WebFlux实际场景
:::info
SpringMVC和WebFlux更多的是互补关系，而不是替换。阻塞的场景该SpringMVC还是SpringMVC，并不是WebFlux出来就把SpringMVC取代了。
:::
![image.png](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653017070097-570c209c-6e2e-43e5-b76a-f304cfdcf130.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=uab967c49&margin=%5Bobject%20Object%5D&name=image.png&originHeight=463&originWidth=828&originalType=url&ratio=1&rotation=0&showTitle=false&size=136616&status=done&style=none&taskId=ucc8c8bde-d479-4321-9963-1b4345e2b03&title=)

- **WebFlux需要非阻塞的业务代码**，如果阻塞，需要自己开线程池去运行。WebFlux什么场景下可以替换SpringMVC呢？
- **想要内存和线程数较少的场景**
- **网络较慢或者IO会经常出现问题的场景**

总结： 只适合特定场景。个人感觉还是由于java的线程太重，go语言不需要这种设计。
# 网关常用功能实现 方式  
路由                3分钟
限流算法        3分钟
## 限流算法
**计数器算法**
计数器算法采用计数器实现限流有点简单粗暴，一般我们会限制一秒钟的能够通过的请求数，比如限流qps为100，算法的实现思路就是从第一个请求进来开始计时，在接下去的1s内，每来一个请求，就把计数加1，如果累加的数字达到了100，那么后续的请求就会被全部拒绝。等到1s结束后，把计数恢复成0，重新开始计数。具体的实现可以是这样的：对于每次服务调用，可以通过AtomicLong#incrementAndGet()方法来给计数器加1并返回最新值，通过这个最新值和阈值进行比较。这种实现方式，相信大家都知道有一个弊端：如果我在单位时间1s内的前10ms，已经通过了100个请求，那后面的990ms，只能眼巴巴的把请求拒绝，我们把这种现象称为“突刺现象”
**漏桶算法**
漏桶算法为了消除"突刺现象"，可以采用漏桶算法实现限流，漏桶算法这个名字就很形象，算法内部有一个容器，类似生活用到的漏斗，当请求进来时，相当于水倒入漏斗，然后从下端小口慢慢匀速的流出。不管上面流量多大，下面流出的速度始终保持不变。不管服务调用方多么不稳定，通过漏桶算法进行限流，每10毫秒处理一次请求。因为处理的速度是固定的，请求进来的速度是未知的，可能突然进来很多请求，没来得及处理的请求就先放在桶里，既然是个桶，肯定是有容量上限，如果桶满了，那么新进来的请求就丢弃。
![](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653017675512-a6b3f27a-5ab1-4219-ad86-e9ea5f9750ac.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=udad864fe&margin=%5Bobject%20Object%5D&originHeight=299&originWidth=443&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u46583bb4-74bd-4f1d-8d40-30877c851cd&title=)
在算法实现方面，可以准备一个队列，用来保存请求，另外通过一个线程池（ScheduledExecutorService）来定期从队列中获取请求并执行，可以一次性获取多个并发执行。
这种算法，在使用过后也存在弊端：无法应对短时间的突发流量。
**令牌桶算法**
从某种意义上讲，令牌桶算法是对漏桶算法的一种改进，桶算法能够限制请求调用的速率，而令牌桶算法能够在限制调用的平均速率的同时还允许一定程度的突发调用。在令牌桶算法中，存在一个桶，用来存放固定数量的令牌。算法中存在一种机制，以一定的速率往桶中放令牌。每次请求调用需要先获取令牌，只有拿到令牌，才有机会继续执行，否则选择选择等待可用的令牌、或者直接拒绝。放令牌这个动作是持续不断的进行，如果桶中令牌数达到上限，就丢弃令牌，所以就存在这种情况，桶中一直有大量的可用令牌，这时进来的请求就可以直接拿到令牌执行，比如设置qps为100，那么限流器初始化完成一秒后，桶中就已经有100个令牌了，这时服务还没完全启动好，等启动完成对外提供服务时，该限流器可以抵挡瞬时的100个请求。所以，只有桶中没有令牌时，请求才会进行等待，最后相当于以一定的速率执行。
![](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653017675605-053070c7-f484-47c6-b2f7-8d37468f6082.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u23e2b8d2&margin=%5Bobject%20Object%5D&originHeight=779&originWidth=700&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ucd1d0857-eafc-4732-8ecd-9b0c68e0166&title=)
实现思路：可以准备一个队列，用来保存令牌，另外通过一个线程池定期生成令牌放到队列中，每来一个请求，就从队列中获取一个令牌，并继续执行。



常识介绍：3分钟
# http及长连接    
## **短连接**
HTTP 协议最初（0.9/1.0）是个非常简单的协议，通信过程也采用了简单的“请求 - 应答”方式。 
它底层的数据传输基于 TCP/IP，每次发送请求前需要先与服务器建立连接，收到响应报文后会立即关闭连接。 
因为客户端与服务器的整个连接过程很短暂，不会与服务器保持长时间的连接状态，所以就被称为“短连接”（short-lived connections）。早期的 HTTP 协议也被称为是“无连接”的协议。 
短连接的缺点相当严重，因为在 TCP 协议里，建立连接和关闭连接都是非常“昂贵”的操作。TCP 建立连接要有“三次握手”，发送 3 个数据包，需要 1 个 RTT；关闭连接是“四次挥手”，4 个数据包需要 2 个 RTT。 
而 HTTP 的一次简单“请求 - 响应”通常只需要 4 个包，如果不算服务器内部的处理时间，最多是 2 个 RTT。这么算下来，浪费的时间就是“3÷5=60%”，有三分之二的时间被浪费掉了，传输效率低得惊人。 
![](https://cdn.nlark.com/yuque/0/2022/png/26146148/1653017414662-e4cc26d3-bdbf-4822-9a94-b63b18eb6419.png#clientId=uf6dbc22d-8751-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u2386f990&margin=%5Bobject%20Object%5D&originHeight=1585&originWidth=1100&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u9bdb0abb-e650-4074-a883-eacaad5886f&title=)
## **长连接**
针对短连接暴露出的缺点，HTTP 协议就提出了“长连接”的通信方式，也叫“持久连接”（persistent connections）、“连接保活”（keep alive）、“连接复用”（connection reuse）。 
其实解决办法也很简单，用的就是“成本均摊”的思路，既然 TCP 的连接和关闭非常耗时间，那么就把这个时间成本由原来的一个“请求 - 应答”均摊到多个“请求 - 应答”上。 
这样虽然不能改善 TCP 的连接效率，但基于“分母效应”，每个“请求 - 应答”的无效时间就会降低不少，整体传输效率也就提高了。 
在短连接里发送了三次 HTTP“请求 - 应答”，每次都会浪费 60% 的 RTT 时间。而在长连接的情况下，同样发送三次请求，因为只在第一次时建立连接，在最后一次时关闭连接，所以浪费率就是“3÷9≈33%”，降低了差不多一半的时间损耗。显然，如果在这个长连接上发送的请求越多，分母就越大，利用率也就越高。

并发测量工具 （实验）  3分钟
实验长连接，短链接 并发量。
wrk,ab  
抓包工具    查看时间消耗  3分钟
Wireshark ,tcpdump 

过滤器
限流算法
实验方式
并发测量工具及方法
wrk,ab


[_E9_AB_98_E6_80_A7_E8_83_BDJava_E5_BA_94_E7_94_A8_E5_B1_82_E7_BD_91_E5_85_B3_E8_AE_BE_E8_AE_A1.pdf](https://yuncodeweb.oss-cn-hangzhou.aliyuncs.com/uploads/xiquwugou/source/50c0236d884151460c5b3952c28d3a54/%E9%AB%98%E6%80%A7%E8%83%BDJava%E5%BA%94%E7%94%A8%E5%B1%82%E7%BD%91%E5%85%B3%E8%AE%BE%E8%AE%A1.pdf)
