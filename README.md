# MQTTv5 for AWS IoT介绍和DEMO


# 以下AWS物联网服务支持MQTT v5

* AWS IoT Core

* AWS IoT Core Device Advisor

* AWS IoT Greengrass

# Request/Response–请求/应答

标准化 MQTT 的请求/应答模型，通过 Response Topic 和 Correlation Data 属性，使得 Response 消息可 以正确路由回 Request 的发布者。同时，为客户端从 Server 获取构造 Response Topic 的配置的能力。

![](img/MQTTv5%20for%20AWS%20IoT0.png)

## [Demo1: 使用请求/响应模式发送更透明的消息](mqttv5.ipynb)


![](img/MQTTv5%20for%20AWS%20IoT2.png)


# User Properties – 用户属性

用户属性功能允许连接的设备或订阅者应用程序通过将自定义键值对附加到MQTT数据包（包括发布和连接）来传递自定义信息。

该功能与HTTP标头提供了类似的功能，只要标头中不超过8KB的总大小，就可以使用。

## [Demo2:具有用户属性功能的更灵活的设备消息传递](mqttv5.ipynb)

# Topic Alias-主题别名

通过将主题名称映射为一个数字\(主题别名\)从而降低消息的传输、处理开销。客户端和 Server 可以各自指定允许的主题别名的数量

## [Demo3:使用主题别名功能更有效地使用设备带宽](mqttv5.ipynb)

# Clean Start and Session Expiry

| 参数值 | 描述 |
| :-: | :-: |
| Clean Start= 1 | 创建一个新的会话，如果存在前一个会话，则终止其存在。 |
| Clean Start= 0 | 如果以前的会话存在，则恢复一个会话。 |
| Session Expiry Interval> 0 | 保持一个会话。 |
| Session Expiry interval= 0 | 不保持会话。 |

在MQTT 5中，如果你设置Clean Start = 1和Session Expiry Interval = 0，这就相当于MQTT 3的洁净会话\(clean session\)。如果你设置Clean Start = 0和Session Expiry Interval> 0，这就相当于MQTT 3的持久性会话\(persistent session\)

# Message Expiry – 消息过期

你可以在发布的信息中添加信息过期值。这些值代表消息的过期时间间隔，单位是秒。如果消息在该时间间隔内没有被发送给订阅者，该消息将过期并被删除。如果你不设置消息过期值，消息将不会过期。

## [Demo4:使用消息过期、会话过期和Clean Start功能更好地控制设备行为](mqttv5.ipynb)

![](img/MQTTv5%20for%20AWS%20IoT3.png)

# Server Disconnect–Server断开

允许 Server 侧发送 DISCONNECT 控制包，以指示断开的原因。

# Reason code on all ACKs – 所有控制包包含原因码

