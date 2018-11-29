
# 安华手机碎屏险API对接文档 - v1.0

本文档为 **安华手机碎屏险** Web服务API对接的详细说明，主要包括：对接流程、数据签名、错误处理和具体API接口这四个部分。

## 1. 对接流程

本产品的API对接主要涉及到保单询价、保单出单、保单查询三个接口。其中保单询价用于获取手机碎屏险的价格（基于指定手机的型号），于此同时该接口还会对保单进行核保。上述三个接口调用的基本流程如下：

![enter image description here](https://lh3.googleusercontent.com/fdmrNVuxSKmBSi9yXy8sVzQusT-f-e2ijfJQAzAKl7X1XNx5S67LNEGNTdJbmetF80kON4QpDRdZ9Q)

## 2. 数据签名

道可特会提供给每一个渠道方一对密钥：accessKey和secretKey（测试、生产环境各提供一套）。渠道方在调用本系统的接口时，需要利用这对密钥对请求接口的数据进行签名处理。API服务器端收到请求时，会用同样的方式对签名进行验证。通过这种机制保证仅有获得授权的渠道方可以调用本系统的接口。

***基本处理逻辑：***
> 将需要签名的内容和渠道方的secretKey进行字符串拼接，拼接后的内容使用MD5算法进行运算。

***通过API进行数据写请求（HTTP POST方法）时的签名处理逻辑：***
> 将HTTP POST请求的Body内容与secretKey进行字符串拼接，然后进行MD5运算，运算的结果再添加到请求URL的signature参数上

***通过API进行数据读请求（HTTP GET方法）时的签名处理逻辑：***
> 将请求URL中的参数（包含accessKey参数）按照**参数名称**按字母排序，然后使用&符号拼接，再与secretKey进行字符串拼接，最后对拼接内容进行MD5运算，运算的结果添加到请求URL的signature参数上
> 

**以保单查询接口为例说明**

1. HTTP GET请求路径及参数为
```
/api/policies/issuances?transactionNo=PA_JUNHANG_AH_SCR2018112900004
```
2. 获取URL中的参数，并附加accessKey参数后，按照参数名称排序，排序后使用&符号拼接后的结果为
```
accessKey=Q83KAkyo34&transactionNo=PA_JUNHANG_AH_SCR2018112900004
```
3. 将第2步的结果与secretKey(YtcxtLnPRcll5ceyZBSmWrdRspXcRWac)拼接后即为签名内容： 
```
accessKey=Q83KAkyo34&transactionNo=PA_JUNHANG_AH_SCR2018112900004YtcxtLnPRcll5ceyZBSmWrdRspXcRWac
```
4. 将第3步的内容进行MD5运算获得到签名结果
```
6cb478bc3bc8b0ee0e76ddbe3301bc92
```
5. 最终的请求URL为
```
/api/policies/issuances?accessKey=Q83KAkyo34&transactionNo=PA_JUNHANG_AH_SCR2018112900004&signature=6cb478bc3bc8b0ee0e76ddbe3301bc92
```

## 3. 错误处理

当API请求处理发生错误时，API服务器会返回类似如下结构的报文，调用方需根据返回的错误信息来调整API的请求。
```
{
    "timestamp": "2018-10-12T08:32:35.726+0000",
    "status": 401,
    "error": "Unauthorized",
    "message": "Unauthorized",
    "path": "/api/policies/issuances"
}
```

常见的错误状态如下：

| status | 字段说明 | 处理方式 |
|:------------- |:---------------|:-------------|
|401|认证未成功|确认accessKey与secretKey正确的前提下，检查签名算法是否正确|
|403|授权未成功|请确认API请求路径是否正确|
|404|数据|请确认API请求路径是否正确|
|500|服务器端错误|请根据message信息调整API请求

## 4. API接口

### 4.1. 询价接口

### 4.2. 投保接口（支持幂等性）

* 接口地址
	* 测试环境 https://test.mingzebx.com/api/policies/issuances
	* 生产环境 https://platform.mingzebx.com/api/policies/issuances
* 接口说明 ：保单出单接口
* 请求方式 ：POST
* 请求参数

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|agreementCode|协议号|String|√|由道可特提供给接入方产品对应协议号|
|refTransactionNo|交易号|String(128)|√|调用方提供的交易单号，一般为调用方系统该保单对应的订单号或流水号；本系统会根据此交易号做幂等性处理|
|planCode|方案号|String|√|由道可特提供给接入方|
|effectiveDate|起保时间|String|√|采用ISO8601日期格式|
|expiredDate|终保时间|String|√|采用ISO8601日期格式|
|policyHolder|投保人信息|Object|√|详见【投保人policyHolder】
|insuredObject|被保人信息|Object[]|√|详见【被保人insuredObject】

***投保人policyHolder***

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|id|身份证号|String(18)|√||
|name|姓名|String|√||

***被保人insuredObject***

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|name|姓名|String|√|被保人姓名|
|id|身份证号|String(18)|√|被保人的证件类型及号码|
|phoneNumber|手机号码|String(11)|√|被保人的手机号码|

* 返回参数

| 字段  | 字段名  | 类型及长度 | 是否必返回 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|transactionNo|保单交易号|String(128)|√|
|orderNo|保单订单号|String(128)|√|

* 请求示例

```
{
  "agreementCode": "A_JUNHANG_AH_SCR",
  "refTransactionNo": "REF201811280001",
  "effectiveDate": "2018-11-29T00:00:00.000+0800",
  "expiredDate": "2019-11-28T23:59:59.000+0800",
  "planCode": "P_AH_SCR_0",
  "policyHolder": {
    "name": "苏宁第一门店",
    "id": "SOCIAL_CREDIT:9136110206972762X5"
  },
  "insuredPersons": [
    {
      "name": "小郑",
      "id": "ID_CARD:320303198111150033",
      "phoneNumber": "13912345678"
    }
  ],
  "properties": {
    "IMEI": "1234567890",
    "model": "iPhone 7:001"
  }
}
```

* 成功返回示例

```
{
    "transactionNo": "OD2018100800008"
}
```

### 4.3 保单查询接口
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTY0MDQ5MzM0LDE5OTE3MTY0MzUsLTE5Mz
A2NTYzNzMsLTE0NzgyNjk3MSwxOTY2Mzg5NDBdfQ==
-->