---
title: "组件介绍"
date: 2018-10-03T10:14:32-07:00
draft: false
weight: 80
---

本小节将介绍解决方案中的组件，我们将从以下模块展开:

### 设备模拟器模块
物联网设备模拟器微服务是一系列 AWS Lambda 函数，为所有设备模拟操作提供业务逻辑和数据访问层。每个 Lambda 函数都采用 AWS 身份和访问管理(IAM)角色，具有最少的特权访问(所需的最低权限)来执行其指定的功能。 
### 管理模块 
Lambda 函数 iot-sim-admin-service 处理发送到 /admin/* API 端点的设备模拟器API 请求。所有 /admin/ * 的 API 端点都配置为 Lambda 代理端点，将完整的请求有效负载转发至 iot-sim-admin-service 函数。 
### 度量指标模块 
Lambda 函数 iot-simple-metrics-service 处理发送到 /metrics/* API 端点的设备模拟器 API 请求。所有/metrics/* 的 API 端点都被配置为 Lambda proxy 端点 ，将完整的请求有效负载转发给 iot- simple-metrics-service 函数。度量指标模块处理物联网设备模拟器的所有度量指标操作。

### 设备管理模块
Lambda 函数 iot-sim-device-service 处理发送到 /device/* 端点的设备模拟器 API请求。所有 /device/* API 端点都配置为 Lambda proxy 端点，将完整的请求有效负载传递给 iot-sim-device-service 函数。设备管理模块所有设备和设备类型操作，包括设备列表、添加设备、删除设备、列表设备类型、添加设备类型、更新设备类型、启动模拟和停止模拟等操作。 
### 设备仿真模块 
本方案利用 Amazon Simple Queue Service（Amazon SQS），Amazon Elastic Container Service（Amazon ECS）和 AWS Fargate 来模拟将数据发送到 AWS IoT终端节点的虚拟设备。 用户通过该解决方案附带的 Web 控制台发出模拟请求。 系统将模拟请求添加到存储到 Amazon SQS 队列中，直到将其处理为止。 AWS Fargate 及 Amazon ECS 容器包含一个模拟引擎，该引擎会定期轮询模拟队列以获取模拟请求。 模拟引擎将启动虚拟设备，然后启动设备将模拟数据发布到 AWS IoT终端节点。 在到达指定的持续时间之后，模拟引擎将停止模拟，终止虚拟设备，并更新 iot-sim-device-widgets DynamoDB 表中的设备状态和指标。 

### 汽车模拟模块 
另外该方案还包括汽车模拟模块，您可以使用该模块使用预定义的汽车类型来模拟车辆遥测数据。 该遥测数据来自动力总成仿真模型所生成的仿真数据。关于汽车在地图上位置的实时更新功能也是本模块所具有的功能，所使用的地图是利用 Mapbox 所提供的服务。 该功能的前提需要您注册一个免费的 Mapbox 开发人员帐户，并获得 访问令牌 (Access Token)。 在注册后，需要将该 Mapbox 访问令牌添加到 IoT 设备模拟器。 

### 认证模块 
该模块利用 Authing.cn 所提供的身份验证功能。 在用户身份验证成功后，Authing.cn将发布一个 JSON Web Token (JWT) ，该令牌用于允许控制台将请求提交到设备模拟器API。 控制台将 HTTPS 请求与包含 JWT 令牌的授权标头一起发送到模拟器 API。

### DynamoDB 表 
该方案使用 Amazon DynamoDB 表保存设备，设备类型，设置和度量指标，以及相关的元数据。