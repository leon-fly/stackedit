
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
|refTransactionNo|交易号|String(128)|√|必须在我家加油平台唯一（用于幂等支持）|
|agreementCode|协议号|String|√|由道可特提供给接入方产品对应协议号(详见产品信息文档)|
|planCode|方案号|String|√|由道可特提供给接入方(详见产品信息文档)|
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
  "refTransactionNo": "LH2018092812092101",
  "agreementCode": "ARG_SL_GL_01",
  "planCode": "SL_GL_CVAI_03_1",
  "effectiveDate": "2018-09-29T16:00:00.000Z",
  "expiredDate": "2019-09-29T15:59:59.000Z",
  "policyHolder": {
    "id": "ID_CARD: 320303198111150033",
    "personName": "刘德华",
    "phoneNumber": "13912345678"
  },
  "insuredPersons": [
    {
      "id": "ID_CARD:320303198111150033",
      "personName": "刘德华",
      "phoneNumber": "13912345678",
      "relationshipToInsuredPerson":"00"
    }
  ]
}

```

* 成功返回示例

```
{
    "transactionNo": "OD2018100800008"
}
```


<!--stackedit_data:
eyJoaXN0b3J5IjpbMjI5MDkzNDU2LDk2MjU5OTYzOCwtMTA4Mj
IwODkzMCwtNzMzNTczNjg1LC0xODI1MDY1NzY1LC01OTU3Mjk4
NzYsMTk0NjQxNDkyOCwtMTA1ODM1NDkyNywtMTE3ODQ5NTQ2NS
wtMTIzNTY3OTMzNCwxMzg1NzYwMjc5LC0xNjAxNDQ2MTkwLC0x
MDM3OTY5NTIsLTEwNDcyMzM4MTUsMTI0NDkwNzQ4MiwtMTM4MT
EzNzcwMiwtMzA1Mzc4NDUyLC01MjI4MjIwNzYsMTQzMjY3Njkw
NywxNDcwMzE2NDUyXX0=
-->