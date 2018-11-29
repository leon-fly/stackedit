
# 我家加油API

| version  | updated by  | update at | remark |
|:-------------: |:---------------:| -------------:|-------------:|
| v1.0      | LeonWang |         20180928 | Create

## 1. 投保接口（支持幂等性）
* 接口地址 ：https://test.mingzebx.com/api/policies/issuances
* 接口说明 ：用于我家加油投保
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
eyJoaXN0b3J5IjpbMTM5OTA3MzcyOSw5NjI1OTk2MzgsLTEwOD
IyMDg5MzAsLTczMzU3MzY4NSwtMTgyNTA2NTc2NSwtNTk1NzI5
ODc2LDE5NDY0MTQ5MjgsLTEwNTgzNTQ5MjcsLTExNzg0OTU0Nj
UsLTEyMzU2NzkzMzQsMTM4NTc2MDI3OSwtMTYwMTQ0NjE5MCwt
MTAzNzk2OTUyLC0xMDQ3MjMzODE1LDEyNDQ5MDc0ODIsLTEzOD
ExMzc3MDIsLTMwNTM3ODQ1MiwtNTIyODIyMDc2LDE0MzI2NzY5
MDcsMTQ3MDMxNjQ1Ml19
-->