---
title: "Connect路由原理以及使用队列实现基于技能的路由"
author: "Mavlarn"
description : "Amazon Connect路由的原理，以及使用队列实现基于坐席技能的路由"
date: 2023-03-19T10:13:17+08:00
date: 2023-03-21T10:13:17+08:00
weight: 3

tags : [                                    
"Amazon Connect", "Agent"
]
categories : [                              
"Amazon Connect",
]
keywords : [                                
"Amazon Connect", "Agent", "queue"
]

---

Amazon Connect使用队列(Queue)和路由配置(Routing Profile)实现基于坐席技能的路由。坐席-路由配置-队列之间的关系如下：

![](1-agent-routingprofile-queue.jpg?width=600px&featherlight=false)

每个坐席用户(User)都有一个 *RoutingProfile(路由配置)*，而每个 *RoutingProfile* 都跟多个队列(Queue)关联。

当一个联系电话接入以后：
1. 会由跟电话号码关联的联系流 *Contact Flow* 处理，在这个 *Contact Flow* 中，会设置要转到某个队列（也可能直接转给某个Agent）。
2. 根据 Queue 和 *Routing Profile* 和 Agent 的关系，该来电会被自动分发给下一个可用的 Agent（处于空闲时间最长的 Agent）。
3. 也可以通过设置工作时间 *hours of operation*，来设置某个队列的工作时间，只有在工作时间内，来电才会转接到队列内的坐席。

## 创建队列

首先，我们需要为不同的用户类型，或者技能组，创建不同的队列。进入 Connect 的管理界面，从左侧的菜单找到 Routing - Queues，然后点击添加创建新的队列：
![](2-queue-create.jpg?width=800px&featherlight=false)

这里输入队列名称和描述，一般还需要设置 *Outbound caller configuration*，这时设置在外呼的时候，外呼使用的号码和流程。其中有些地区还需要设置 *Caller ID*。

*Quick connects* 就是可以从这个队列的来电进行转接时，能够转接到哪些 *quick connect*。

最后，也可以设置 Tags 标签，这些标签用于进行基于标签的权限控制，也可以在查询指标数据的时候，根据标签进行查询。

## 修改或创建 Routing Profile

接下来就是创建 *Routing Profile*，在管理界面，找到菜单 Users - Routing Profiles，创建新的配置：
![](3-routing-profile-create.jpg?width=800px&featherlight=false)
需要的配置如下：
1. 名称和描述
2. Set channels and concurrenc，设置可以通过该队列，接收哪些类型的联系请求。对于 Voice(电话) 类型，同时只能接听一路电话，而队列文本消息和任务，可以设置最多可以同时处理的数量。
3. Queues，设置该配置关联的队列，可以设置多个，并配置每个队列里能接收的channels，以及优先级、延时。通过这个优先级可以将不同级别的客户联系转发到不同级别的队列，坐席在接听的时候，会优先接听高优先级的（数字越低优先级越高），如果优先级一样，则优先处理延时低的。
4. Default outbound queue，即默认呼出队列，当通过配置或API进行自动外呼时，如果需要坐席接听外呼的电话，则有这个来配置由哪个队列的坐席接听外呼时的电话。
5. Tags，即标签，与上面的一样，用于基于标签的权限控制和指标查询。

## 设置 User - Routing Profile
然后，从菜单 Users - User management 进入用户管理列表，找到要设置的用户，设置关联的 *Routing Profile* ：
![](4-user-routing-profile.jpg?width=800px&featherlight=false)


## 配置 Contact Flow
最后，需要在 *Contact Flow* 里配置要转发的队列，最基本的 *Contact Flow* 如下所示：
![](2-basic-contact-flow.jpg?width=800px&featherlight=false)

这里首先通过 *Set working queue* 设置要转发的队列，一般我们可以在这里配置要转发到哪个队列，比如下面就是转发到 *BasicQueue* 队列：
![](2-basic-contact-flow-set-queue1.jpg?width=900px&featherlight=false)

这里也可以动态的设置目标的队列，或者直接设置某一个Agent。

然后再通过 *Transfer to queue* 转移，最后一个 *Disconnect* 的Block表示这个流程的自动的部分结束，这时该队列里的坐席就会弹出电话，进行接听。
