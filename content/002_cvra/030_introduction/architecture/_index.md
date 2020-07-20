---
title: "架构描述"
date: 2018-10-03T10:17:52-07:00
draft: false
weight: 20
---

1.	联网车辆会发送消息到 AWS IoT平台，AWS IoT在收到消息时会对其进行身份验证和授权，而规则引擎会对消息执行相应的规则，将其路由到相应的后端应用程序。

2.	AWS IoT 规则将联网车辆数据转发到 Amazon Kinesis Data Firehose 流，原始数据保存到 Amazon S3 存储桶。

3.	负责行程数据的 AWS IoT 规则会实时处理检测并触发调用 AWS Lambda 函数来处理行驶中车辆发送的数据，并将其存储在 DynamoDB 表中。

4.	负责驾驶员安全评分的 AWS IoT 规则会行程结束后，进行触发调用 AWS Lambda 函数来处理聚合的行程数据。以便生成驾驶员安全评分、触发针对驾驶员的 Amazon SNS 通知，并将评分添加到行程数据 DynamoDB 表中。

5.	负责诊断故障代码的 AWS IoT 规则会检测 IoT 主题中的诊断故障代码，然后调用 Lambda 函数来将故障代码存储在 DynamoDB 表中。并将其转换为容易被读取的信息。

6.	负责基于位置的营销的 AWS IoT 规则会检测车辆的位置，然后调用 Lambda 函数来确定车辆是否位于兴趣点附近。当车辆靠近兴趣点时，该函数会将位置记录在 DynamoDB 表中，并触发针对用户的 Amazon SNS 通知，其中附带广告。