更改所有控制包都包含一个 Reason Code，这些控制包包括:CONNACK，PUBACK，SUBACK，UNSUBACK，DISCONNECT 。这允许参与者是否继续请求的功能。可以查看[详细的控制包的原因码](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html#mqtt5-reason-codes)。

## [Demo5:使用原因代码和服务器断开功能增强了设备连接流程](mqttv5.ipynb)

# Payload format and content type -  载荷格式和内容类型

允许指定发布消息的载荷格式\(binary，text\)和 MIME 风格的内容类型。这两个属性会被转发给订阅方。

# 最大封包大小

允许客户端和服务器独立指定它们支持的最大数据包大小。 会话参与方发送较大的数据包是会报错的。

# Server保活

允许服务器指定希望客户端用作保持活动状态的值。 这允许服务器设置最大允许的保持活动状态 ，并且客户端遵守它。

# Reason string on all ACKs – 所有控制包包含原因字符串

更改所有控制包都包含一个 Reason Code 以及可选的提示语。它用于问题诊断，接收方不必解析其内 容。

# Shared Subscriptions – 共享订阅

为支持多个订阅消费者的负载均衡，支持共享订阅

目前AWS IoT暂时不支持

# 订阅ID

允许在 SUBSCRIBE 中指定一个数字类型的订阅标识符，并在消息分发时返回标识符。这使得客户端 在收到被传递的消息时，得知属于哪个或哪些订阅。

# 流控

允许 Client 和 Server 各自指定外发在途需要确认的控制包的数量。当到达发送配额时，发送方暂定发送。这用来限制可靠消息的速率/频率，限制在途控制包的数目。

# 可选的服务端功能可用性

定义服务器不允许的一组功能，并提供一种机制，以便服务器将其指定给客户端。 可以通过这种方 式指定的功能包括:最大 QoS，保留可用，通配符订阅可用，订阅标识符可用和共享订阅可用。 客 户端使用服务器声明的不可用功能是错误的。

在 MQTT 的早期版本中，服务器可能会通过声明客户端未获得该功能的授权而未实现该功能。 此功能允许声明这种可选行为，并在客户端无论如何使用这些功能之一时添加特定的原因代码。

# 增强认证

提供一种机制来启用质询/响应形式的身份验证，包括相互身份验证。 如果客户端和服务器都支持， 这允许使用 SASL 样式身份验证，并且包括客户端在连接内重新身份验证的功能。

# 订阅选项

提供主要定义为允许消息桥接应用程序的订阅选项。 这些选项包括不发送源自此客户端的消息的选项\(noLocal\)，以及在订阅时处理保留消息的选项。

# 遗嘱延迟

添加了指定连接结束与发送遗嘱消息之间的延迟的功能。 这样设计的目的是，如果重新建立与会话的连接，则不会发送遗嘱消息。 这样可以在不通知他人的情况下短暂中断连接。

# 分配ClientID

如果由服务器分配了 ClientID，则返回分配的 ClientID。 这也解除了服务器分配的 ClientID 仅可用于 Clean Session = 1 连接的限制。

# 参考 Server

允许服务器指定要在 CONNACK 或 DISCONNECT 上使用的备用服务器。 这可以用作重定向或进行配置。

# AWS IoT和MQTT技术规范的区别

* AWS IoT不支持MQTT 3的以下数据包：PUBREC\, PUBREL\, and PUBCOMP\.

* AWS IoT不支持MQTT 5的以下数据包：PUBREC、PUBREL、PUBCOMP和AUTH。

* AWS IoT仅支持MQTT服务质量（QoS）级别0和1。AWS IoT不支持以QoS级别2发布或订阅。当请求QoS级别2时，消息代理不发送PUBACK或SUBACK。

* 在AWS IoT中，以QoS级别订阅一个主题意味着一个消息会被传送0次或更多次。一条消息可能会被交付一次以上。传递超过一次的消息可能会以不同的数据包ID发送。在这些情况下，DUP标志不被设置。

* 当响应一个连接请求时，消息代理会发送一个CONNACK消息。该消息包含一个标志，以表明连接是否在恢复以前的会话。

* 在发送额外的控制包或断开连接请求之前，客户端必须等待他们的设备从AWS IoT消息代理那里收到CONNACK消息。

* 当客户端订阅一个主题时，在消息代理发送SUBACK和客户端开始接收新的匹配消息的时间之间可能会有延迟。

* 当客户端在主题过滤器中使用通配符\#来订阅一个主题时，其在主题层次结构中的所有字符串和下面的字符串都被匹配。然而，父级话题不被匹配。例如，对话题sensor/\#的订阅会收到发布到话题sensor/、sensor/temperature、sensor/temperature/room1的消息，但不会收到发布到sensor的消息。

* 在极少数情况下，消息代理可能会用不同的数据包ID重新发送同一个逻辑PUBLISH消息。

* 消息代理使用客户端ID来识别每个客户端。客户端ID作为MQTT有效载荷的一部分从客户端传给消息代理。具有相同客户端ID的两个客户端不能同时连接到消息代理。当一个客户端使用另一个客户端正在使用的客户端ID连接到消息代理时，新的客户端连接会被接受，之前连接的客户端会被断开。

* 订阅包含通配符的主题过滤器不能接收保留消息。要接收保留的消息，订阅请求必须包含一个与保留的消息主题完全匹配的主题过滤器。

* 消息代理不保证消息和ACK的接收顺序。

* AWS IoT可能有与规范不同的限制。参阅AWS IoT参考指南中的AWS IoT核心消息代理和协议限制和配额。
