# Elastos Verifiable Claims Specification v0.2

Smart Web Powered by Blockchain

----

Elastos Foundation

30 November 2019

**Version**

0.2

**Description of Contents**

This document is the Elastos Verifiable Claims Technical Specification, which is published and maintained by the Elastos Foundation. It explains the universal data model and format of Elastos Verifiable Claims, as well as related operations. In the future, Elastos Foundation will continue to upgrade this document so that it reflects the latest state of development of the Elastos DID and related technology.

**Copyright Statement**

The copyright of this document belong to Elastos Foundation. All rights reserved.

----

[TOC]

----

## Abstract

Verifiable Claims are specifically designed for the decentralized DID, and are personal credentials which allows users to attain self-sovereignty and self-control. Within the architecture of current service-centric systems, it is very difficult for users to change their service providers without giving up their current digital identity. This locks in suppliers, weakens identities and causes user privacy breaches. But verifiable claims give users an easier, more secure way to express and exchange credential verified by a third-party.

In principle, verifiable claims are held by the corresponding DID entity, for example the verifiable claims are stored in the DID client software on its terminal device, and the client provides the necessary protective measures. From the perspective of privacy and security, the data of the verifiable claims is not recommended to be stored on the blockchain, encrypted or unencrypted. Directly storing unencrypted information on the blockchain is the equivalent of publicizing personal information to the world; even after encryption, the security of encryption algorithms is relative, and a secure approach now cannot guarantee its future security .

### Goals of this Specification

