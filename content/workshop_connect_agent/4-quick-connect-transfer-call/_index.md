---
title: "通过Quick Connect实现电话转接"
author: "Mavlarn"
description : "通过Quick Connect实现电话转接"
date: 2023-03-20T10:45:17+08:00
weight: 4

tags : [
"Amazon Connect", "Agent"
]
categories : [
"Amazon Connect"
]
keywords : [
"Amazon Connect", "Agent", "Quick Connect"
]

---

在 Amazon Connect 中，来电（或者消息）的转接，是通过 *Quick Connect* 的功能完成。在坐席的CCP界面，会有一个 *Quick Connect* 的按钮，坐席在接听电话时，可以讲来电转接到其他的坐席、队列、或者外部号码；如果坐席没有在接听，也可以通过 *Quick Connect* 呼叫外部号码。

> 有关 Quick Connect 的工作原理和如何创建，可以参考[官方文档](https://docs.aws.amazon.com/connect/latest/adminguide/how-quick-connects-work.html)

## Quick Connect 基本原理

Quick Connect 就是指定一个预定义的目标，这样坐席就可以通过选择这个预定义的 *Quick Connect* 进行外呼或者电话转接。

### Quick Connect 类型

根据 *Quick Connect* 的目标不同，有3种类型的 *Quick Connect*。

1. Phone Number
即一个电话号码，可以将当前来电转接到该电话上。*Phone Number* 类型的 *Quick Connect* 可以在坐席接听状态，或者空闲状态使用。在CCP界面中，点击 *Quick Connect*，即可看到一个列表。
![](1-ccp-quick-connect.jpg?width=400px&featherlight=false)
![](1-ccp-quick-connect2.jpg?width=400px&featherlight=false)

2. User （Agent）
即一个坐席用户用户，用于将当前来电转接给一个坐席。
3. Queue
即一个队列，用于将当前来电转接到一个队列，然后由这个队列对应的坐席进行接听。

对于后两种类型，则是在坐席接听电话的过程中，进行转接：
![](1-ccp-quick-connect-agent.jpg?width=400px&featherlight=false)
![](1-ccp-quick-connect-agent2.jpg?width=400px&featherlight=false)
![](1-ccp-quick-connect-agent3.jpg?width=400px&featherlight=false)

### Quick Connect 如何工作

Amazon Connect 中，以用户类型的 *Quick Connect* 为例，使用 *Quick Connect* 中进行转接的流程如下：
1. 客户拨打电话进行咨询
2. 该电话对应某一个Contact Flow进行处理
3. 在 *Contact Flow* 的流程中完成以后，电话进入某一个队列，等待坐席接听
4. 空闲坐席Agent1接听该来电
5. Agent1 转接来电给 Agent2
6. 该来电转到相应的 *转接坐席* 的 *Contact Flow*
7. 转接 *Contact Flow* 完成后，坐席 Agent2 接收到来电请求，进行接听

所以，在转接给坐席的时候，实际上也是通过 Flow 来实现的。Connect 已经为我们提供了默认的流程 *Default agent transfer*，可以在 Flows 列表中找到。当 Agent1 通过 *Quick Connect* 转接给 Agent2 时，会执行这个流程。

![](2-default-agent-transfer.jpg?width=800px&featherlight=false)

在这个流程中，会执行以下几个 Block：
1. 通过 *Play prompt* block，播放语音 “Transferring now...”，这是 Agent1 会听到的提示音。
2. 通过 Block *Set working queue*，设置 Agent2 所在的队列为工作队列。
3. 通过 Block *Transfer to queue* 将当前电话转接到队列。
4. 最后，Disconnect 表示当前流程结束，之后就由坐席接听。

## 创建 Agent Quick Connect

了解了 *Quick Connect* 的工作原理，下面就来看看如何创建 Agent 类型的 *Quick Connect*。

### 创建 Quick Connect
根据上面的原理描述，我们需要一个转移到Agent的 Flow 流程，我们就使用默认的 *Default agent transfer* 即可，如果想要修改转接时的动作，比如说修改提示语，可以修改 *Default agent transfer* Flow，或者创建新的。

然后，需要创建一个 *Quick Connect*，使用管理员登录 Connect 的管理界面，从菜单中找到 Routing - Quick Connects，进入 *Quick Connect* 列表页：
![](2-quick-connects.jpg?width=800px&featherlight=false)

点击添加：
![](2-quick-connects-add.jpg?width=800px&featherlight=false)

输入名字，描述信息，选择类型为 *User*，用户选择要转接的用户，Contact flow选择 *Default agent transfer* ，或者自己创建的 flow。

### 关联队列

上面介绍了在转接的时候，是通过队列转接到队列中的坐席用户，所以需要将新创建的 *Quick Connect*，关联到队列上。

在Connect 的管理界面，菜单中找到 Routing - Queues，进入队列列表，找到跟坐席用户关联的队列，点击进行编辑。

![](2-quick-connects-add-to-queue.jpg?width=800px&featherlight=false)

在 *Quick connects* 部分，将新创建的 *Quick Connect* 添加到列表中，点击保存。在上面创建的 *Quick Connect* 中，用户是 mav2，所以，某个坐席用户在队列 *BasicQuueue* 中接听客户电话的时候，可以选择将当前电话，转接给 mav2 坐席。

### 测试
现在某个坐席接听了客户电话后，就可以通过 *Quick Connect* 转接给 Mav2:
![](1-ccp-quick-connect-agent.jpg?width=400px&featherlight=false)

点击 *Quick Connect*，就会看到创建的列表：
![](2-quick-connects-transfer-agent.jpg?width=400px&featherlight=false)

转给 Mav2 后，就会看到如下页面：
![](2-quick-connects-transfer-agent2.jpg?width=400px&featherlight=false)

## Queue 类型的 Quick Connect
Queue 类型的 Quick Connect 的创建和配置过程，跟Agent类型的类似，只是在 *Quick Connect* 中，类型选择 *Queue* 类型，以及 *Default queue transfer* flow：
![](2-quick-connects-add.jpg?width=800px&featherlight=false)

转接的时候，就会转到队列 *BasicQueue*，跟这个队列关联的所有坐席用户，都会接到这个转接的电话请求。