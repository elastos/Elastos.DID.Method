# 亦来云DID解析接口规范

区块链驱动的智能万维网



----

亦来云Core团队

2019年11月30日



**版本**

0.2



**内容说明**

此文档是亦来云DID解析接口规范，由亦来云Core团队发布并维护。主要说明了亦来云DID解析的接口定义，以及相关的请求和响应数据定义。未来我们会持续升级此文档，以体现亦来云DID技术最新发展状态。



**版权声明**
此文档著作权归亦来云Core团队所有，保留所有权利。

----


[TOC]

----

## 概述

Elastos采用[JSON-RPC](https://www.jsonrpc.org/specification)接口提供DID解析方法。关于[JSON-RPC](https://www.jsonrpc.org/specification)的具体细节，可以参考官网规范。



## Resolve  request

### method

字符串值，其中包含要调用的方法的名称。对DID Resolve请求，值为"resolvedid"。



### params

对象值，包含在resolvedid方法调用需要使用的参数。属性定义如下：

- did

  DID字符串。可以是完整的DID字符串如：*did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4*，或者仅method specific string如：*iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4*。

- all

  是否获取该DID的所有历史操作。Boolean类型，true或者false。



### id

客户端设定的请求标识符，必须包含字符串，数字或NULL值（如果包含）。 值通常不应为null，并且数字不应包含小数部分。



## Resolve response

### jsonrpc

JSON-RPC的默认响应属性，字符串值，指定JSON-RPC协议的版本。 必须为“ 2.0”。



### result

如果Resolve执行成功，则包含此成员，值为resolve的结果对象。如果调用resolve时出错，则该成员不存在或者为null。DID Resolver解析结果对象的定义参考[Resolve result object](#resolve-result-object)。



### error

如果Resolve执行正常，则该成员不存在或者为null。如果Resolve执行错误，那么包含该成员，值为错误信息对象。错误信息对象的定义参考[Error object](#error-object)。


### id

此成员是必需的。它必须与请求对象中id成员的值相同。如果在请求对象中检查ID时发生错误（例如解析错误/无效请求），则该值必须为null。



## Resolve result object

当DID Resolve成功执行时，response中必须包含result成员，其值是具有以下成员的对象：

### did

请求的目标DID。

### status

数值类型的状态码，指示目标DID解析的结果状态。该值为整数。值定义如下：

| status | meaning                                                      |
| ------ | ------------------------------------------------------------ |
| 0      | DID文档有效。transaction成员包含请求的DID交易结果数组。      |
| 1      | DID文档过期(expired)。transaction成员包含请求的DID交易结果数组。 |
| 2      | DID文档被撤销(deactivated)。transaction成员包含请求的DID交易结果数组。 |
| 3      | DID不存在。结果对象不包含transaction成员。                   |

> DID 解析时，不论DID状态如何，只要正常执行了解析，都应该返回本对象，而不是返回error对象。通过不同的status值来表示不同的解析结果。比如DID不存在、过期、被撤销等状况，都属于正常，应该返回本对象。只有在Resolver服务不能正常执行解析行为时，才返回error对象，一般应该是JSON-RPC的错误，或者Resover自身的错误。

### transaction

数组对象，元素为被请求目标DID的ID交易及操作信息。元素是包含以下成员的对象：

* **txid**

  字符串成员，为当前ID交易的transaction id。

* **timestamp**

  交易时间，值必须是有效的[RFC3339](https://tools.ietf.org/html/rfc3339)组合日期和时间的字符串值，同时必须标准化为UTC时间，尾随“Z”。

* **operation**

  对象成员，为当前ID交易的payload，即DID操作的JSON对象。

当[resolve did请求的参数](#params)`all`为*false*时，transaction数组对象仅包含目标DID的最后一个交易的信息。当`all`为*true*时，transaction数组对象包含目标DID的所有交易信息，数组元素按照ID交易的时间降序存放，即第0个为最新ID交易。



## Error object

当Resolve调用遇到错误时，response中必须包含error成员，其值是具有以下成员的对象：

### code

数值类型的错误代码，指示发生的错误类型。该值为整数。

按照[规范](https://www.jsonrpc.org/specification)定义，从-32768到-32000的错误代码（包括-32768至-32000）保留给预定义的错误。 此范围内未在下面明确定义的任何代码都保留供将来使用。 

| code             | message          | meaning                                                      |
| ---------------- | ---------------- | ------------------------------------------------------------ |
| -32700           | Parse error      | Invalid JSON was received by the server. An error occurred on the server while parsing the JSON text. |
| -32600           | Invalid Request  | The JSON sent is not a valid Request object.                 |
| -32601           | Method not found | The method does not exist / is not available.                |
| -32602           | Invalid params   | Invalid method parameter(s).                                 |
| -32603           | Internal error   | Internal JSON-RPC error.                                     |
| -32000 to -32099 | Server error     | Reserved for implementation-defined server-errors.           |

### message

对错误进行简短描述的字符串。一般是一个简洁的句子。

### data

可选成员，包含有关错误的其他信息。值由Resolver服务器定义（例如，详细的错误信息，嵌套的错误等）。



## 示例

### 解析DID文档，并且文档有效

#### Request

```json
{
    "method": "resolvedid",
    "params":{
        "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
        "all": false
    },
    "id": "8555cbd1afbf3b8fd8748464ee949574"
}
```

#### Response

```json
{ 
  "id": "8555cbd1afbf3b8fd8748464ee949574",
  "jsonrpc": "2.0",
  "result": {
    "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
    "status": 0,
    "transaction": [{
      "txid": "467491e6be79fbc6...ce9192d6f15ca81e",
      "timestamp": "2019-08-10T17:30:00Z",
      "operation": {
        "header": {
          "specification": "elastos/did/1.0",
          "operation": "update",
          "previousTxid": "3641de55f368583c...8917756a872093d2"
        },
        "payload": "eyJpZCI6ImRpZDplbGFzdG9zOmlWUGFk...UMDI6MDA6MDBaIn0",
        "proof": { 
          "type":"ECDSAsecp256r1",
          "verificationMethod":"#primary",
          "signature":"IdcfNkS7yA_GHL6g...6lWq7eI9OJGaNPbg"
        }
      }
    }]
  }
}
```



### 解析DID文档，文档过期

#### Request

```json
{
    "method": "resolvedid",
    "params":{
        "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
        "all": false
    },
    "id": "8555cbd1afbf3b8fd8748464ee949574"
}
```

#### Response

```json
{ 
  "id": "8555cbd1afbf3b8fd8748464ee949574",
  "jsonrpc": "2.0",
  "result": {
    "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
    "status": 1,
    "transaction": [{
      "txid": "467491e6be79fbc6...ce9192d6f15ca81e",
      "timestamp": "2019-08-10T17:30:00Z",
      "operation": {
        "header": {
          "specification": "elastos/did/1.0",
          "operation": "update",
          "previousTxid": "3641de55f368583c...8917756a872093d2"
        },
        "payload": "eyJpZCI6ImRpZDplbGFzdG9zOmlWUGFk...UMDI6MDA6MDBaIn0",
        "proof": { 
          "type":"ECDSAsecp256r1",
          "verificationMethod":"#primary",
          "signature":"IdcfNkS7yA_GHL6g...6lWq7eI9OJGaNPbg"
        }
      }
    }]
  }
}
```



### 解析DID文档，DID被撤销

#### Request

```json
{
    "method": "resolvedid",
    "params":{
        "did":"iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
        "all": false
    },
    "id": "8555cbd1afbf3b8fd8748464ee949574"
}
```

#### Response

```json
{ 
  "id": "8555cbd1afbf3b8fd8748464ee949574",
  "jsonrpc": "2.0",
  "result": {
    "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
    "status": 2,
    "transaction": [{
      "txid": "467491e6be79fbc6...ce9192d6f15ca81e",
      "timestamp": "2019-11-10T21:30:00Z",
      "operation": {
        "header": {
          "specification": "elastos/did/1.0",
          "operation": "deactivate"
        },
        "payload": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
        "proof": { 
          "verificationMethod":"#recovery",
          "signature":"IdcfNkS7yA_GHL6g...6lWq7eI9OJGaNPbg"
        }
      }
    }]
  }
}
```


### 解析DID文档，DID不存在

#### Request

```json
{
    "method": "resolvedid",
    "params":{
        "did": "iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
        "all": false
    },
    "id": "8555cbd1afbf3b8fd8748464ee949574"
}
```

#### Response

```json
{ 
  "id": "8555cbd1afbf3b8fd8748464ee949574",
  "jsonrpc": "2.0",
  "result": {
    "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
    "status": 3
  }
}
```



### 解析DID所有操作记录

#### Request

```json
{
    "method": "resolvedid",
    "params":{
        "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
        "all": true
    },
    "id": "8555cbd1afbf3b8fd8748464ee949574"
}
```

#### Response

```json
{ 
  "id": "8555cbd1afbf3b8fd8748464ee949574",
  "jsonrpc": "2.0",
  "result": {
    "did": "did:elastos:iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
    "status": 0, // Maybe 0, 1, 2
    "transaction": [{
      "txid": "467491e6be79fbc6...ce9192d6f15ca81e",
      "timestamp": "2019-12-1T17:30:00Z",
      "operation": {
        "header": {
          "specification": "elastos/did/1.0",
          "operation": "update",
          "previousTxid": "3641de55f368583c...8917756a872093d2"
        },
        "payload": "eyJpZCI6ImRpZDplbGFzdG9zOmlWUGFk...UMDI6MDA6MDBaIn0",
        "proof": { 
          "type":"ECDSAsecp256r1",
          "verificationMethod":"#primary",
          "signature":"IdcfNkS7yA_GHL6g...6lWq7eI9OJGaNPbg"
        }
      }
    }, {
      "txid": "3641de55f368583c...8917756a872093d2",
      "timestamp": "2019-10-18T10:30:00Z",
      "operation": {
        "header": {
          "specification": "elastos/did/1.0",
          "operation": "update",
          "previousTxid": "3a2936c4777f02a9...3f683c82a1aa378c"
        },
        "payload": "E1NndTUkR2dEQ1SEt2Q1BOcnlITszcV...ZTVTQiLCJwdWJsaW",
        "proof": { 
          "type":"ECDSAsecp256r1",
          "verificationMethod":"#primary",
          "signature":"xOanVzekR2aGk5aW...l2NlJxQ0RHc1Rjc0"
        }
      }      
    }, {
      "txid": "3a2936c4777f02a9...3f683c82a1aa378c",
      "timestamp": "2019-08-10T17:30:00Z",
      "operation": {
        "header": {
          "specification": "elastos/did/1.0",
          "operation": "create"
        },
        "payload": "NLZXkiOlt7ImlkIjoiI3ByaW1hcnkiLC...JwdWJsaWNLZXlCYX",
        "proof": { 
          "type":"ECDSAsecp256r1",
          "verificationMethod":"#primary",
          "signature":"NlNTgiOiJwdmZBUU...68dTRnTm1hdDZHTE"
        }
      }
    }]
  }
}
```



### 解析DID错误

#### Request

```json
{
    "method": "resolvedid",
    "params":{
        "did": "iVPadJq56wSRDvtD5HKvCPNryHMk3qVSU4",
        "all": false
    },
    "id": "8555cbd1afbf3b8fd8748464ee949574"
}
```

#### Response

```json
{ 
  "id": "8555cbd1afbf3b8fd8748464ee949574",
  "jsonrpc": "2.0",
  "error": {
    "code": -32001,
    "message": "Resolver internal error."
  }
}
```

