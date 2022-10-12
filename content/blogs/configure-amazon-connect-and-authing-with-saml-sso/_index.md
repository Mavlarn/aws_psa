---
title: "配置Amazon Connect使用 Authing SAML进行单点登录"
author: "Mavlarn"
description : "配置 Amazon Connect 使用 Authing SAML 进行单点登录"
date: 2022-10-12T11:13:17+08:00
date: 2022-10-12T11:13:17+08:00
weight: 2

tags : [                                    
"Amazon Connect", "Authing", "SAML"
]
categories : [                              
"Amazon Connect",
]
keywords : [                                
"Connect", "Authing", "SAML", "SSO"
]

# next: /tutorials/github-pages-blog      # 下一篇博客地址
# prev: /tutorials/automated-deployments  # 上一篇博客地址
---

[Authing](https://authing.cn/) 是在云上为企业和开发者提供专业的身份认证和授权服务，也支持 SAML 2.0 协议。本文介绍如何使用 Authing 认证服务，来实现 Amazon Connect 的单点登录。

对于，SAML 协议以及使用 SAML 协议进行单点登录的详解，可以参考另一篇文章 [配置 Amazon Connect 使用 Keycloak SAML 进行单点登录]({{< relref "configure-amazon-connect-keycloak-with-saml-sso" >}} "配置 Amazon Connect 使用 Keycloak SAML 进行单点登录")。在本文中，就只是简单介绍在 Authing 中配置时需要设置和注意的几个地方。


## 创建 Authing 应用

首先需要在 Authing 里面创建一个应用，具体过程参考[官方文档：成为 SAML2 身份源](https://docs.authing.cn/v2/guides/federation/saml.html)。即创建一个应用，然后在这个应用中启用 SAML，在配置当中，配置 ACS 地址为 *https://signin.aws.amazon.com/saml*。回调地址就是 *https://ap-northeast-2.console.aws.amazon.com/connect/federate/d47fc0b2-83c2-4af9-b78a-6d312739b4a3* 这样的地址。

然后，还需要创建几个自定义 SAML Response 属性，就像在上一篇文档中创建的几个属性：

 * https://aws.amazon.com/SAML/Attributes/Role
 * https://aws.amazon.com/SAML/Attributes/RoleSessionName
 * https://aws.amazon.com/SAML/Attributes/SessionDuration

其中 *https://aws.amazon.com/SAML/Attributes/Role* 就是要映射到 AWS 的角色，所以也需要先创建相应的身份提供商以及角色。

## 在 AWS 中配置使用 Authing SAML

同样的，我们需要在 AWS 中创建 Authing 的身份提供商，有关这个，Authing 也提供了文档[使用 SAML2 登录 AWS 控制台（中国区）](https://docs.authing.cn/v2/integration/aws/)。

具体过程也是一样，先从 Authing 获取描述文件，然后在 AWS 创建的时候使用该描述文件导入，创建身份提供商后在创建角色，用于 Authing 登录的用户访问 AWS 的 Connect 服务。

然后，的到角色的 ARN 和身份提供商的 ARN，用逗号连起来，就是在上面创建自定义 SAML Response 属性时 *https://aws.amazon.com/SAML/Attributes/Role* 的值。

其他的都跟 [配置 Amazon Connect 使用 Keycloak SAML 进行单点登录]({{< relref "configure-amazon-connect-keycloak-with-saml-sso" >}} "配置 Amazon Connect 使用 Keycloak SAML 进行单点登录") 类似，参考上一篇即可。