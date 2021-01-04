# 亦来云可验证声明规范 v0.2

区块链驱动的智能万维网

----

亦来云基金会

2019年11月30日

**版本**

0.2

**内容说明**

此文档是亦来云可验证声明技术规范，由亦来云基金会发布并维护。主要说明了亦来云可验证声明的通用数据模型和格式，以及相关的操作。未来我们会持续升级此文档，以体现亦来云DID以及相关技术最新发展状态。

**版权声明**

此文档著作权归亦来云Core团队所有，保留所有权利。

----

[TOC]

----

## 概述

可验证声明是针对去中心DID设计的，由用户能够实现自我主权并自我控制的个人凭证。在现有的以服务为中心的体系结构中，用户在不放弃已有数字身份的情况下很难改变他们的服务提供商，这导致了供应商被锁定无法转换、身份脆弱性以及用户隐私的泄露。而通过可验证声明，则可以让用户在网络上以更容易、更安全的方式来表达和交换已被第三方验证的凭证。

原则上可验证声明由对应DID主体持有，比如存放在其终端设备上的DID客户端软件中，并由客户端提供必要的保护措施。从隐私安全的角度看，不建议将可验证声明本身上链，不论加密与否，不加密直接上链相当于把个人信息向世界公开；即使加密，加密算法的安全也是相对的，当下安全不能保证未来安全。

### 本规范的目的

