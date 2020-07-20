---
title: "创建认证"
date: 2018-10-03T10:18:20-07:00
draft: false
weight: 20
---

需要注册申请 Authing 账户

需要在 https://authing.cn/ 进行新账户的注册申请。

### 新建用户池 
当注册成功并进入 Authing.cn 后，点击左侧菜单栏中的 【用户池列表>新建用户池】 菜单。 根据下列示例信息创建新的用户池。请注意填写一个唯一的用户池名称。有可能有的用户池名称已经被占用。 

用户池名称： iotsimulation 

用户池域名： iotsimulation 

选择你的用户池类型： Web-Web Application

具体截图如下：

![Image](/images/PreRequest/001.png)

### 新建第三方登录-OIDC 应用 
新建用户池后，进入用户池(例如：iot-simulation)。选择 “第三方登录”->“OIDC 应用菜单” ->“创建 OIDC 应用”

![Image](/images/PreRequest/002.png)

具体字段填写信息如下：

应用名：输入自定义的应用名称（例如： iotsimulation） 
![Image](/images/PreRequest/003.png)

认证地址：输入自定义的应用名称（例如： iotsimulation）
![Image](/images/PreRequest/004.png)

回调地址 URL：输入 " http://127.0.0.1:4200 "。
{{% notice warning %}}
请注意，该值会在 IoT device Simulator 部署后会使用 Cloudformation 的输出值进行替换，如果不替换，将无法实现用户认证成功后系统自动跳转的功能。
{{% /notice%}}
![Image](/images/PreRequest/005.png)

授权模式：选择 authorization_code, refresh_token, authingToken 单选框。
![Image](/images/PreRequest/006.png)

返回类型：选择 Code 单选框
![Image](/images/PreRequest/007.png)

Token 换取时认证方式： 选择 Client_secret_post 单选框。
![Image](/images/PreRequest/008.png)

id_token 签名算法： 选择 RS256 单选框。
![Image](/images/PreRequest/009.png)

高级选项 》 自定义 RSA 签名密钥， 点击随机生成签名密钥。例如
![Image](/images/PreRequest/010.png)

其他值保持默认值，点击 “确定” 按钮。 

生成 OIDC 应用。 点击已授权应用。信息如下所示。（注意：仅为示例，真实值请根据自己创建的 OIDC 应用进行填写）
![Image](/images/PreRequest/011.png)

请留意如下字段，如下字段中对应的值要在以后的步骤中被使用到。

1. App ID
2. App Secret
3. Issuer
4. 应用名
5. 认证地址
6. 回调 URL
7. 签名密钥

### 创建测试用户 
该测试用户将用于整个解决方案的功能演示。选择右侧的【用户中心】-》“创建用户”按钮。
![Image](/images/PreRequest/012.png)

输入用户名，邮箱，密码和确认密码。该用户名及密码将用于 IoT Device Simulator 的登录界面。
![Image](/images/PreRequest/013.png)

创建成功后，所创建的新用户会出现在当前用户列表中。如下图所示，用户名为 test 的用户已经被成功创建。
![Image](/images/PreRequest/014.png)