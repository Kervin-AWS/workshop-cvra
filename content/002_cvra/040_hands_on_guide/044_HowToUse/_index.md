---
title: "系统使用"
date: 2018-10-03T10:14:32-07:00
draft: false
weight: 40
---

## 模拟车辆行驶

由于本解决方案专注于车联网中云端部分的功能实现。而对于车端的数据发送则建议您借助于“物联网设备模拟器解决方案”中的汽车功能模块发送车辆模拟行驶数据。

关于物联网设备模拟器解决方案的部署，请参考 (https://www.amazonaws.cn/solutions/iot-device-simulator/)

![Image](/images/cvra/009.png)

或者自己开发脚本或程序按照如下的数据格式发送模拟数据。

在“物联网设备模拟器”中【汽车】模块，发送模拟数据格式如下：

|变量	|含义|
| ------ | ----------- | 
|vehicle_speed_mean|车辆速度|
|engine_speed_mean|发动机转速|
|torque_at_transmission_mean|扭矩|
|oil_temp_mean|油温|
|accelerator_pedal_position_mean|加速踏板位置|
|brake_mean|刹车踏板力度|
|high_speed_duration|高速时长|
|high_acceleration_event|急加速事件|
|high_braking_event|急减速事件|
|idle_duration|怠速时长|
|start_time|驾驶开始时间|
|ignition_status|点火状态|
|brake_pedal_status|刹车踏板状态|
|transmission_gear_position|档位|
|odometer|里程表数值|
|fuel_level|剩余油量|
|fuel_consumed_since_restart|本次驾驶油耗|
|latitude|经度|
|longitude|维度|
|timestamp|时间戳|
|trip_id|行程ID|
|vin|车辆ID|
|end_time|驾驶结束时间|

## API Gateway
### API 接口
该方案借助 API Gateway 对外提供操作及查询服务，接口描述如下：

|路径|方法|功能
|/signin|post|提交用户名及密码，返回 JWT Token|
|/vehicles|get|返回所有车辆信息|
|/vehicles|post|创建新的车辆|
|/vehicles/{vin}|get|根据输入参数车辆编码{vin}，返回车辆详细信息。|
|/vehicles/{vin}/anomalies|get|返回某一辆车{vin}的所有异常驾驶记录|
|/vehicles/{vin}/anomalies/{anomaly_id}|get|返回某一辆车{vin}的某一个异常驾驶记录{anomaly_id}详细信息|
|/vehicles/{vin}/dtc|get|返回某一辆车{vin}的所有 DTC 故障代码|
|/vehicles/{vin}/dtc/dtc_id|get|返回某一辆车{vin}中某一个DTC 代码{dtc_id}的详细信息|
|/vehicles/{vin}/healthreports|get|返回某一辆车{vin}的所有健康报告|
|/vehicles/{vin}/healthreports/{report_id}|get|返回某一辆车{vin}中某一个健康报告{report_id}的详细信息|
|/vehicles/{vin}/trips|get|返回某一辆车{vin}的所有行驶记录|
|/vehicles/{vin}/trips/{trip_id}|get|返回某一辆车{vin}中某一个行程{trip_id}的详细信息|

## 使用 Restful API 工具进行测试

该解决方案部署完成后，只提供了 API Gateway 服务接口及 DynamoDB 表 ，S3 存储桶等数据存储服务。
而没有提供一个可视化的界面供用户使用。为了便于您的使用，可以借助于 Postman 或 Swagger 这两款免费的API 调用工具进行测试。
由于 API Gateway 提供相关服务导出为 Swagger 或 OpenAPI3 接口文件，所以用户可以从以下两种工具进行选择。在本指南中，仅以测试  Get /Vehicles 接口为例。

### Postman

借助于Postman，可以创建并执行任何REST，SOAP和GraphQL查询。如需获得更多关于 Postman的信息，请参考（https://www.postman.com/）。借助API Gateway 的 导出功能，可导出Connected Vehicle API 相关接口为 JSON 格式文件。然后把该 JSON 格式的文件导入 Postman。具体步骤如下：
1. 进入 API Gateway → Stages → Prods → Export → Export as Swagger →选择JSON 格式, 复制 Json文件内容。
    ![Image](/images/cvra/010.png)
2. 在 Postman → Import → Raws Text, 粘贴 Json 文件内容，点击 【continue】。
    ![Image](/images/cvra/011.png)
3.	选择 Post /signin 接口， 在Body 标签页中选择 【raw】单选框，并且在下面的输入框输输入如下信息，模拟用户登录。
```
{"userid":"5ec6644b9eb9fe198b7f973e","password":"a1ef50c2e6a5e31f0f5ddc4a8f0fad41"}
```
userid 值可以是任意标志当前登录用户 id 的字符串，而 password则是在【部署说明】-> 【前提】中，所生成的32位字符的字符串。
    ![Image](/images/cvra/012.png)

4.	点击【 Send】按钮，返回JWT Token.
```
{
 "jwttoken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJfaWQiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJpYXQiOjE1OTEzMzc0NjIsImV4cCI6MTU5MTM0MTA2MiwiYXVkIjoiaHR0cDovL2F3c2Nvbm5lY3RlZHZlaGljbGUuY29tIiwiaXNzIjoiQVdTIENvbm5lY3RlZCBWZWhpY2xlIn0.p_W_JbQNi9wK-kC5iVpsMpA9W8HsfZP_nVssO-8Jr30"
}
```

5. 测试Get /vehicles 接口: 选择 Get /vehicles 接口函数， 选择【Headers】 标签页 ，选择【Key: Authorization】 ，在 【Value】输入框输入返回 Jwttoken 内容。
    ![Image](/images/cvra/013.png)

6.	然后点击 【Send】 按钮。返回所有车辆数据。
    ![Image](/images/cvra/014.png)

### Swagger UI

关于 Swagger UI 的详情，请参考该链接。https://swagger.io/tools/swagger-ui/ Swagger UI允许您可以以可视化的方式同API资源交互。 它可根据您的OpenAPI规范自动生成接口，并具有可视化文档，可简化后端实现和客户端使用。另外借助API Gateway 的 导出功能，可导出车辆网 API 相关接口（ JSON 格式文件）并导入 Swagger。

示例界面如下：
![Image](/images/cvra/015.png)

启动 Swagger
1.	进入 API Gateway 控制台， 选择  Stages → Prods → Export → Export as Swagger →选择JSON 格式。
2.	下载 JSON 文件并保存。假设该 JSON 文件 connected_vehicle_swagger.json 存储在 /Users/test/ 目录。
3.	启动 Docker （请注意修改 /Users/test:/ 为您的目录名称）
docker run -p 8080:8080 -e SWAGGER_JSON=/mnt/connected_vehicle_swagger.json -vpa /Users/test:/mnt swaggerapi/swagger-ui
4.	启动浏览器，输入 http://localhost:8080 
![Image](/images/cvra/016.png)
5.	由于Swagger 运行在本地，localhost 的域名同 API Gateway 的域名不一致。根据 CORS 的规则，无法使用 Swagger 直接 提交(Post)数据。为了演示方便，使用 Curl 命令提交认证信息到 API Gateway /signin 接口。 Post 数据格式包含 userid 及 password。假设API Gateway URL 为 https://5hjzv26t9a.execute-api.cn-northwest-1.amazonaws.com.cn/prod
6.	在API Gateway 中 /Signin 接口中 Post 操作提供用户认证的功能。用户首先需要提交用户 ID 及密码，密码就是OIDCAppSecretParameter 参数所输入的值，例如 a1ef50c2e6a5e31f0f5ddc4a8f0fad41
7.	运行如下命令，得到 JWT Token。注意：请替换为您部署后得到的 API Gateway Endpoint。userid 值可以是任意标志当前登录用户 id 的字符串，而 password则是在【部署说明】-> 【前提】中，所生成的32位字符的字符串。

```shell
curl -X POST "https://5hjzv26t9a.execute-api.cn-northwest-1.amazonaws.com.cn/prod/signin" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"userid\": \"5ec6644b9eb9fe198b7f973e\", \"password\": \"a1ef50c2e6a5e31f0f5ddc4a8f0fad41\"}"
```
8.	返回如下字符串。
```
{"jwttoken":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJfaWQiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJpYXQiOjE1OTEzMzY2MzgsImV4cCI6MTU5MTM0MDIzOCwiYXVkIjoiaHR0cDovL2F3c2Nvbm5lY3RlZHZlaGljbGUuY29tIiwiaXNzIjoiQVdTIENvbm5lY3RlZCBWZWhpY2xlIn0.4UloXDdBvc2wzPI1oScUOCZAHwCCcUZawuKOEw1k1pY"}
```

9.	该 Token 的默认过期时间为1 小时，使用算法 HS256。
10.	使用 Jwt decode 解码（可使用工具 https://jwt.io/）。载荷信息显示如下：

```
"sub": "5ec6644b9eb9fe198b7f973e",
"_id": "5ec6644b9eb9fe198b7f973e",
"iat": 1591336638,
"exp": 1591340238,
"aud": "http://awsconnectedvehicle.com",
"iss": "AWS Connected Vehicle"
```

11.	选取 Jwttoken 对应值，其中sub字段表示当前 userid，iat字段为 token 颁发时间,exp 字段为token过期时间。aud及 iss 为演示目的使用。
12.	在 Swagger 中点击 Authorize 按钮
![Image](/images/cvra/017.png)
13.	在 Vechicle-service-authorizer(apikey)中的 Value 输入框输入 jwttoken 对应的值。
    示例
    ```
    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJfaWQiOiI1ZWM2NjQ0YjllYjlmZTE5OGI3Zjk3M2UiLCJpYXQiOjE1OTEzMzY2MzgsImV4cCI6MTU5MTM0MDIzOCwiYXVkIjoiaHR0cDovL2F3c2Nvbm5lY3RlZHZlaGljbGUuY29tIiwiaXNzIjoiQVdTIENvbm5lY3RlZCBWZWhpY2xlIn0.4UloXDdBvc2wzPI1oScUOCZAHwCCcUZawuKOEw1k1pY
    ```
![Image](/images/cvra/018.png)
14.	点击 authorize 按钮，保存后。点击 Close 按钮，关闭该窗口。
15.	测试 Get /vehicles 接口。
    ![Image](/images/cvra/019.png)
16.	点击 Try it out → Execute 按钮。
    ![Image](/images/cvra/020.png)
返回所有车辆信息。