The purpose of creating this specification is to define a standard verifiable claim data model for the internal Elastos system within the framework of [W3C Verifiable Claims Specification](https://www.w3.org/TR/verifiable-claims-data-model/), thus attaining standardization of digital identities within the Elastos ecosystem, allowing any dApp to use the identity credentials associated with Elastos DID by conforming to this standard.

### Design Goals

The design of this specification is based on [W3C Verifiable Claims Specification](https://www.w3.org/TR/verifiable-claims-data-model/), with reference to relevant documents and code of [DIF](https://identity.foundation/), and with the use of a subset of the W3C Specification.

Conciseness and usability are also an important element for this design, and we hope that verifiable claims are able to reach a reasonable equilibrium in areas such as human readability, structure, and ease of processing and use.

## Data Model

### Data Format

Verifiable claims **must** be a single JSON object conforming to [RFC8259](https://tools.ietf.org/html/rfc8259). The description of Elastos Verifiable Claims does not utilize the format described in [JSON-LD](https://www.w3.org/TR/json-ld/), it only uses simple JSON. This kind of design keeps the conciseness of verifiable claims and facilitates application development. Related properties and naming remain consistent with the definitions in the W3C specification; if there is no related definition in the W3C specification, then add the definition according to the requirements of specification extension as the Elastos Verifiable Claim standard. Meanwhile, for interoperability with the W3C verifiable claims standard, Elastos Verifiable Claims will provide a mapping mechanism to W3C Verifiable Claims.

### Verifiable Credentials

Credentials are a set of one or more claims made by an entity. Credentials should contain identifiers and metadata used to describe the credential's properties, such as issuer, expiration date and time, the public key used for verification purposes, and revocation mechanisms. Metadata and credential subject are signed by the issuer. Verifiable credentials are a set of anti-tampering claims and metadata, and uses cryptographic methods to prove whether the entity was issued as stated in the claim and has not been tampered.

The definition of properties that can be contained in verifiable credentials are as follows. 

#### ID

`id` is used to mark specific credentials, applications or users can also use ID to reference specified credentials. `id` value **must** be a single URI that conforms to [RFC3986](https://tools.ietf.org/html/rfc3986). The credential must contain an `id` property.

#### Type

According to [the definition in the W3C Verifiable Claims Specification](https://www.w3.org/TR/verifiable-claims-data-model/#types), `type` is the name used to differentiate credential information types defined by specific implementation software systems. Credentials must contain a `type` property.

The value for `type` is an irregular set used to present type names, and is presented as an array in JSON. This also means that a credential can belong to multiple types and can be used in different scenarios.

In order for Elastos ecosystem dApps to be able to have a unified understanding of types, definitions of the following basic types are provided in this specification:

| Type Name                    | Description                                                                                                                                              |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SelfProclaimedCredential     | DID holder‘s credentials for self-claims, without third party verification                                                                               |
| BasicProfileCredential       | Basic personal information credentials, can contain information like name, nickname, gender, age, region, and language.                                  |
| ApplicationProfileCredential | Basic personal information credentials for special applications, specific information related to the application.                                        |
| InternetAccountCredential    | Credentials for internet service accounts. Only supporting Google, Microsoft. Facebook, Twitter, Wechat, QQ, Taobao, Alipay, Weibo, and email addresses. |

> The type definitions described above are provided for the reference of applications, and those that can't be covered are to be expanded by the application.

For example, a self-claim credential fragment:

```json
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

For example, a credential fragment issued by Elastos IDteria self-service:

```json
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

#### Issuer

The `issuer` property is used to describe the issuer of this credential, and the value is the issuer's DID. This property is optional. If the credential is self-claimed by the DID holder, then this property can be omitted or can be their own DID, indicating that it is self-claimed; if the credential is issued by a third-party, then this property must be contained, and its value must be the DID of the issuer.

#### Issuance Date

`issuanceDate` is used to indicate the date and time of issue. The credential must contain an issuance date property. The property value must be a string of [RFC3339](https://tools.ietf.org/html/rfc3339) combining the date and time strings, standardized as UTC time followed by "Z." This date also indicates the earliest date that information related to the `credentialSubject` attribute came into effect.

#### Expiration Date (Optional)

`expirationDate` is used to indicate the date and time when the credential is no longer valid. Credential can contain this attribute. The attribute value must be a string value of [RFC3339](https://tools.ietf.org/html/rfc3339) combining the date and time strings, standardized as UTC time followed by "Z." If this attribute is not present, it means that this credential's expiration is consistent with the corresponding DID expiration date.

#### Credential Subject

A verifiable credential must contain a credential subject, which can contain one or multiple attributes, and is used to explain specific information about this attribute. It must contain one `id` attribute, defining this credential's corresponding DID. Other attributes are decided by the holder or application.

To facilitate a consistent understanding of credential information among Elastos ecosystem applications, attached is a list of recommended attributes:

| Attribute name    | Description                                                                                                                 |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------- |
| name              | name.                                                                                                                       |
| givenName         | Given name. Can be used with familyName to replace name.                                                                    |
| familyName        | Last name.                                                                                                                  |
| additionalName    | Middle name.                                                                                                                |
| nickname          | Nickname, handle.                                                                                                           |
| gender            | Gender. Recommended values: female, male, n/a.                                                                              |
| birthDate         | Date of birth.                                                                                                              |
| birthPlace        | Place of birth.                                                                                                             |
| image             | Profile picture. It is recommended that the value is a URL directing to an image, or an embedded image encoded with Base64. |
| description       | Description.                                                                                                                |
| address           | Address.                                                                                                                    |
| email             | Email.                                                                                                                      |
| telephone         | Telephone number.                                                                                                           |
| faxNumber         | Fax number.                                                                                                                 |
| city              | City of residence.                                                                                                          |
| nation            | Country.                                                                                                                    |
| language          | Primary language.                                                                                                           |
| url               | Entity's webpage.                                                                                                           |
| im                | Instant message address.                                                                                                    |
| googleAccount     | Google account.                                                                                                             |
| MicrosoftPassport | Microsoft account.                                                                                                          |
| facebook          | Facebook account.                                                                                                           |
| twitter           | Twitter account.                                                                                                            |
| telegram          | Telegram account.                                                                                                           |
| weibo             | Weibo account.                                                                                                              |
| qq                | QQ account.                                                                                                                 |
| wechat            | WeChat account.                                                                                                             |
| taobao            | Taobao Account                                                                                                              |
| alipay            | Alipay account.                                                                                                             |

> See definitions in [schema.org](https://schema.org/Person) for definitions of the attributes described above. For attributes that are not  covered, it is recommended that the application extends the attributes by following [schema.org](https://schema.org/Person).

#### Proof

`proof`is the necessary validation method and relevant evidence information provided by verifiable credentials. Of the two methods described in [W3C Verifiable Claims Specification](https://www.w3.org/TR/verifiable-claims-data-model/#proofs-signatures), including both external proof and embedded proof, this specification only uses the embedded proof method. Credentials must contain a proof property. Proof must contain the following properties:

- The default `type` is `ECDSAsecp256r1`, and can be omitted.
- `verificationMethod` indicates the verification method, the value is the public key reference used for signatures and verification in the issuer's DID document.
- `signature` indicates the signature value, coded using Base64URL.

### Verifiable Presentations

Secure design is the important purpose of the DID and of verifiable claims, so it is very important for entities to be able to provide the minimum necessary individual credential information for verification method requirements and personal roles. Verifiable presentation refers to data sets that includes a subset of the entity's verifiable credentials and a countersignature used to prove one’s own identity to a third party.

Speaking generally, a verifiable presentation contains credentials for one DID identity, and these credentials can be issued by different third-party entities to present the entity's identification information.

Definitions of properties that can be contained by verifiable presentations are as follows:

#### Type

`type` indicates that the type of the target data is one verifiable presentation, and its value is `VerifiablePresentation`.

#### Creation Time (Optional)

`created` is the time at which this verifiable presentation was created, and the value of the property must be a string of [RFC3339](https://tools.ietf.org/html/rfc3339) combining the date and time strings, standardized as UTC time followed by  "Z."

#### Verifiable Credential

`verifiableCredential` is an array of verifiable credentials and can be a subset of credentials provided by the holder according to the application scenario or the role requirements.

#### Proof

`proof` is the verification method and related proof information provided by the holder for this verifiable presentation. The proof rules are as follows:

- The default `type` is `ECDSAsecp256r1`, and can be omitted.
- `verificationMethod` indicates the verification method, and the value is the public key reference in the provider's DID document used for signatures and verification.
- `nonce` indicates the random value used by the signature operation.
- `realm` indicates the target realm where this presentation is applicable, such as a domain name, application name, etc.
- `signature` indicates the signature value, coded using Base64URL.

### Examples

#### Self Claim Verifiable Credentials

```json
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

#### Verifiable credentials issued by a third party

```json
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

#### Verifiable Presentation

```json
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

> REF: <https://w3c.github.io/vc-imp-guide/>

## Workflow and Lifecycle

```sequence
Holder->DIDClientApp: Issue credential submission
DIDClientApp->Issuer: Request credentials
Issuer-->DIDClientApp: Credential application form
DIDClientApp-->Holder: Present form to Holder
Holder->DIDClientApp: Fill out form
DIDClientApp->Issuer: Submit form
Issuer->Issuer: Review verification form
Issuer->DIDClientApp: Issue verifiable credentials
note over DIDClientApp: Verifiable credentials stored here
Holder->Service/Verifier: Log in
Service/Verifier->DIDClientApp: Credential request\n(selective disclosure request)
DIDClientApp->Holder: Confirm request
Holder->DIDClientApp: authorize confirmation
DIDClientApp->DIDClientApp: Sign identity presentation
DIDClientApp->Service/Verifier: Verifiable presentation
Service/Verifier->DLT: Obtain related DID within the verifiable presentation
DLT->Service/Verifier: DID document
Service/Verifier->Service/Verifier: Verify the verifiable presentation provided by Hodler
note over Service/Verifier: Complete login
```

The premise of this workflow and lifecycle is that both the Holder and Issuer have already registered for a DID and have logged into DLT.

## Other

Please see [W3C Verifiable Claims Specification](https://www.w3.org/TR/verifiable-claims-data-model/) for the definition of items not included in this specification.
