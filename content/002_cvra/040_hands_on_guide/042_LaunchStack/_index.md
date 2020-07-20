---
title: "快速部署"
date: 2018-10-03T10:14:32-07:00
draft: false
weight: 20
---



本文的步骤主要针对在运行在由西云数据运营的AWS（宁夏）区域或由光环新网运营的AWS（北京）区域中部署车联网解决方案，您可以使用以下链接快速启动一个CloudFormation堆栈来部署和管理整个方案：

[<span style="color: red;">**点击启动堆栈**</span> ](https://cn-northwest-1.console.amazonaws.cn/cloudformation/home?region=cn-northwest-1#/stacks/create/template?stackName=aws-connected-vehicle&templateURL=https:%2F%2Faws-solutions-reference.s3.cn-north-1.amazonaws.com.cn%2Faws-connected-vehicle-solution%2Flatest%2Faws-connected-vehicle-solution-dev-cn.template)

## 开始启动堆栈
点击宁夏和北京区域链接，打开 AWS 管理控制台（如果还没登录会先跳转到登录页面，登录后进入模板启动页面）。默认情况下，此模板在宁夏区域启动，您同时可以使用控制台右上方的区域选择链接，以在其他区域部署该方案。
![Image](/images/cvra/003.png)

### 指定堆栈详细信息
堆栈名称 ： connected-vechicle

参数：

OIDCAppSecretParameter 字段输⼊随机字符或数字，该值会⽤于 JWT Token 的签
发和验证。

随机字符串⽣成⽅法见 【部署说明】→ 【前提】→ 【准备⽤于 OIDC（OpenID
Connect） 的 JWT token 加密及验证的字符串】中⽣成的随机字符串。

本指南使⽤ a1ef50c2e6a5e31f0f5ddc4a8f0fad41 作为⽰例。
![Image](/images/cvra/004.png)
点击 【下⼀步】按钮。

### 配置堆栈选项
保持默认值，点击【下⼀步】

![Image](/images/cvra/005.png)


### 审核
请确保勾选 “我确认，AWS CloudFormation 可能创建具有⾃定义名称的 IAM 资源”的
单选框。如下图所⽰：

![Image](/images/cvra/006.png)

点击 【创建堆栈】按钮。

部署完成
等待⼤约 5 分钟，堆栈部署完成。
![Image](/images/cvra/007.png)

点击【输出】标签页。查找 TelemetricsApiEndpoint 键对应的值。例如下图中的
https://gbos35z12b.execute-api.cn-northwest-1.amazonaws.com/prod 该值为车联⽹解决⽅案 API Gateway 的 endpoint，通过该 endpoint 可实现数据交
互。
![Image](/images/cvra/008.png)