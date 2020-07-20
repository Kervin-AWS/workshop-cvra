---
title: "组件介绍"
date: 2018-10-03T10:14:32-07:00
draft: false
weight: 80
---

本小节将介绍解决方案中的组件，我们将从以下模块展开:

### IoT消息认证

当AWS IoT收到消息时，它对消息进行身份验证和授权，规则引擎对消息执行适当的规则，规则引擎将消息路由到适当的后端应用程序。

AWS IoT的所有连接点都利用了双向身份验证和数据加密传输的优势，以确保在没有经过验证的身份的情况下，不会在车辆和AWS IoT之间交换数据。

### 行程数据

车辆在行驶时，会将传感器收集到的数据发送到 AWS IoT，AWS IoT规则不断触发调用Lambda函数，该函数处理车辆远程信息数据，并将数据存储在DynamoDB表中。

### 驾驶员安全评分

在行程结束后，AWS IoT 规则检测到行程已经结束，会调用一个Lambda函数来处理行程数据并聚合。按照预设的算法来生成驾驶员的安全评分，该分数被添加到DynamoDB行程数据表中。

### 诊断故障码

AWS IoT 诊断故障代码规则检测诊断发送到到IoT 主题的故障代码,并调用一个Lambda函数在DynamoDB表中存储故障代码，故障代码转换为便于用户阅读和理解的消息,并触发一个Amazon SNS通知包含故障代码的消息给用户。

### 基于位置的营销

AWS IoT基于位置的营销的规则实时检测车辆的位置，并调用Lambda函数来确定车辆是否在感兴趣点附近。如果车辆靠近感兴趣的地点，该函数将在DynamoDB表中记录该位置，并向用户触发Amazon SNS通知，该通知中包含广告信息。

### 用户认证模块

本解决方案会创建 API Gateway并作为数据交互的接口 。其他应用程序和第三方服务可以通过RESTful API安全地与 API Gateway进行交互。

这些API充当访问存储在Amazon DynamoDB中的车辆数据的“前门”。

关于用户认证的功能，其API Gateway 中 /Signin 接口的 Post 操作提供了用户认证的功能。用户首先需要提交用户 ID 及密码，后端 Lambda函数比对用户提交的密码和在使用 CloudFormation 创建堆栈时输入的密码。如果密码正确，那么返回一个 JWT Token，否则不会返回 JWT Token。具体流程如下图所示。

![Title Image](/images/cvra/002.png)

针对其他接口的认证功能，则使用了自定义的 Lambda 授权方方法对用户请求进行验证。确保在请求 API Gateway 其他接口时需要确保HTTP Header 中设置Authorization变量。Lambda授权方函数验证 Authorization 变量的有效性。在该解决方案中，Authorization 的值为 /signin/ 接口返回的JWT Token 内容。JWT Token 内容示例：

```
jwttoken:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJfaWQiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJpYXQiOjE1OTAzMjcyNjIsImV4cCI6MTU5MDMzMDg2MiwiYXVkIjoiaHR0cDovL2F3c2Nvbm5lY3RlZHZlaGljbGUuY29tIiwiaXNzIjoiQVdTIENvbm5lY3RlZCBWZWhpY2xlIn0.vEkDvSgaMmiUXY_XAW2ehd2J6Dfw2llSOGDGexlKoKo
```
