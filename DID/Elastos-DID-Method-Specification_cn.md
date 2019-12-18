# 亦来云DID方法规范 v0.2

区块链驱动的智能万维网



----

亦来云基金会

2019年11月30日



**版本**

0.2



**内容说明**

此文档是亦来云DID方法技术规范，由亦来云基金会发布并维护。主要说明了亦来云DID的通用数据模型和格式，以及相关的操作。未来我们会持续升级此文档，以体现亦来云DID技术最新发展状态。



**版权声明**
此文档著作权归亦来云基金会所有，保留所有权利。

----



[TOC]

----

## 概述

当下互联网上身份标示系统都是中心化系统，大都是由互联网服务巨头提供，在这种情况下，我们不得不选择信任这些互联网服务商（无论是公司、机构还是其他组织），他们收集我们的数据，于此同时也有保护数据安全的义务。看似本身没有什么问题，但数据安全的问题时有发生，用户身份被泄露的情况非常严重，个人对自己的身份信息没有任何控制力，甚至服务商可以在用户完全不知情的状况下恶意泄露个人身份信息。

随着区块链技术的发展，技术社区提出了基于区块链技术实现的去中心ID系统，数字身份不再由中心化的服务商提供，而是记录在去中心的区块链上。同时数字身份完全由用户控制，不再依赖于中心化机构对数据的控制，而且DID是可验证的，可以同时用于身份验证和数据加密等多种场景。

DID完全由DID主体控制，不依赖于任何集中式注册机构、身份提供商或证书颁发机构。DID通过DID文档来描述，每个DID文档至少包含两个组件：加密材料、身份验证套件。与身份验证套件相结合的加密材料提供了一组身份验证机制(如公钥、匿名生物识别协议等)，其它的可选部分可以根据应用和用户的需求来使用。



### 本规范的目的

制定本规范有两个目的，首先是定义一个亦来云体系内标准的DID文档描述格式和基本DID属性集，使得亦来云生态上的应用可以理解标准集中定义的基本数字身份信息，从而实现数字身份在亦来云生态体系内的标准化；其次是定义一组通用的操作方法，这些操作可以为任何dApp使用，实现对亦来云DID服务的引用。



### 设计目标

