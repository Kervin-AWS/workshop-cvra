---
title: "数据存储及交互"
date: 2018-10-03T10:14:32-07:00
draft: false
weight: 30
---


## DynamoDB 表

本解决方案使用 DynamoDB 存储相关行程数据，DTC 故障代码数据等。共包含如下 DynamoDB 表，具体信息如下：

| DynamoDB 表 | 用途 | 数据示例 |
| ------ | ----------- | ----------- |
|AdTrackingTable|广告跟踪表|{ "action": "none", "created_at": "2020-05-25T03:10:23Z", "identified_at": "2020-05-25T03:10:23Z", "message": "Book your holiday party and get a $50 gift card:", "poi_id": "va_rst_1", "trip_id": "c3d597dd-bbf7-4dfc-8a34-6abc43f9c124", "updated_at": "2020-05-25T03:10:23Z", "vin": "XBKW3TRJUFYQ9TVHX"}| 
|DtcTable|常见 DTC 编码及解释|{ "description": "Mass or Volume Air Flow Circuit Malfunction", "dtc": "P0100", "steps": []}| 
|HealthReportTable|车辆健康报告|{vin,report_id}| 
|MarketingPoiTable|兴趣地点信息|{ "address": "700 Dranesville Rd, Herndon, VA 20170", "city": "Herndon", "latitude": "38.97674", "longitude": "-77.377427", "message": "Parents, don't forget, no school this Friday. Enjoy your long weekend!", "poi": "Town High School", "poi_id": "va_edu_1", "radius": "500", "state": "VA"}| 
|VehicleDtcTable|车辆故障|{ "acknowledged": false, "created_at": "2020-05-25T01:53:30Z", "description": "No description available.", "dtc": "C1959", "dtc_id": "yPe-8-A9G", "generated_at": "2020-05-25T01:53:29Z", "steps": [
], "updated_at": "2020-05-25T01:53:30Z", "vin": "XBKW3TRJUFYQ9TVHX"}| 
|VehicleOwnerTable|驾驶人员与车辆关联表|{ "nickname": "BMW", "owner_id": "5ec6644b9eb9fe198b7f973e", "vin": "XBKW3TRJUFYQ9TVHX"}| 
|VehicleTripTable|车辆行程数据表|{ "accelerator_pedal_position_mean": 18.309185124850682, "brake_mean": 11.255813953488373, "brake_pedal_status": false, "driver_safety_score": 66.4558315455128, "end_time": "2020-05-25T06:00:06.168Z", "engine_speed_mean": 2811.2972050532244, "fuel_consumed_since_restart": 0.16391932245133267, "fuel_level": 99.59023919387167, "geojson": { "bucket": "cvbeta-iotsimdatabucket-muuy18aggwi", "key": "trip/1Z644A08RBQY8SGKC/c6b9b824-6085-4539-8427-a45d63adf615.json" }, "high_acceleration_event": 2, "high_braking_event": 0, "high_speed_duration": 0, "idle_duration": 49404, "ignition_status": "off", "latitude": 38.918713, "longitude": -77.227279, "name": "aggregated_telemetrics", "odometer": 7.858865687675835, "oil_temp_mean": 217.89026056033126, "start_time": "2020-05-25T05:51:07.803Z", "timestamp": "2020-05-25 06:00:06.168000000", "torque_at_transmission_mean": 121.5500692832436, "transmission_gear_position": "first", "trip_id": "c6b9b824-6085-4539-8427-a45d63adf615", "vehicle_speed_mean": 52.574673993985506, "vin": "1Z644A08RBQY8SGKC"}| 

进入 DynamoDB Console 页面，查看 已经创建的DynamoDB表。

## AWS IoT 消息主题

AWS IoT设备网关使设备能够安全有效地与AWS IoT进行通信，已联网的车辆使用发布/订阅模型与云端进行通信。 在发布/订阅模型中，车辆使用 MQTT或 WebSocket 协议将消息发送到 AWS IoT 平台中特定的主题。 

