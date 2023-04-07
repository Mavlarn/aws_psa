---
title: "坐席基本功能设置"
author: "Mavlarn"
description : "配置 Amazon Connect，配置Agent的基本功能"
date: 2023-03-18T11:13:17+08:00
date: 2023-03-18T11:13:17+08:00
weight: 2

tags : [
"Amazon Connect", "Agent"
]
categories : [
"Amazon Connect",
]
keywords : [
"Amazon Connect", "Agent", "Desk Phone"
]

---

本节将介绍一些基本的 Amazon Connect 的功能设置。


## 界面

首先，需要了解 Amazon Connect 有几个界面：

### Amazon Console 的 Connect 界面
即从 Amazon Console 找到 Connect 服务进入的页面，这里可以创建并配置 Connect 实例。如下就是其中一个配置页：
![](0-amazon-console-connect.jpg?width=900px)

### Connect 管理页面

创建完Connect 实例以后，打开Connect 的管理界面：
![](0-console-dashboard.jpg?width=900px)

### 坐席页面

坐席的应用页面有2个地址：
1. 基本CCP（Contact Control Panel）界面，仅包含拨号、接听的界面。
https://connect-xxxxx.my.connect.aws/ccp-v2

2. 坐席应用界面，包括基本CCP，以及Customer信息，Case，知识库（Wisdom）等页面。
https://connect-xxxxx.my.connect.aws/agent-app-v2

> 如果要打开包含客户信息、case等界面的坐席应用，需要坐席具有相应的权限。默认情况下，坐席只有打开基本 CCP 界面的权限，需要在用户管理中，修改用户的 Security Profile 给他设置相应的权限。

## 坐席基本通话功能

### 坐席状态

登录后，坐席可以设置自己的状态，默认只有两种状态，*Available* 和 *Offline*。只有在 *Available* 状态时，坐席才能接听到客户来电。

我们也可以设置自定义的坐席状态，管理员登录到 Connect 的管理界面，从左侧菜单中找到用户管理-坐席状态菜单：
![](1-mgmt-console-agent-status.jpg?width=500px)

进入坐席状态列表页面，可以新建一个状态：
![](1-mgmt-console-agent-status-new.jpg?width=500px)

输入新状态的名称和描述，保存后，就可以在坐席的界面看到新的状态，坐席就可以修改自己的状态。坐席只有在  *Available* 状态时才能接听到来电。


### 坐席通话界面、通话保持以及继续

当坐席  *Available* 状态时，有新客户来电时，页面上会收到提示：
![](1-agent-ccp.jpg?width=900px)

此时，左侧的界面会显示来电，以及电话号码，右侧的 *Customer Profile* 界面会试图查询该用户并显示。再通话过程中或结束后，坐席也可以编辑该信息。（坐席需要具有相应权限才能显示该页面）。

坐席接听后，就会开始通话：
![](1-agent-ccp-accept-call.jpg?width=400px)

坐席可以点击 *Hold* 进行通话保持，然后继续 *Resume*：
![](1-agent-ccp-hold-call.jpg?width=400px)

如果坐席未能及时接听，就会出现漏接电话提示：
![](1-agent-ccp-miss-call.jpg?width=400px)

当坐席完成客服电话挂断以后，坐席就会进入 *After Call* 阶段，此时坐席的通话界面不会完全结束，可以进行其他操作，例如修改客户信息，创建 Task 等。
![](1-agent-ccp-after-call.jpg?width=400px)

例如创建后续任务，例如该用户后续要完成的操作等：
![](1-agent-ccp-after-call-task.jpg?width=400px)

> 注意这时要创建任务之前，需要先创建一个quick connect。

### 坐席外呼

坐席在CCP界面，也可以进行外呼，点击 *Number pad* 打开拨号界面，输入号码就可以外呼。

坐席也可以点击CCP界面等 *Quick Connect* 按钮，再页面中输入号码进行外呼，但是要注意，各个地区的Connect实例，允许外呼的号码所在地区有一定限制。

### 坐席转移呼叫

当坐席接听客户来电时，也可以通过 *Quick Connect* 按钮转接到外部电话，或者其他坐席：

![](1-agent-ccp-quick-connect.jpg?width=400px)

在这里，我们可以选择已经创建的 *Quick Connect* 的用户或者号码，也可以输入其他号码。比如下面的转接页面，我们可以选择之前创建的 *mav1 user qc*，将这个来电转出去。
![](1-agent-ccp-quick-connect2.jpg?width=400px)

进行转接之后，就可以看到如下界面：
![](1-agent-ccp-quick-connect3.jpg?width=400px)

这时，你也可以点击 *Join* 按钮，加入到通话中，这时，当前坐席、转接对象、以及用户就会形成一个三方通话。
![](1-agent-ccp-quick-connect4.jpg?width=400px)

如果需要退出三方通话，就可以点击 *Leave* 离开。

> 注意在 Connect 中，无法转接到紧急号码，如 911 等。


## Agent登录并使用电话号码接听

AWS Connect可以允许坐席使用自己的电话接听。可以通过2种方式进行设置。

### 坐席登录设置

坐席登录后，点击设置图标，打开坐席的设置页面：
![](1-agent-setting.jpg?width=800px)

在 *Phone Type* 类型设置中，选择 *Desk Phone*，并输入坐席自己的电话号码并保存，就可以讲来电转接到相应的号码上。
![](1-agent-setting-desk-phone.jpg?width=500px)

> 根据各个国家的要求，我们不能讲来电转接到任何号码上，例如在美国区域创建的Connect实例，我们只能转接到美国、加拿大、墨西哥等少数几个国家的号码上。并且，也不能转接到类似 911 这样的紧急呼叫的号码上。

### 管理员设置

使用管理员登录并打开 Amazon Connect 的管理界面，左侧菜单中找到用户管理的菜单项：
![](2-user-mgmt.jpg?width=500px)

进入用户管理界面，找到要设置的坐席的用户，
![](2-user-mgmt-edit-user.jpg?width=900px)


我们可以在这里设置坐席的基本信息，如名字、邮箱、密码（只有本地登录才可以修改密码，如果是SAML或AD方式登录不会有修改密码选项）等，也可以在这里设置坐席的转接电话。
![](2-user-mgmt-edit-user-deskphone.jpg?width=500px)

## 坐席自动接听
如果坐席使用网页方式接听来电，即 *Soft Phone* 方式，就可以设置自动接听，管理员登录管理界面后，进入用户管理页面，进入用户详情：勾选 *Auto-accept calls* 即可。
![](2-user-mgmt-edit-user.jpg?width=900px)



