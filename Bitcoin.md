
# 安华手机碎屏险API对接文档

## 1. 投保接口（支持幂等性）

* 接口地址
	* 测试环境 https://test.mingzebx.com/api/policies/issuances
	* 生产环境 https://platform.mingzebx.com/api/policies/issuances
* 接口说明 ：保单出单接口
* 请求方式 ：POST
* 请求参数

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|agreementCode|协议号|String|√|由道可特提供给接入方产品对应协议号|
|refTransactionNo|交易号|String(128)|√|调用方提供的交易单号，一般为diao yogn fan（用于幂等支持）|
|planCode|方案号|String|√|由道可特提供给接入方|
|policyHolder|投保人信息|Object|√|详见【投保人policyHolder】
|insuredObject|被保人信息|Object[]|√|详见【被保人insuredObject】
|effectiveDate|起保时间|Date|√|采用ISO8601格式|
|expiredDate|终保时间|Date|√|采用ISO8601格式|

***投保人policyHolder***

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|id|身份证号|String(18)|√|
|personName|姓名|String|√|
|phoneNumber|手机号码|String(11)|√|
***被保人insuredObject***

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|id|身份证号|String(18)|√|
|personName|姓名|String|√|
|phoneNumber|手机号码|String(11)|√|
|relationshipToPolicyHolder|是投保人的什么（关系）|String(2)|√|00:本人  01:丈夫 02:妻子 03：儿子 04：女儿|



* 返回参数

| 字段  | 字段名  | 类型及长度 | 是否必填 |字段说明|
|:------------- |:---------------|:-------------|:-------------:|:---------|
|transactionNo|交易号|String(128)|√|

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


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MjEzMzk3NTcsOTYyNTk5NjM4LC0xMD
gyMjA4OTMwLC03MzM1NzM2ODUsLTE4MjUwNjU3NjUsLTU5NTcy
OTg3NiwxOTQ2NDE0OTI4LC0xMDU4MzU0OTI3LC0xMTc4NDk1ND
Y1LC0xMjM1Njc5MzM0LDEzODU3NjAyNzksLTE2MDE0NDYxOTAs
LTEwMzc5Njk1MiwtMTA0NzIzMzgxNSwxMjQ0OTA3NDgyLC0xMz
gxMTM3NzAyLC0zMDUzNzg0NTIsLTUyMjgyMjA3NiwxNDMyNjc2
OTA3LDE0NzAzMTY0NTJdfQ==
-->