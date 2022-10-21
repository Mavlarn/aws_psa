---
title: "Amazon Connect++ 解决方案设计"
author: "Mavlarn"
description : "Amazon Connect++ 解决方案设计"
date: 2022-10-14T11:13:17+08:00
date: 2022-10-14T11:13:17+08:00
weight: 3
draft: true

tags : [                                    
"Amazon Connect", "Solution", "SAML"
]
categories : [                              
"Amazon Connect",
]
keywords : [                                
"Connect", "Solution", "SAML", "SSO"
]

# next: /tutorials/github-pages-blog      # 下一篇博客地址
# prev: /tutorials/automated-deployments  # 上一篇博客地址
---


# SmartOOP

## Auth
### Cognito user pool
1. user management

### Cognito identity pool
1. to access DynamoDB, AppStream

### PostAuth
1. create Agent item in DynamoDB


### UserGroups
1. Admin
2. Manager
3. Agent


## GraphQL API


### Desktops
1. createDesktop
2. listDesktop
3. assignDesktop
4. initDesktop

### Agents
1. createAgent
2. updateAgent
3. deleteAgent
4. listAgent

## REST API

### admin

1. api: /admin

2. function: AdminFunction


### Messages

1. listMessage
2. getOneMessage
3. replyMessage


## AB III

1. Value prodpotion(方案的价值)


cdk deploy --hotswap



