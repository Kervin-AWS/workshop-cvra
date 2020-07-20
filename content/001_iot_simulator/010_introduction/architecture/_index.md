---
title: "架构描述"
date: 2018-10-03T10:17:52-07:00
draft: false
weight: 20
---

AWS CloudFormation 模板部署了设备模拟器 API，该 API 利用 Amazon API Gateway 调用 AWS Lambda 函数。这些 Lambda 函数提供了用于在虚拟设备和设备类型上执行数据 CRUD 操作，记录相关模拟指标以及执行管理任务的业务逻辑。这些Lambda 函数与 Amazon Simple Storage Service（Amazon S3），Amazon DynamoDB，AWS 身份和访问管理（IAM）和 Amazon CloudWatch Logs 交互以提供数据存储，管理和日志记录功能。

该解决方案还部署了一个包含两个公共子网和两个的私有子网的 Amazon VPC 网络拓扑。该模拟器引擎在由 AWS Fargate 提供的 Amazon Elastic Container Service（Amazon ECS）容器中运行。 同时 VPC 还包括一个 NAT 网关。

该解决方案创建一个 Web 控制台，并将其部署到配置了静态网站托管的 Amazon S3存储桶中。关于用户认证的部分，本方案使用 Authing（https://authing.cn/ ）作为 OpenID 的提供商， Cognito Identity pool 支持将身份与通过 IAM 配置的 OpenID Connect 提供商相关联，得到 ID 令牌，从而实现对控制台和设备模拟器 API 的访问控制。

首先需要进入系统，点击登录按钮后，系统会转向到 authing.cn 并提示输入用户和密码。当输入正确的用户名和密码后，进入到主 页面。

业务逻辑方面，当设备模拟器 API 收到授权请求时，Amazon API Gateway 会调用相应的 Lambda 函数。 Lambda 函数将执行结果返回给 API，API 将结果返回给模拟器控制台。收到设备模拟请求后，设备微服务会将请求发送到 Amazon Simple Queue Service（Amazon SQS）中的模拟队列。仿真引擎轮询仿真队列以获取仿真开始和停止请求。

最后，当收到启动物联网设备请求后，系统将基于该请求生成虚拟设备。并在设备类型定义中的持续时间内，开始将模拟数据发布到已定义的 AWS IoT 端点。当用户每次点击该设备的启动按钮，所对应的模拟设备就会开始运行，直到定义的执行持续时间到期或收到停止请求为止。当收到停止模拟请求后，系统将根据该请求停止设备模拟，并在当前设备列表中所对应的设备进行状态更新。