本规范的设计是基于[W3C DID规范讨论稿](https://w3c-ccg.github.io/did-spec/)，同时参考了[DIF](https://identity.foundation/)相关的文档和代码，采用了W3C DID的一个子集，符合W3C DID规范要求。

简洁性和易用性也是本设计的一个重要考量因素，希望DID文档能够在人类可读性、结构化、便于处理和使用等方面达到一个合理的平衡。DID相关操作方法有良好的易用性，降低dApp中使用DID的工程成本。

在[W3C DID规范讨论稿](https://w3c-ccg.github.io/did-spec/)中的一些术语本文不再重复说明，可参阅原文。



## DID方法

亦来云DID method固定为`elastos`。在亦来云应用体系内可以省略method，直接使用did:specific-idstring，应用默认识别为elastos的method，即有效的亦来云DID。



## 目标系统

亦来云DID的DLT后端采用亦来云ID侧链，并且适用于亦来云所有系统和dApps。

DID相关的操作行为由DID主题持有者通过DID客户端发起交易来实现，DID相关的操作和DID文档都保存在交易的payload中。



## DID描述符

亦来云DID采用[W3C DID规范讨论稿](https://w3c-ccg.github.io/did-spec/)中的定义的描述格式，是符合[RFC3986](https://tools.ietf.org/html/rfc3986)标准的URI，它由DID后跟可选路径和或片段组成。

下面是使用[RFC5234](https://tools.ietf.org/html/rfc5234)语法的亦来云DID描述符的ABNF定义：

```makefile
did                = "did:" method ":" specific-idstring ;
method             = "elastos"
specific-idstring  = idstring *( ":" idstring ) ;
idstring           = BASE58 ;

did-url            = did [ "/" did-path ] [ "#" did-fragment ] ;
did-path           = path-rootless ;
path-rootless      = segment-nz *( "/" segment ) ;
segment            = *pchar ;
segment-nz         = 1*pchar ;
did-fragment       = fragment ;
fragment           = *( pchar / "/" / "?" ) ;
pchar              = unreserved / pct-encoded / sub-delims / ":" / "@" ;

unreserved         = ALPHA / DIGIT / "-" / "." / "_" / "~" ;
pct-encoded        = "%" HEXDIG HEXDIG
sub-delims         = "!" / "$" / "&" / "'" / "(" / ")"
                     / "*" / "+" / "," / ";" / "=" ;

    ALPHA          =  %x41-5A / %x61-7A ;
    DIGIT          =  %x30-39 ;
    HEXDIG         = DIGIT / "A" / "B" / "C" / "D" / "E" / "F" ;
    BASE58         =  Bitcoin style base58 encoded string ;
```



## DID字符串

亦来云DID中的ID字符串是使用Bitcoin风格Base58编码的ID侧链地址，并且以字母`i`开始，如`icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN`，ID字符串区分大小写。




## DID文档

### DID文档格式

DID文档**必须**是符合[RFC8259](https://tools.ietf.org/html/rfc8259)的单个JSON对象，同时在ID侧链上存储的DID文档数据也是基于JSON格式的。亦来云DID文档不采用W3C DID规范讨论稿指定的[JSON-LD](https://www.w3.org/TR/json-ld/)格式，仅使用简单的JSON，这样的设计是为了保持DID文档的简洁和应用开发的方便。相关的属性、命名采用和W3C DID规范一致的定义；同时为了和W3C DID标准互操作，亦来云DID将提供到W3C DID文档的映射机制。



### DID文档属性

#### 主题

主题是DID文档的唯一标识符，用来表示对应的DID文档。主题的规则如下：

- DID文档必须有一个且只有一个DID主题。
- 主题的key必须是`id`。
- 主题的值必须是一个有效的DID描述符。
- 当该DID文档注册到ID侧链上时，该DID描述符要和持有者的公钥相对应。

例如：

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN"
}
```



#### 公钥/Public Keys

公钥用于数字签名、加密等操作，主要的目的是实现身份认证，或与服务端点建立安全通信。 此外，公钥也可用于DID授权和委托，并在DID CRUD操作中用来验证操作的合法性。

亦来云DID文档中至少需要包含一个公钥，即DID描述符对应的公钥。

公钥的规则是：

- DID文档必须包含一个`publicKey`属性。
-  `publicKey`属性的值必须是公钥数组。

- 每个公钥必须包含`id`和`type`属性。 公钥数组不应该包含具有相同`id`和具有不同格式的不同值属性的重复条目。
- `id`属性值由该DID标识符和一个自定义的URI片段构成，如`did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key`，出于保持数据紧凑的目的，可以省略前面的DID标识符，而仅使用URI片段部分，如`#master-key`。
- 亦来云公钥的`type`默认是`ECDSAsecp256r1`，可以省略。
- 每个公钥可以包含一个`controller`属性，用于表示相应私钥控制者的DID；默认为所在文档的DID，这种情况可省略该属性。
- 每个公钥必须包含一个`publicKeyBase58` 属性，用来存放Base58编码的公钥。

例如：

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  "publicKey": [{ // 和DID地址对应的公钥
    "id": "#master-key",
    "type": "ECDSAsecp256r1",
    "controller": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    "publicKeyBase58": "zNxoZaZLdackZQNMas7sCkPRHZsJ3BtdjEvM2y5gNvKJ"
  }, { // 本DID所有者持有的第二个公钥，此处省略了type和controller
    "id": "#key-2",
    "publicKeyBase58": "273j8fQ1ZZVM6U6d5XE3X8SyULuJwjyYXbxNopXVuftBe"
  }, { // 用于恢复的公钥，本DID所有者的可信的第三方持有
    "id": "#recovery-key",
    "type": "ECDSAsecp256r1",
    "controller": "did:elastos:ip7ntDo2metGnU8wGP4FnyKCUdbHm4BPDh",
    "publicKeyBase58": "zppy33i2r3uC1LT3RFcLqJJPFpYuZPDuKMeKZ5TdAskM"
  }],
  ...
}
```



#### 认证/Authentication (可选)

认证是实体可以通过加密方式证明他们与DID相关联的机制。 认证属性可以指定哪些DID的公钥可以被用来进行身份认证。基于ID侧链的技术实现特征，DID主题对应的公钥在默认的情况下具备身份认证功能，即使没有显式的声明。

身份验证的规则是：

- DID文档可以包含最多一个`authentication`属性。

- `authentication`属性的值是一个验证方法数组，即可用于身份验证的公钥数组。

- 可以嵌入或引用验证方法。 在引用时可以是一个完整的公钥URI，或者仅仅是片段部分，如：`did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key`和`#master-key`相同，都是同一个公钥的引用。如果使用嵌入的公钥，公钥的书写规则和[公钥属性](#公钥/Public Keys)一致。

例如：

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "authentication": [
    // 可以用于认证的公钥引用
    "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-keys",
    // 可以用于认证的公钥引用
    "#key-2",
    // *仅仅用于* 认证的公钥，采用内嵌的形式书写在authentication中，
    // 这个公钥不能被用于其他的目的
    {
      "id": "#keys-3",
      "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  ],
  ...
}
```



#### 授权和委托/Authorization and Delegation(可选)

授权是用于说明如何代表DID主题执行操作的机制。 委托是指DID主题可以授权他人代表他们行事的机制。 这对于DID密钥丢失情况下的DID安全善后事宜尤其重要，当主体不再能够访问其密钥或密钥泄露时，DID所有者授权的可信第三方可以声明停用该DID，从而禁止在密钥泄露的情况下导致的一些恶意行为。

亦来云DID的授权和委托仅支持必要的最小授权，即授权可信第三方用于停用目标DID，不支持其它操作。

授权和委托的规则是：

- DID文档可以包含最多一个`authorization`属性。
- `authorization`属性的值应该是一个验证方法数组，即可用于委托的公钥数组。
- 可以嵌入或引用每种验证方法。 在引用时可以是一个完整的公钥URI，或者仅仅是片段部分，如：`did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#recovery-key`和`#recovery-key`相同，都是同一个公钥的引用。如果使用嵌入的公钥，公钥的书写规则和[公钥属性](#公钥/Public Keys)一致。

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "authorization": [
    // 委托的目标公钥引用，也可以简写为 "#recovery-key"
    "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#recovery-key",
    // *仅仅用于* 委托的公钥，采用内嵌的形式书写在authorization中，
    // 这个公钥不能被用于其他的目的
    {
      "id": "#keys-3",
      "controller": "did:elastos:iZUeeATkmBwLuEx4B41P6cvvFQYuzSQJxf",
      "publicKeyBase58": "cheFmhCj9DcSvjYEFeCQQFrrVJnG8sg3xvXRPeP1xRxi"
    }
  ],
  ...
}
```



#### 可验证凭证/verifiableCredential(可选)

按照DID设计的初衷，原则上DID文档不应该携带任何个人信息/PII（Personally-Identifiable Information），或者携带可以关联到具体实体的关键信息。但是从现实世界的需求看，有些DID实体具有公开身份的需求，比如作为well-known的Crendential Issuers等，所以亦来云DID文档可以内嵌公开的可验证凭证，来支持有DID公开绑定实体信息的需求。可验证凭证详细定义可以参[亦来云可验证声明规范](Elastos Verifiable Claims Specification.md)。

可验证凭证的规则是：

- DID文档可以包含最多一个`verifiableCredential`属性。
- `verifiableCredential`属性的值应该是一个可验证凭证(Verifiable Credential)的数组。
- 在可验证凭证中可以省略`credentialSubject`中的`id`属性，默认是当前DID主题；如果是完全自我声明的凭证，那么`verifiableCredential`的`proof`属性也可以省略，因为该声明是在用户私钥控制下更新的。

例如：

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "verifiableCredential": [{
    // specify the identifier for the credential, in long format
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-1",
    // the credential types, which declare what data to expect in the credential
    "type": ["SelfProclaimedCredential", "BasicProfileCredential"],
    // when the credential was issued
    "issuanceDate": "2019-01-01T19:20:18Z",
    "credentialSubject": {
      // assertion about the subject of the credential
      "nickname": "woohah",
      "nation": "Singapore",
      "language": "English"
    },
    // this proof can be omitted, because it's a self proclaimed credential
    "proof": {
      // the cryptographic signature suite that was used to generate the signature
      "type": "ECDSAsecp256r1",
      // the public key identifier that created the signature
      "verificationMethod": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key",
      "signatureValue": "pYw8XNi1..Cky6Ed="
    }
  }, {
    // specify the identifier for the credential, in short format
    "id": "#crdential-2",
    // the credential types, which declare what data to expect in the credential
    "type": ["ElastosIDteriaCredential", "InternetAccountCredential"],
    // the entity that issued the credential
    "issuer": "did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw",
    // when the credential was issued
    "issuanceDate": "2019-01-01T19:20:18Z",
    // when the credential will expire
    "expirationDate": "2020-01-01T19:23:24Z",
    // claims about the subject of the credential
    "credentialSubject": {
      "email": "woohah@example.com",
      "phone": "+16693553638",
      "googleAccount": "woohah@gmail.com",
      "twitter": "@woohah"
    },
    // digital proof that makes the credential tamper-evident
    "proof": {
      // the cryptographic signature suite that was used to generate the signature
      "type": "ECDSAsecp256r1",
      // the public key identifier that created the signature
      "verificationMethod": "did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw#sign-key",
      // the digital signature value
      "signature": "BavEll0...W3JT24="
    }
  }],
  ...
}
```



#### 服务端点/Service Endpoints(可选)

除了发布身份验证和授权机制之外，DID文档的另一个主要目的是发布DID主题对应的服务端点 。服务端点可以表示主体希望通告的任何类型的服务，包括用于进一步发现，认证，授权或交互的分散身份管理服务。 服务端点的规则是：

- DID文档可以包含最多一个`service`属性。

- `service`属性的值应该是服务端点的数组。

- 每个服务端点必须包含`id` ， `type`和`serviceEndpoint`属性，并且可以包含其他应用自定义的属性。

- 服务端点协议应该以开放标准规范发布。

- `serviceEndpoint`属性的值必须是符合[RFC3986](https://tools.ietf.org/html/rfc3986)的有效URI，并根据[RFC3986第6节](https://tools.ietf.org/html/rfc3986#section-6)中的规则进行规范化。

例如：

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "service": [{
    "id": "#openid",
    "type": "OpenIdConnectVersion1.0Service",
    "serviceEndpoint": "https://openid.example.com/"
  }, {
    "id": "#vcr",
    "type": "CredentialRepositoryService",
    "serviceEndpoint": "https://did.elastos.org/credential"
  }, {
    "id": "#carrier",
    "type": "CarrierAddress",
    "serviceEndpoint": "carrier://X2tDd1ZTErwnHNot8pTdhp7C7Y9FxMPGD8ppiasUT4UsHH2BpF1d"
  }],
  ...
}
```



#### 创建/Created(无)

亦来云DID文档中不包含创建属性，因为采用ID侧链交易进行记录，所以天然具时间戳，无需文档中显式的声明创建时间。亦来云的DID解析器在从链上获取DID文档时，会自动补充该属性。



#### 更新/Updated(无)

亦来云DID文档中不包含更新属性，因为采用ID侧链交易进行记录，所以天然具时间戳，无需文档中显示的声明更新时间。亦来云的DID解析器在从链上获取DID文档时，会自动补充该属性。



#### 证据/Proof 
DID文档的Proof属性是用来对DID文档的完整性提供加密证明的信息。为了保证DID文档的可信以及完整性，Proof中需要包含针对改DID文档的签名，并且签名必须使用DID主题对应的密钥生成，从而保证文档的完整性，以及防止中间人攻击。Proof的规则是：

- DID文档必须包含最多一个`proof`。
- `type`默认是`ECDSAsecp256r1`，可以省略。
- `created`表示签名创建时间，可以省略。
- `creator`表示验证签名的密钥引用，值是必须是DID主题对应密钥的引用，可以省略。
- `signatureValue`表示签名的值，使用Base64URL编码。

例如：

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "proof": {
    "type": "ECDSAsecp256r1",
    "creator": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key",
    "signatureValue": "QNB13Y7Q9...1tzjn4w"
  }
}
```



#### 过期/Expires

出于安全的考虑，亦来云DID主题具有有效期，最长有效期可以设定为5年，用户也可以根据自身需求设定为更短的有效期。超出有效期的DID主题则会被DID解析器识别为无效DID主题。 过期的规则是：

- DID文档必须包含一个`expires`属性。
- 此键的值必须是有效的[RFC3339](https://tools.ietf.org/html/rfc3339)组合日期和时间的字符串值。
- 该日期时间值必须标准化为UTC时间，尾随“Z”。

例如：

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "expires": "2024-02-10T17:00:00Z"
}
```



### 可扩展性

应用可以在遵循上述文档定义的标准属性之外，根据应用的需要对DID文档进行扩展。扩展的部分应该遵循上述文档格式的约定，在增加应用自定义属性时，建议使用JSON-LD声明扩展属性的context，以便于亦来云DID客户端将亦来云DID文档规范化为W3C标准兼容的DID文档时，正确的处理应用扩展属性；否则，应用的扩展属性将被忽略，不被包含在规范化的DID文档结果中。

> 关于扩展属性的处理以及JSON-LD的context的约束，目前在没有实际应用的情况下，上述的规则只是一个设想，后续可以根据应用的需求以及DID文档的规范化处理情况再进行调整。



## DID操作

亦来云DID文档存储是基于ID侧链的，所以相关的操作都需要在支持ID侧链的客户端中操作，并且需要使用对应DID主题的公钥给自己发起交易来实现。但是停用DID支持一个例外，允许DID主题授权和委托的DID公钥发起停用操作。

DID操作和对应的文档采用JSON格式保存在交易的payload中，DID操作JSON文档的属性定义如下：

- 必须包含一个`header`属性，该属性包含DID操作的基本信息。`header`的属性定义如下：
  - 必须包含一个`specification`属性，表示DID操作所符合的规范和版本，当前只支持`elastos/did/1.0`。
  - 必须包含一个`operation`属性，用来说明执行何种操作，属性值参见具体操作。
  - 根据操作需要，可以包含一个`previousTxid`属性。属性定义参见具体操作。
- 必须包含一个`payload`属性，属性值是操作的目标DID文档或者DID主题。
- 必须包含一个`proof`属性，该属性包含DID持有者操作DID的公钥和签名，用于证明是持有者本人或者委推人的操作。`proof`属性定义如下：
  - `type`默认是`ECDSAsecp256r1`，可以省略。
  - `verificationMethod`表示证明方法，值是颁发者DID文档中用于签名和验证的公钥引用。
  - `signature`表示签名的值，使用Base64URL编码。

因为区块链是经济驱动的，交易需要手续费，并且payload也需要根据大小额外收费，所以从经济性的角度考虑，DID文档的要尽量紧凑，控制上链的大小，所以一般而言，上链的DID文档可以省略默认的属性，如公钥的`type`，和当前DID主题一致的`controller`，文档内属性引用的URI尽量减写等等，可以有效降低DID操作的费用。



### 创建/Create DID

创建DID需要DID客户端根据持有人拥有的密钥对，在本地生成DID文档，并填充相关属性，默认包含该公钥，并根据该公钥生成DID主题，以及通过该公钥进行认证，其它的属性由应用或者用户根据需求填写。

创建DID的规则是：

- 必须包含一个`header`属性，其中`operation`属性值是`create`，表示新创建DID。
- 必须包含`payload`属性，值是目标DID文档采用Base64URL模式编码的文档。
- 必须包含一个`proof`属性，包含DID所有者的公钥引用和签名，用于证明该操作是DID持有者发起。

例如：

```json
{
  "header": {
    "specification": "elastos/did/1.0",
    "operation": "create"
  },
  "payload": "ICAiZG9jIjogewogICAgImlkIjogImRpZDplbGFzdG9zOmljSjR6MkRVTHJIRXpZU3ZqS05KcEt5aHFGRHh2WVY3cE4iLAogICAgInB1YmxpY0tleSI6IFt7CiAgICAgICJpZCI6ICIjbWFzdGVyLWtleSIsCiAgICAgICJwdWJsaWNLZXlCYXNlNTgiOiAiek54b1phWkxkYWNrWlFOTWFzN3NDa1BSSFpzSjNCdGRqRXZNMnk1Z052S0oiCiAgICB9LCB7CiAgICAgICJpZCI6ICIja2V5LTIiLAogICAgICAicHVibGljS2V5QmFzZTU4IjogIjI3M2o4ZlExWlpWTTZVNmQ1WEUzWDhTeVVMdUp3anlZWGJ4Tm9wWFZ1ZnRCZSIKICAgIH0sIHsKICAgICAgImlkIjogIiNyZWNvdmVyeS1rZXkiLAogICAgICAiY29udHJvbGxlciI6ICJkaWQ6ZWxhc3RvczppcDdudERvMm1ldEduVTh3R1A0Rm55S0NVZGJIbTRCUERoIiwKICAgICAgInB1YmxpY0tleUJhc2U1OCI6ICJ6cHB5MzNpMnIzdUMxTFQzUkZjTHFKSlBGcFl1WlBEdUtNZUtaNVRkQXNrTSIKICAgIH1dLAogICAgImF1dGhlbnRpY2F0aW9uIjogWwogICAgICAibWFzdGVyLWtleXMiLAogICAgICAiI2tleS0yIiwKICAgIF0sCiAgICAuLi4KICB9LA",
  "proof": {
    "type": "ECDSAsecp256r1",
    "verificationMethod": "#master-key",
    "signature": "JCAlfEBh...I3NSwg="
  }
}
```

其中payload是以下DID文档通过Base64URL编码后的结果：

```json
{
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    "publicKey": [{
      "id": "#master-key",
      "publicKeyBase58": "zNxoZaZLdackZQNMas7sCkPRHZsJ3BtdjEvM2y5gNvKJ"
    }, {
      "id": "#key-2",
      "publicKeyBase58": "273j8fQ1ZZVM6U6d5XE3X8SyULuJwjyYXbxNopXVuftBe"
    }, {
      "id": "#recovery-key",
      "controller": "did:elastos:ip7ntDo2metGnU8wGP4FnyKCUdbHm4BPDh",
      "publicKeyBase58": "zppy33i2r3uC1LT3RFcLqJJPFpYuZPDuKMeKZ5TdAskM"
    }],
    "authentication": [
      "master-keys",
      "#key-2",
    ],
    ...
}
```



### 更新/Update DID

只有DID主题的持有者有更新DID的权力。非DID主题持有者发起的更新DID交易都会被DID解析器过滤并丢弃，不对目标DID产生任何影响。更新DID时不支持增量更新，需要在更新交易中附加完整的DID文档。

更新DID的规则是：

- 必须包含一个`header`属性，其中 `operation`属性值是`update`，表示更新DID。
- `header`需要包含`previousTxid` 属性，值是前一个DID文档操作的交易ID。用来避免不必要的错误更新操作。
- 必须包含`payload`属性，值是新的DID文档。
- 必须包含一个`proof`属性，包含DID所有者的公钥引用和签名，用于证明该操作是DID持有者发起。

例如：

```json
{
  "header": {
    "specification": "elastos/did/1.0",
    "operation": "update",
    "previousTxid": "3641de55f368583c...8917756a872093d2"
  },
  "payload": "ICAiZG9jIjogewogICAgImlkIjogImRpZDplbGFzdG9zOmljSjR6MkRVTHJIRXpZU3ZqS05KcEt5aHFGRHh2WVY3cE4iLAogICAgInB1YmxpY0tleSI6IFt7CiAgICAgICJpZCI6ICIjbWFzdGVyLWtleSIsCiAgICAgICJwdWJsaWNLZXlCYXNlNTgiOiAiek54b1phWkxkYWNrWlFOTWFzN3NDa1BSSFpzSjNCdGRqRXZNMnk1Z052S0oiCiAgICB9LCB7CiAgICAgICJpZCI6ICIja2V5LTIiLAogICAgICAicHVibGljS2V5QmFzZTU4IjogIjI3M2o4ZlExWlpWTTZVNmQ1WEUzWDhTeVVMdUp3anlZWGJ4Tm9wWFZ1ZnRCZSIKICAgIH0sIHsKICAgICAgImlkIjogIiNyZWNvdmVyeS1rZXkiLAogICAgICAiY29udHJvbGxlciI6ICJkaWQ6ZWxhc3RvczppcDdudERvMm1ldEduVTh3R1A0Rm55S0NVZGJIbTRCUERoIiwKICAgICAgInB1YmxpY0tleUJhc2U1OCI6ICJ6cHB5MzNpMnIzdUMxTFQzUkZjTHFKSlBGcFl1WlBEdUtNZUtaNVRkQXNrTSIKICAgIH1dLAogICAgImF1dGhlbnRpY2F0aW9uIjogWwogICAgICAibWFzdGVyLWtleXMiLAogICAgICAiI2tleS0yIiwKICAgIF0sCiAgICAuLi4KICB9LA",
  "proof": {
    "verificationMethod": "#master-key",
    "signature": "ZGhscJxw...tCAgQA="
  }
}
```



### 停用/Deactivate DID

DID持有者可以主动停用DID，比如DID持有者不再使用该DID，或者认为DID对应私钥泄漏，都可以选择停用DID，因为这时DID所有者还持有私钥。

如果DID持有者的私钥遗失，那么他将失去该DID的控制权，无法直接对DID进行任何更新操作，包括停用。若DID文档中包含了授权和委托，那么委托的可信第三方可以向该DID对应的地址发起停用交易，用来代替DID持有人停用DID，从而避免DID被恶意使用。

停用DID的规则是：

- 必须包含一个`header`属性，其中`operation`属性值是`deactivate`，表示停用DID。
- 必须包含`payload`属性，值是目标DID主题。
- 必须包含一个`proof`属性，包含DID所有者或者委托人的公钥引用和签名，用于证明该操作是DID持有者或者委托人发起。

例如：

```json
{
  "header": {
    "specification": "elastos/did/1.0",
    "operation": "deactivate"
  },
  "payload": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  "proof": {
    "verificationMethod": "#recovery-key",
    "signature": "dkODxARE...hMUFRY="
  }
}
```



### 读取/验证DID

DID文档存储在ID侧链上，任何ID侧链节点可以读取任意DID主题对应的文档，只需读取DID对应的交易即可。其中需要验证有效的DID交易，有效交易包含：DID持有人发起的对自己的交易，以及DID文档中授权委托的DID持有人使用委托的公钥发起的停用DID交易。

在实际的使用中，一般DID读取和验证是由DID解析器来实现的。



## DID解析器

DID解析器是是对DID进行读取和验证的功能组件，其API设计用于接受DID查找请求并执行相应的DID方法以检索权威DID文档。 为了符合W3C规范，亦来云DID解析器的处理过程如下：

1.  从链上读取和DID主题关联的所有交易，并按照历史顺序依次处理；
2.  处理过程中将非法交易（可以理解为未授权交易）剔除；
3.  然后生成最终的DID文档；
4.  根据DID方法规范验证DID是否有效，无效则产生错误；
5.  根据请求返回DID文档的相应属性或者服务。

目前的解析器设计依赖于SPV客户端，是一个完全去中心的设计，具有良好的安全性，但同时在DID解析时可能会相对比较耗时。应用开发者可以根据需求，针对自己的应用提供可信的链外DID解析服务，该服务的设计要充分考虑安全设计，避免重放，消息插入，删除，修改，模拟和中间人之类的攻击。



## 安全考虑

亦来云DID以ID侧链作为DID的去中心存储系统，ID侧链通过亦来云主链与比特币联合挖矿获得的哈希算力作为安全保障，并在此基础上充分考虑DID的自我主权需求。

DID的操作是基于区块链上的交易来完成，以交易的安全来支持DID操作的安全；同时严格限定了DID操作主体，保证DID不会被重放，消息插入，删除，修改，模拟和中间人之类的攻击，从而保证DID的安全。

在上述安全策略的基础上，通过授权和委托机制，实现了DID在持有人将密钥丢失后的异常情况下的DID停用机制，最大程度的保证了DID不被恶意使用。



## 隐私考虑

隐私和自我主权是DID设计的基础原则，亦来云DID的隐私保护策略遵循了W3C的约定。原则上DID文档不应该携带任何实体信息、或者可以关联到具体实体的关键信息。实体的个人信息凭证一般都是由实体或者个人在终端设备上管理和存储，由自己全权掌控，然后通过可验证声明的形式提供给验证方；但有些应用、场景、实体需要公开实体信息，为了便于类似需求，亦来云DID中增加了**可选**的内嵌可验证凭证属性，由DID主题的持有实体自主决定是否使用。这样的也体现了DID的自我主权精神，同时也符合[GDPR](https://eugdpr.org/)的要求。



## 示例

### 一个最简单的DID文档

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  "publicKey": [{
    "id": "#key",
    "publicKeyBase58": "zNxoZaZLdackZQNMas7sCkPRHZsJ3BtdjEvM2y5gNvKJ"
  }]
}
```

该DID文档中默认有一个公钥，即和DID主题对应的公钥；其它DID默认属性如下：

- 公钥`#key`的`type`是`ECDSAsecp256r1`。

- 公钥`#key`的`controller`是目标DID主题`did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN`持有者。

- 公钥`#key`可以用于认证DID主题持有者身份。

- 有效期为5年。



### 一个复杂/冗长的DID文档

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",

  "publicKey": [{
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#default",
    "type": "ECDSAsecp256r1",
    "controller": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    "publicKeyBase58": "zNxoZaZLdackZQNMas7sCkPRHZsJ3BtdjEvM2y5gNvKJ"
  }, {
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#key2",
    "type": "ECDSAsecp256r1",
    "controller": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    "publicKeyBase58": "273j8fQ1ZZVM6U6d5XE3X8SyULuJwjyYXbxNopXVuftBe"
  }, {
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#recovery",
    "type": "ECDSAsecp256r1",
    "controller": "did:elastos:ip7ntDo2metGnU8wGP4FnyKCUdbHm4BPDh",
    "publicKeyBase58": "zppy33i2r3uC1LT3RFcLqJJPFpYuZPDuKMeKZ5TdAskM"
  }],

  "authentication": [
    "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#default",
    "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#key2",
    {
      "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#keys3",
      "type": "ECDSAsecp256r1",
      "controller": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
      "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  ],

  "authorization": [
    "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#recovery"
  ],

  "credential": [{
    // specify the identifier for the credential
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-1",
    // the credential types, which declare what data to expect in the credential
    "type": ["SelfProclaimedCredential", "BasicProfileCredential"],
    // when the credential was issued
    "issuanceDate": "2019-01-01T19:20:18Z",
    "credentialSubject": {
      // assertion about the subject of the credential
      "nickname": "woohah",
      "nation": "Singapore",
      "language": "English"
    },
    "proof": {
      // the cryptographic signature suite that was used to generate the signature
      "type": "ECDSAsecp256r1",
      // the public key identifier that created the signature
      "verificationMethod": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key",
      "signature": "pYw8XNi1..Cky6Ed="
    }
  }, {
    // specify the identifier for the credential
    "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#crdential-2",
    // the credential types, which declare what data to expect in the credential
    "type": ["ElastosIDteriaCredential", "InternetAccountCredential"],
    // the entity that issued the credential
    "issuer": "did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw",
    // when the credential was issued
    "issuanceDate": "2019-01-01T19:20:18Z",
    // when the credential will expire
    "expirationDate": "2020-01-01T19:23:24Z",
    // claims about the subject of the credential
    "credentialSubject": {
      "email": "woohah@example.com",
      "phone": "+16693553638",
      "googleAccount": "woohah@gmail.com",
      "twitter": "@woohah"
    },
    // digital proof that makes the credential tamper-evident
    "proof": {
      // the cryptographic signature suite that was used to generate the signature
      "type": "ECDSAsecp256r1",
      // the public key identifier that created the signature
      "verificationMethod": "did:elastos:iY3PF8u1bYMujq6Z4bk2iAnmRFyD1cqbSw#sign-key",
      // the digital signature value
      "signature": "BavEll0...W3JT24="
    }
  }],

  "service": [{
    "id": "#openid",
    "type": "OpenIdConnectVersion1.0Service",
    "serviceEndpoint": "https://openid.example.com/"
  }, {
    "id": "#vcr",
    "type": "CredentialRepositoryService",
    "serviceEndpoint": "https://did.elastos.org/credential"
  }, {
    "id": "#carrier",
    "type": "CarrierAddress",
    "serviceEndpoint": "carrier://X2tDd1ZTErwnHNot8pTdhp7C7Y9FxMPGD8ppiasUT4UsHH2BpF1d"
  }],

  "expires": "2020-02-10T17:00:00Z"
}
```