制定本规范的目的是在[W3C可验证声明规范](https://www.w3.org/TR/verifiable-claims-data-model/)框架下，定义一个亦来云体系内标准的可验证声明数据模型，从而实现数字身份在亦来云生态体系内的标准化，使得任何dApp都可以按照这个标准，使用亦来云DID关联的身份凭证。

### 设计目标

本规范的设计是基于[W3C可验证声明规范](https://www.w3.org/TR/verifiable-claims-data-model/)，同时参考了[DIF](https://identity.foundation/)相关的文档和代码，采用了W3C规范的一个子集。

简洁性和易用性也是本设计的一个重要因素，希望可验证声明能够在人类可读性、结构化、便于处理和使用等方面达到一个合理的平衡。

## 数据模型

### 数据格式

可验证声明**必须**是符合[RFC8259](https://tools.ietf.org/html/rfc8259)的单个JSON对象。亦来云可验证声明的描述不采用W3C规范中指定的[JSON-LD](https://www.w3.org/TR/json-ld/)格式，仅使用简单的JSON，这样的设计是为了保持可验证声明的简洁和应用开发的方便。相关的属性、命名保持和W3C规范中的定义一致；如果W3C规范没有相关的定义，则按照规范扩展的要求增加定义并作为亦来云可验证声明的标准。同时为了和W3C可验证声明标准互操作，亦来云可验证声明将提供到W3C可验证声明的映射机制。

### 可验证凭证/Verifiable Credentials

凭证是由实体作出的一个或多个声明的集合。 凭证应该包括用于描述凭证属性的标识符和元数据，诸如发行者，到期日期和时间，用于验证目的的公钥，撤销机制等。 元数据和凭证主题由发行者签名。可验证的凭证是一组防篡改的声明和元数据，可以通过密码学的方法证明是否如声明中所声称的实体颁发并且未被篡改。

可验证凭证可以包含的属性定义如下。

#### 标识/id

`id`是用来标记特定凭证的，应用或者使用者也可以通过标识来引用特定的凭证。`id`的值**必须**是单个符合[RFC3986的URI](https://tools.ietf.org/html/rfc3986)。凭证必须具有`id`属性。

#### 类型/type

按照[W3C可验证证明规范中的定义](https://www.w3.org/TR/verifiable-claims-data-model/#types)，`type`是由具体实现软件系统来定义的用来区分凭证信息类型的名称。凭证必须具有`type`属性。

`type`的值是一个用来表达类型名称的无序集，在JSON中表现为一个数组。这也意味着一个凭证可以属于多种不同的类型，用于不同的场景中。

为了亦来云生态内dApps能够统一对类型的理解，本规范定义了以下几个基本的类型：

| 类型名称                     | 说明                                                                                                                      |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| SelfProclaimedCredential     | DID持有者自主声明的凭证，没有第三方证明。                                                                                 |
| BasicProfileCredential       | 基本个人信息凭证，可包含姓名、昵称、性别、年龄、居住地、语言等信息。                                                      |
| ApplicationProfileCredential | 特定应用的基本个人信息凭证，具体信息和应用相关。                                                                          |
| InternetAccountCredential    | 互联网服务账号凭证。仅支持Google / Microsoft / Facebook / Twitter / Wechat / QQ / Taobao / Alipay / Weibo，以及邮件地址。 |

> 上述类型定义供应用参考，不能覆盖的由应用去扩展。

例如一个自我声明的凭证片段：

```json5
{
  // specify the identifier for the credential
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-1",
  // the credential types, which declare what data to expect in the credential
  "type": ["SelfProclaimedCredential", "BasicProfileCredential"],
  ...
  "credentialSubject": {
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    // assertion about the subject of the credential
    "nickname": "woohah",
    "nation": "Singapore",
    "language": "English"
  },
  ...
}
```

例如一个由Elastos IDteria自助服务颁发的凭证片段：

```json5
{
  // specify the identifier for the credential
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-2",
  // the credential types, which declare what data to expect in the credential
  "type": ["ElastosIDteriaCredential", "InternetAccountCredential", "PhoneCredential"],
  ...
  "credentialSubject": {
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    "email": "woohah@example.com",
    "phone": "+16693553638",
    "googleAccount": "woohah@gmail.com",
    "twitter": "@woohah"
  },
  ...
}
```

#### 颁发人/issuer

`issuer`属性用来描述这个凭证的颁发者，值是颁发者的DID。该属性为可选，如果凭证是DID持有者自主声明的，那么可以省略该属性，也可以是自己的DID，表示是自我声明的；如果凭证是第三方颁发，那么需要具有该属性，并且属性值是颁发者的DID。

#### 颁发日期/issuanceDate

`issuanceDate`用于表示颁发凭证的日期和时间。凭证必须具有颁发日期属性。属性的值必须是[RFC3339](https://tools.ietf.org/html/rfc3339)组合日期和时间字符串的字符串值，并标准化为UTC时间，尾随"Z"。此日期同时也表示与`credentialSubject`属性关联信息生效的最早日期。

#### 过期日期/expirationDate(可选)

`expirationDate`表示凭证不再有效的日期和时间。凭证可以具有该属性。属性的值必须是[RFC3339](https://tools.ietf.org/html/rfc3339)组合日期和时间字符串的字符串值，并标准化为UTC时间，尾随"Z"。如果没有该属性，意味着这个凭证的有效期和对应DID的有效期一致。

#### 凭证主题/credentialSubject

一个可验证凭证必须具有一个凭证主题，其中可以包含一个或者多个属性，用来表述该凭证的具体信息。其中必须包含一个`id`属性，表述该凭证对应的DID。其它属性由持有者或者应用来决定。

为了便于亦来云生态应用一致的理解凭证信息，附了一个建议属性别表：

| 属性名称          | 说明                                                        |
| ----------------- | ----------------------------------------------------------- |
| name              | 名称。                                                      |
| givenName         | 名字。可以和familyName一起使用替代name。                    |
| familyName        | 姓氏。                                                      |
| additionalName    | 中间名。                                                    |
| nickname          | 昵称，绰号。                                                |
| gender            | 性别。建议值：female，male，n/a。                           |
| birthDate         | 出生日期。                                                  |
| birthPlace        | 出生地。                                                    |
| image             | 头像。建议值是一个指向图片的URL，或者Base64编码的内嵌图片。 |
| description       | 描述说明。                                                  |
| address           | 地址。                                                      |
| email             | 邮件地址。                                                  |
| telephone         | 电话号码。                                                  |
| faxNumber         | 传真号码。                                                  |
| city              | 居住城市。                                                  |
| nation            | 国家。                                                      |
| language          | 首选语言。                                                  |
| url               | 实体的Web主页。                                             |
| im                | 即时消息的地址。                                            |
| googleAccount     | Google账号。                                                |
| MicrosoftPassport | 微软账号。                                                  |
| facebook          | 脸书账号。                                                  |
| twitter           | Twitter账号。                                               |
| telegram          | Telegram账号。                                              |
| weibo             | 微博账号。                                                  |
| qq                | QQ账号。                                                    |
| wechat            | 微信账号。                                                  |
| taobao            | 淘宝账号。                                                  |
| alipay            | 支付宝账号。                                                |

> 上述属性定义参照[schema.org](https://schema.org/Person)定义，不能涵盖的属性，建议应用遵从[schema.org](https://schema.org/Person)进行扩展。

#### 证据/proof

`proof`是针对可验证凭证提供的必要的验证方法以及相关证据信息。本规范仅使用[W3C可验证声明规范](https://www.w3.org/TR/verifiable-claims-data-model/#proofs-signatures)中外部证据和嵌入证据两种方式中的嵌入证据方式。凭证必须包含证据属性。证据需要包含以下属性：

- `type`默认是`ECDSAsecp256r1`，可以省略。
- `verificationMethod`表示证明方法，值是颁发者DID文档中用于签名和验证的公钥引用。
- `signature`表示签名的值，使用Base64URL编码。

### 可验证表达/Verifiable Presentations

隐私设计是DID和可验证声明的重要目标，所以实体能够在不同的场景中针对验证方的需求和自身的角色而提供一个最小的、必须的个人凭证信息是很重要的。可验证表达是指包含实体可验证凭证子集及副署签名(countersign)的数据集合，用于对第三方表明自身身份。

一般而言，可验证表达中包含的是针对一个DID身份的凭证，这些凭证可以是不同的第三方实体颁发，用来表达实体的身份信息。

可验证表达可以包含的属性定义如下。

#### 类型/type

`type`表示目标数据的类型是一个可验证表达，值为`VerifiablePresentation`。

#### 创建时间/created(可选)

`created`是该可验证表达创建的时间，属性的值必须是[RFC3339](https://tools.ietf.org/html/rfc3339)组合日期和时间字符串的字符串值，并标准化为UTC时间，尾随"Z"。

#### 可验证凭证/verifiableCredential

`verifiableCredential`是一个可验证凭证的数组，可以根据应用场景或者角色需求由持有者提供的一个凭证的子集。

#### 证明/proof

`proof`是由持有者提供的针对该可验证表达必要的验证方法以及相关证据信息。证明的规则如下：

- `type`默认是`ECDSAsecp256r1`，可以省略。
- `verificationMethod`表示证明方法，值是提供者DID文档中用于签名和验证的公钥引用。
- `nonce`表示签名操作使用的随机值。
- `realm`表示该表达适用的目标领域，如网站域名，应用名等等。
- `signature`表示签名的值，使用Base64URL编码。

### 示例

#### 自主声明的可验证凭证

```json5
{
  // specify the identifier for the credential
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-1",
  // the credential types, which declare what data to expect in the credential
  "type": ["SelfProclaimedCredential", "BasicProfileCredential"],
  // when the credential was issued
  "issuanceDate": "2019-01-01T19:20:18Z",
  "credentialSubject": {
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    // assertion about the subject of the credential
    "nickname": "woohah",
    "nation": "Singapore",
    "language": "English"
  },
  "proof": {
    // the cryptographic signature suite that was used to generate the signature
    "type": "ECDSAsecp256r1",
    // the public key identifier that created the signature,
    // DID subject can be omitted, keep the fragment only
    "verificationMethod": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key",
    "signature": "pYw8XNi1..Cky6Ed="
  }
}
```

#### 第三方颁发的可验证凭证

```json5
{
  // specify the identifier for the credential
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-2",
  // the credential types, which declare what data to expect in the credential
  "type": ["ElastosIDteriaCredential", "InternetAccountCredential", ...],
  // the entity that issued the credential
  "issuer": "did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw",
  // when the credential was issued
  "issuanceDate": "2019-01-01T19:20:18Z",
  // when the credential will expire
  "expirationDate": "2020-01-01T19:23:24Z",
  // claims about the subject of the credential
  "credentialSubject": {
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    "email": "woohah@example.com",
    "phone": "+16693553638",
    "googleAccount": "woohah@gmail.com",
    "twitter": "@woohah"
  },
  // digital proof that makes the credential tamper-evident
  "proof": {
    // the cryptographic signature suite that was used to generate the signature
    "type": "ECDSAsecp256r1",
    // the public key identifier that created the signature,
    // DID subject can be omitted, keep the fragment only
    "verificationMethod": "did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw#sign-key",
    // the digital signature value
    "signature": "BavEll0a...W3JT24="
  }
}
```

#### 可验证表达

```json5
{
  "type": "VerifiablePresentation",
  "created": "2019-01-10T21:19:10Z",

  // the verifiable credential issued in the previous example
  "verifiableCredential": [{
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-2",
    "type": ["ElastosIDteriaCredential", "InternetAccountCredential", ...],
    "issuer": "did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw",
    "issuanceDate": "2019-01-01T19:20:18Z",
    "expirationDate": "2020-01-01T19:23:24Z",
    "credentialSubject": {
      "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
      "email": "woohah@example.com",
      "phone": "+16693553638",
      "googleAccount": "woohah@gmail.com",
      "twitter": "@woohah"
    },
    "proof": {
      "type": "ECDSAsecp256r1",
      // Omitted the DID subject, default is issuer's DID.
      // Equals: did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw#sign-key
      "verificationMethod": "#sign-key",
      "signature": "BavEll0...W3JT24="
    }
  }],

  "proof": {
    "type": "ECDSAsecp256r1",
    // DID subject can be omitted.
    "verificationMethod": "did:example:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#keys-1",
    // 'nonce' and 'realm' protect against replay attacks
    "nonce": "6c573d7abe8720b5a659671335788a6f",
    "realm": "example.com",
    "signature": "eyJhbGci...AnKb78="
  }
}
```

> REF: https://w3c.github.io/vc-imp-guide/

## 流程和生命周期

```sequence
Holder->DIDClientApp: 发起凭证申请
DIDClientApp->Issuer: 请求凭证
Issuer-->DIDClientApp: 凭证申请表单
DIDClientApp-->Holder: 将表单呈现给Holder
Holder->DIDClientApp: 填写表单
DIDClientApp->Issuer: 提交表单
Issuer->Issuer: 审核验证表单
Issuer->DIDClientApp: 颁发可验证凭证
note over DIDClientApp: 本地存储可验证凭证
Holder->Service/Verifier: 登录
Service/Verifier->DIDClientApp: 凭证要求\n(selective disclosure request)
DIDClientApp->Holder: 确认请求
Holder->DIDClientApp: 核准确认
DIDClientApp->DIDClientApp: 签署身份表达
DIDClientApp->Service/Verifier: 可验证表达
Service/Verifier->DLT: 获取可验证表达中相关DID
DLT->Service/Verifier: DID文档
Service/Verifier->Service/Verifier: 验证Hodler提供的可验证表达
note over Service/Verifier: 完成登录
```

该流程和生命周期的前提是Holder和Issuer已经注册DID并登记到DLT。

## 其它

本规范未尽事项请参考[W3C可验证声明规范](https://www.w3.org/TR/verifiable-claims-data-model/)的定义。
