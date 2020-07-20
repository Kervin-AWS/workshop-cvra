---
title: "快速部署"
date: 2018-10-03T10:14:32-07:00
draft: false
weight: 20
---



本文的步骤主要针对在运行在由西云数据运营的 AWS（宁夏）区域或由光环新网运营的 AWS（北京）区域中的 IoT Device Simulator 解决方案，您可以使用以下链接快速启动一个 CloudFormation 堆栈来部署和管理整个方案：

[<span style="color: red;">**点击启动堆栈**</span> ](https://cn-northwest-1.console.amazonaws.cn/cloudformation/home?region=cn-northwest-1#/stacks/create/template?stackName=iot-device-simulator&templateURL=https:%2F%2Faws-solutions-reference.s3.cn-north-1.amazonaws.com.cn%2Fiot-device-simulator%2Fv2.1.1%2Fiot-device-simulator-china.template)

## 开始启动堆栈
点击宁夏和北京区域链接，打开 AWS 管理控制台（如果还没登录会先跳转到登录页面，登录后进入模板启动页面）。默认情况下，此模板在宁夏区域启动，您同时可以使用控制台右上方的区域选择链接，以在其他区域部署该方案。
![Image](/images/PreRequest/016.png)

## 配置启动参数
点击下一步，进入详细参数的配置页面 

配置示例如下：

![Image](/images/PreRequest/017.png)
下表展示的是每一项参数的含义及默认值。请注意：以下所有的值都为必填值。

| 参数 | 默认 | 描述 |
| ------ | ----------- | ----------- |
| AuthingThumbPrint   | Yes | Authing 指纹，请保持默认值e6a3b45b062d509b3382282d196efe97d5956ccb|
| AuthingAppUrl | No |请输入 Authing OIDC 应用中对应的 “Issue” 字段值，例如https://iotsimulation.authing.cn/oauth/oidc|
| AuthingAppOidc | No |请输入输入 Authing OIDC 应用中对应的 “Issue” 字段值中<span style="color: red;">**去掉 “https"**</span>的部分，例如 iotsimulation.authing.cn/oauth/oidc|
| AuthingAppId|No | 请输入 Authing OIDC 应用中对应的 “App ID” 字段值，例如 5e9420f3b16cc8e2a51c1868|
|AuthingAppName |No |请输入 Authing OIDC 应用中对应的 “应用名” 字段值，例如 iotsimulation |
|AuthingAppSecret |No | 请输入 Authing OIDC 应用中对应的 “App Secret” 字段值，例如 a2d5eba65b1b614a50db1ade61f50e4b|
|AuthingRSAsignature |No | 请输入 Authing OIDC 应用中对应的 “签名密钥” 字段值，例如 { "n": "zTAIwLfWzymVYGK5oywDJnqrq8pbMIQAQVYQEtGC YiDct8nknbtvqHg3N31O3n-vhOOIZE26Ybg3dnlN__I5UDjLmSY_cl_Nusvxs3XZFzw pCuSyLzirZ3MT43HLP8HFV3iCeCgN3mNwDO3xJcnqX KzMmLSgNOmSxLdThKXh1a4_TC5gtbT-qmeO1S0yyDD5HNKvgAsRUl6BFXKY3LS-3o2gT9uWwyeX_PeTXtbSebU4Tya1UwMuzlDxvT3kb HRv7zDuVhG5ypBiEXnO04AGeUUAUd5PnuwQum8v1 mMCmelcw0oKoMcwTuImCTIhWbPTg_blI8EVFOtvRi mAOm8l7Q", "e": "AQAB","use": "sig", "alg": "RS256", "kid": "jjtRhZbXxUmh5ZTCOsD0YpOrM0AyQdoC8xsyfhjLSQ 0","kty": "RSA"}|
| MapxToken| No|请输入 Mapx Token 值，例如 pk.eyJ1IjoiY2h1bmh1YXYiLCJhIjoiY2s2OHo3xxxxxxxxx xxxxxxxx |

具体参数输入示例：

![Image](/images/PreRequest/018.png)
参数设置完成后，点击下一步。在本步骤中请确保堆栈选项保留默认值。直接点击下一步。
![Image](/images/PreRequest/019.png)

## 授权 IAM 并创建堆栈
在最后的审核页面中，勾选最下方的【我确认，AWS CloudFormation 可能创建 IAM资源。】选择框。接下来点击【创建堆栈】，开始堆栈的创建。
![Image](/images/PreRequest/020.png)

## 等待堆栈创建成功
您可以在 AWS CloudFormation 控制台的【状态】列中查看堆栈的状态，并点击右上方的刷新按钮更新状态。大约五分钟内，您可以看到堆栈状态变为CREATE_COMPLETE，此时堆栈创建成功。
![Image](/images/PreRequest/021.png)

## 查看堆栈资源
堆栈创建后，点击【资源】标签，可以看到这个堆栈启动的所有资源。
![Image](/images/PreRequest/022.png)

## 查看堆栈输出参数
点击堆栈的【输出】 tab 页，可以看到该堆栈的输出参数。其中 “NeedToUpdateAUthingCallBackURL” 的输出值需要使用。
![Image](/images/PreRequest/023.png)

## 更新 authing 中的 Redirect URL
堆栈创建完成后，点击 CloudFormation 中对应的堆栈的“输出”标签页。 

{{% notice warning %}}
复制 “NeedToUpdateAUthingCallBackURL”中对应的值，例如 " http://iotsim73-iotsimwebsitebucket-mmpj7mzz94ai.s3-website.cn-north-1.amazonaws.com.cn/home/login " 需要在 Authing 网站中进行相应的更新。
{{% /notice %}}
![Image](/images/PreRequest/024.png)

登录Authing.cn 网站，点击左侧的 用户池列表中您创建的用户池。 在右侧点击【第三方登录】-》【OIDC 应用】: 例如 Iotsimulation 用户池
![Image](/images/PreRequest/025.png)

选择 【回调 URL】 进行更新。请确保多个回调 URL 使用“;”进行分割。示例如下
![Image](/images/PreRequest/026.png)