| 消息类型 | 主题 | Action | 描述 |
| ------ | ----------- | ----------- | ----------- |
|Telematics（远程信息处理|connectedcar/telemetry/<VIN>|publish|车辆传感器和远程信息处理数据{ timestamp:x, trip_id:x, vin:x, name:x, value:x }|
|Vehicle Trip Info （车辆行驶数据）	|connectedcar/trip/<VIN>	|publish	|聚合车辆行驶数据|
|Diagnostic Trouble Code (诊断故障码)	|connectedcar/dtc/<VIN>	|publish	|诊断故障码（DTC）{ timestamp:x, trip_id:x, vin:x, name:'dtc', value:x }|
|Anomaly Alert (异常警告)	|connectedcar/alert/<VIN>/anomaly	|Subscribe|	异常检测告警{ type:’anomaly’,message:x }|
|DTC Alert (DTC 告警）	|connectedcar/alert/<VIN>/dtc	|Subscribe	|DTC 告警{ type:’dtc’,message:x }|
|Driver Score Alert (驾驶员安全评分告警）	|connectedcar/alert/<VIN>/driverscore	|Subscribe	|驾驶员安全评分告警{ type:’driverscore’,message:x}|
|Advertisement Alert(广告提醒）	| connectedcar/telemetry/<VIN>/info	|Subscribe|	广告提醒 { type:’info’,message:x }|

## AWS IoT 规则

当联网车辆将消息发布到 AWS IoT 平台后，AWS IoT规则引擎会根据预先定义的规则进行评估，确定是否需要触发动作（Action）。如果触发，则将消息进行转换或者转发到其他 AWS服务。 

例如可以将规则引擎配置为将车辆发送的实时数据从AWS IoT将转发到其他AWS服务，如Amazon Kinesis Streams或Amazon DynamoDB，或从一个AWS IoT 主题转发到另一个主题。数据也可以被发送到AWS Lambda上的自定义应用程序。

本解决方案共使用如下 AWS IoT 规则：

### ConnectedVehicleTelematicsDtc

功能：针对车辆发出的DTC 故障代码进行信息丰富，并存储数据到 DynamoDB表。

规则查询语句：
```sql
SELECT * FROM 'connectedcar/dtc/#'
```
操作：触发Lambda 处理函数 DtcServiceFunction

### ConnctedVehicleDriverScore

功能：针对驾驶员的驾驶行为进行评分，并存储数据到 DynamoDB表。

规则查询语句：
```sql
SELECT * FROM 'connectedcar/trip/#' WHERE ignition_status = 'off'
```

操作：触发Lambda 处理函数 DriverSafetyServiceFunction

### ConnectedVehicleTrip

功能：把车辆的行程数据存储到 DynamoDB。

规则查询语句：
```sql
SELECT * FROM 'connectedcar/trip/#'
```
操作：将全部或部分 MQTT 消息拆分并写入到 DynamoDB 表VehicleTripTable。MQTT消息载荷中的每个属性将写入到 DynamoDB表的单独一列。此操作处理的消息必须采用 JSON 格式。

### ConnectedVehicleJITR

功能：车辆第一次连接时进行注册。

规则查询语句：
```sql
SELECT * FROM '$aws/events/certificates/registered/certid'
```
操作：触发Lambda 处理函数 JitrServiceFunction

### ConnectedVehicleTelematicsStorage
功能：把车辆行驶过程中，通过车载的 Greengrass从各个传感器收集数据，并将数据发送到AWS IoT 平台。当数据发送到 AWS IoT 平台后，使用该规则把实时数据存放到 Amazon Kinesis Firehose 流。

规则查询语句：
```sql
SELECT * FROM 'connectedcar/telemetry/#'
```
操作：将消息发送到流名称为connected-vehicle-telemetry的Amazon Kinesis Firehose 流。

### ConnectedVehicleLocationBasedMarketing
功能：把车辆行驶过程中，会将车辆的实时位置信息（经纬度）发送到AWS Io。该规则根据经纬度信息查询当前车辆位置附近是否有用户感兴趣的地点。如果检索到感兴趣的地点，会通过 AWS SNS服务发送消息通知驾驶员。

规则查询语句：
```sql
SELECT * FROM 'connectedcar/telemetry/#' WHERE name = 'location'
```
操作：触发Lambda 处理函数 LocationBasedMarketingFunction。
