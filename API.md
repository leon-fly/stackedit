
# 安华手机碎屏险API对接文档 - v1.1

本文档为 **安华手机碎屏险** Web服务API对接的详细说明，主要包括：对接流程、数据签名、错误处理和具体API接口这四个部分。

## 1. 对接流程

本产品的API对接主要涉及到保单询价、保单出单、保单注销、保单查询共四个接口。其中保单询价用于获取手机碎屏险的价格（基于指定手机的型号），于此同时该接口还会对保单进行核保。上述四个接口调用的基本流程如下：

![enter image description here](https://web-1256596879.cos.ap-shanghai.myqcloud.com/docs/%E5%AE%89%E5%8D%8E%E6%B0%B4%E5%B9%B3API%E5%AF%B9%E6%8E%A5%E6%B5%81%E7%A8%8B.png)

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
|404|数据查询失败|请先确认请求路径是否正确，再确认查询的参数值是否正确|
|500|服务器端错误|请根据具体的message信息调整API请求

## 4. API接口

所有接口提供三套集成环境，相应的服务器地址如下：
* 测试环境 https://test.mingzebx.com
* 预发布环境 https://release.mingzebx.com
* 生产环境 https://platform.mingzebx.com

### 4.1. 询价接口

* 接口路径 ：**/api/policies/quotations**
* 接口说明 ：保单询价接口
* 请求方式 ：POST
* 请求参数

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|agreementCode|协议号|String|√|请填写固定值 A_JUNHANG_AH_SCR|
|planCode|方案号|String|√|请填写固定值 P_AH_SCR_0|
|effectiveDate|起保时间|String|√|采用ISO8601日期格式，此产品为当前出单时间+7天后的零点零分|
|expiredDate|终保时间|String|√|采用ISO8601日期格式，此产品为起保时间1年后的23:59:59|
|policyHolder|投保人信息|Object|√|详见【投保人policyHolder】
|insuredObject|被保人信息|Object[]|√|详见【被保人insuredPerson】
|properties|保单标的属性|Object[]|√|详见【保单标的属性properties】

* 返回参数

| 字段  | 字段名  | 类型及长度 | 是否必返回 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|transactionNo|询价交易号|String|√|保单询价交易号|
|premium|保费|Number|√|询价返回的保费|

***投保人 policyHolder***

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|name|企业名称|String|√|公司名称|
|id|企业的统一社会信用代码|String(18)|√|格式为‘SOCIAL_CREDIT:#{18位统一社会信用代码}’ |

***被保人 insuredPerson***

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|name|姓名|String|√|被保人姓名|
|id|身份证号|String(18)|√|被保人的证件类型及号码，格式为‘ID_CARD:#{18位身份证号}’ |
|phoneNumber|手机号码|String(11)|√|被保人的手机号码|

***保单标的属性 properties***

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|IMEI|手机IMEI号|String|√|15或17位的手机IMEI号|
|model|手机型号对应的编码|String|√|手机型号对应的编码|

-   请求示例
```
{
  "agreementCode": "A_JUNHANG_AH_SCR",
  "effectiveDate": "2018-11-29T00:00:00.000+0800",
  "expiredDate": "2019-11-28T23:59:59.000+0800",
  "planCode": "P_AH_SCR_0",
  "policyHolder": {
    "name": "杭州骏杭智能科技有限公司",
    "id": "SOCIAL_CREDIT:91330108MA2CET7U7W"
  },
  "insuredPersons": [
    {
      "name": "小郑",
      "id": "ID_CARD:320303198111150033",
      "phoneNumber": "13912345678"
    }
  ],
  "properties": {
    "IMEI": "357805023984942",
    "model": "iPhone7-001"
  }
}
```
* 成功返回示例

```
{
  "transactionNo": "QA_JUNHANG_AH_SCR2018112900001",
  "premium": 36
}
```

### 4.2. 投保接口（支持幂等性）

* 接口路径 ：**/api/policies/issuances**
* 接口说明 ：保单出单接口
* 请求方式 ：POST
* 请求参数

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|agreementCode|协议号|String|√|请填写固定值 A_JUNHANG_AH_SCR|
|refTransactionNo|交易号|String|√|调用方提供的交易单号，一般为调用方系统该保单对应的订单号或流水号；本系统会根据此交易号做幂等性处理|
|planCode|方案号|String|√|请填写固定值 P_AH_SCR_0|
|effectiveDate|起保时间|String|√|采用ISO8601日期格式，此产品为当前出单时间+7天后的零点零分|
|expiredDate|终保时间|String|√|采用ISO8601日期格式，此产品为起保时间1年后的23:59:59|
|premium|保费|Number|√|计算获得的保单保费|
|policyHolder|投保人信息|Object|√|详见【投保人policyHolder】
|insuredObject|被保人信息|Object[]|√|详见【被保人insuredPerson】
|properties|保单标的属性|Object[]|√|详见【保单标的属性properties】

* 返回参数

| 字段  | 字段名  | 类型及长度 | 是否必返回 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|transactionNo|保单交易号|String|√|保单交易号，用于保单查询API的调用|
|orderNo|保单订单号|String|√|保单对应的订单号，本产品不需此参数|

* 请求示例

```
{
  "agreementCode": "A_JUNHANG_AH_SCR",
  "refTransactionNo": "REF201811280001",
  "effectiveDate": "2018-11-29T00:00:00.000+0800",
  "expiredDate": "2019-11-28T23:59:59.000+0800",
  "premium": 36,
  "planCode": "P_AH_SCR_0",
  "policyHolder": {
    "name": "杭州骏杭智能科技有限公司",
    "id": "SOCIAL_CREDIT:91330108MA2CET7U7W"
  },
  "insuredPersons": [
    {
      "name": "小郑",
      "id": "ID_CARD:320303198111150033",
      "phoneNumber": "13912345678"
    }
  ],
  "properties": {
    "IMEI": "357805023984942",
    "model": "iPhone7-001"
  }
}
```

* 成功返回示例

```
{
  "transactionNo": "PA_JUNHANG_AH_SCR2018112900004",
  "orderNo": "OD2018112900004"
}
```
### 4.3 保单注销接口

本接口用于保单生效前对保单进行注销。如果当前时间已过保单生效开始时间，或者保单的状态为未成功出单，无法对该保单进行注销操作。

调用保单注销接口，系统会采用异步的方式向保险公司发起保单注销请求，所以渠道方在调用**保单注销**接口后，需要轮询调用**保单查询**接口获取保单最新状态：当保单状态变为 **TERMINATED** 时，表示保单已经成功注销。

* 接口路径 ：**/api/policies/cancellations**
* 接口说明 ：保单出单接口
* 请求方式 ：POST
* 请求参数

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|policyTransactionNo|保单交易号|String|√|调用保单出单接口返回的保单交易号|
|applicationType|保单注销申请类型|String|√|保单注销申请类型：BY_CHANNEL 渠道方申请，BY_CUSTOMER 客户申请，OTHER 其他类型|
|applicationReason|保单注销申请原因|String|N|保单注销申请原因说明|

* 返回参数

| 字段  | 字段名  | 类型及长度 | 是否必返回 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|transactionNo|保单交易号|String|√|保单交易号|
|policyNo|保险公司保单号|String|√|保险公司保单号|
|policyStatus|保险公司保单号|String|√|保单状态，包含ISSUED（出单成功）、TERMINATED (保单中止)|

* 请求示例
```
{
  "policyTransactionNo": "PA_JUNHANG_AH_SCR2018120600014",
  "applicationType": "BY_CUSTOMER",
  "applicationReason": "取消购买手机"
}
```
* 成功返回示例
```
{
  "policyTransactionNo": "PA_JUNHANG_AH_SCR2018120600014",
  "policyNo": "8G2013445201800000000055",
  "policyStatus": "ISSUED"
}
```


### 4.4 保单查询接口

当渠道方调用保单出单接口后，系统会异步的将保单出单信息发送给保险公司的核心系统以获取保单号，一般这个时间间隔会控制在10秒钟内。渠道方需要使用保单查询接口来轮询的方式查询最终保险公司返回的保单号，建议每隔3秒查询一次，最多查询4次。

* 接口路径 ：**/api/policies/issuances**
* 接口说明 ：保单出单接口
* 请求方式 ：GET
* 请求参数

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|transactionNo|保单交易号|String|√|出单接口返回的transactionNo|

* 返回参数

| 字段 | 字段名 | 类型及长度 | 字段说明 |
|:------------- |:---------------|:-------------|:-------------|
|policyNo|保单号|String|保险公司返回的保单号|
|productName|产品名称|Number|产品名称|
|policyStatus|保单状态|String|保单状态，包含WAITING_FOR_PAYMENT（待支付）、WAITING_FOR_HANDLING（待人工处理）、ISSUED（出单成功）、TERMINATED (保单中止)|
|underwritingStatus|核保状态|String|核保状态：PASSED 或 FAILED|
|effectiveDate|起保时间|String|采用ISO8601日期格式|
|expiredDate|终保时间|String|采用ISO8601日期格式|
|premium|保费|Number|该保单对应的保费|

**保单查询返回示例**
```
{
  "policyNo": null,
  "productName": "安华手机碎屏险",
  "policyStatus": "WAITING_FOR_PAYMENT",
  "underwritingStatus": "PASSED",
  "effectiveDate": "2018-11-29T00:00:00.000+0800",
  "expiredDate": "2019-11-28T23:59:59.000+0800",
  "premium": 36
}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTA3ODE5NDc1LDEzNzczNDMxMjcsNTQyMT
k2MDg4LDIwNDM2ODUyNjMsMTYxMzA3MzcwMSwtMTYwNjQ4ODcy
NywtMTM3MDk4NjkzNiwtMzk0NjA0MTg1LDE3OTE4ODQ1MTYsNT
Q5NjM5MTk3LDEzNzU4MDA5NiwtMTEwMDI4NDk3MSwxMjYyMjg4
MzEsLTEyMjI4NzgwOTEsMTgxMTcwNDAzNSwxMjAzODkxMzUxLD
k5MDgwMzU0OSwtNTg0ODUyMTkwLC0xMDA4MjMwNjczLDU2MTQy
NTc3NF19
-->