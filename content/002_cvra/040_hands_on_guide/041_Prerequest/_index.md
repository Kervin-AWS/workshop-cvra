---
title: "准备工作"
date: 2018-10-03T10:17:52-07:00
draft: false
weight: 10
---

### 准备用于 OIDC（OpenID Connect） 的 JWT token 加密及验证的字符串

为了确保系统安全，建议使用包含随机字符的字符串作为OIDC的加密密钥。可使用如下的bash脚本生成 包含32 位随机字符的字符串，该字符串将用加密 JWT Token 及用于API请求时密码比对。
示例命令如下：

```shell
cat /dev/urandom | LC_CTYPE=C tr -dc a-z-0-9 | head -c${1:-32} ;echo
```

返回随机字符串示例： 
a1ef50c2e6a5e31f0f5ddc4a8f0fad41

### 准备用于 API Gateway测试的 Restful API 工具

该解决方案部署完成后，只是提供了 API Gateway 服务接口及 DynamoDB 表 S3 存储桶等数据存储服务。而没有提供一个可视化的界面供用户使用。另外由于 API Gateway 提供相关服务导出为 Swagger 或 OpenAPI3 接口文件，可以借助于 Postman 或 Swagger UI ( https://swagger.io/tools/swagger-ui/ )这两款免费的API 调用工具供使用。
借助于这两种工具，方便您测试及验证功能使用。

### 准备 Docker 环境，并拉取 Swagger-ui 镜像

如果想使用 Swagger UI 的 Docker 版本进行测试，需提前准备好 Docker 运行环境并获得镜像(swaggerapi/swagger-ui)。具体命令如下：

```shell
docker pull swaggerapi/swagger-ui
```

具体步骤，参考该文档 https://swagger.io/docs/open-source-tools/swagger-ui/usage/installation/
