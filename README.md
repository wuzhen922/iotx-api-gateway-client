
# 介绍
阿里云IoT网关，旨在通过http/https协议将[物联网套件](https://help.aliyun.com/product/30520.html?spm=a2c4g.750001.7.4.XRrIyT)及衍生服务开放给开发者。本文档主要介绍如何访问阿里云IoT网关。IoT网关依赖阿里云[API网关](https://help.aliyun.com/product/29462.html?spm=a2c4g.750001.3.44.ieDVIY)，IoT网关在阿里云API网关的基础上扩展了IoT的特殊业务协议。

# 协议框架

```json
    {
        "id": "1509086454180",
        "version": "1.0",
        "request": { },
        "params": { }
    }
```

**id**: 一次请求的标示，用于请求跟踪问题排查，通常用UUID生成<br/>
**version**: 协议版本，当前版本1.0<br/>
**request**: JSON对象，与业务无关的通用参数，包括如下可用参数<br/>

*apiVer*:API版本 必填<br/>
*iotToken*:登录用户的token（客户端） 非必填<br/>
*cloudToken*:云端资源token（云端）  非必填<br/>
*language*:国际化扩展，语言。 	 非必填<br/>
*locale*:国际化扩展，地理位置、ip。 非必填<br/>

**params**: JSON对象，访问指定api的业务参数<br/>
# 示例
假设您需要访问IoT网关提供的如下API：<br/>
**定义描述**<br/>
```
    path:/awss/enrollee/list/get
    版本:1.0.2
    描述:分页查询发现设备列表，用于配网流程
    鉴权:是，客户端SDK需启用身份的鉴权
```
    
**请求参数**<br/>
```
参数名称:pageSize,参数类型:Integer,是否必填:是,描述:分页大小
参数名称:pageNum,参数类型:Integer,是否必填:是,描述:页编号
```


**请求示例**
版本1.0.2对应request中的apiVer，由于API要求用户身份验证，request中必须包含参数iotToken。请求参数统一放到params中。

```json
{
    "id": "1509086454180",
    "version": "1.0",
    "request": {
        "apiVer": "1.0.2",
        "iotToken": "token"
    },
    "params": {
        "pageSize": 10,
        "pageNum": 1
    }
}
```

假设您已经拥有了访问IoT网关的appKey和appSecret 分别为appkey1和appsecret1，通过IoT网关访问如下：
```
        SyncApiClient syncClient = SyncApiClient.newBuilder()
            .appKey("appkey1")
            .appSecret("appsecret1")
            .build();

        IoTApiRequest request = new IoTApiRequest();
        //设置api的版本
        request.setApiVer("1.0.2");
        request.setId("123456");

        //如果需要登陆，设置当前的会话的token，token通过登录api获取
        request.setIoTToken("token");

        //设置参数
        request.putParam("pageSize", 10);
        request.putParam("pageNum", 1);
        

        //请求参数域名、path
        String host = "api.link.aliyun.com";
        String path = "/awss/enrollee/list/get";
        ApiResponse response = syncClient.postBody(host, path, request);

        System.out.println(
            "response code = " + response.getStatusCode() + " response content = " + new String(response.getBody(),
                "utf-8"));
```

# 依赖
```
<dependency>
    <groupId>com.aliyun.api.gateway</groupId>
    <artifactId>sdk-core-java</artifactId>
    <version>1.0.4</version>
</dependency>
```