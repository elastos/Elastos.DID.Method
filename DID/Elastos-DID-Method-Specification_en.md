# Elastos DID Method Specification v0.2

Smart Web Powered by Blockchain

----

Elastos Foundation

30 November 2019

**Version**

0.2

**Description of Contents**

This document is the Elastos DID Method Technical Specification. It is published and maintained by the Elastos Foundation. Its aim is to explain the general data model and format of Elastos DID, as well as related operations. In the future, Elastos Foundation will continue to upgrade this document so that it reflects the latest state of development of the Elastos DID technology.

**Copyright Statement**

The copyright of this document belongs to the Elastos Foundation. All rights reserved.

----

[TOC]

----

## Abstract

On the current internet, identification systems are centralized, with most of these systems being offered by Internet service giants. Under these market circumstances, the public is forced to trust these commercial internet services (be it a company, organization, or other bodies). The process is simple: the company collects user data, while simultaneously taking on the responsibility of securing the data. On the surface, the problem does not seem apparent to most. Yet the issue of data security consistently arises in centralized identity systems revealing their inherent flaws and the risks they present to users with little alternative. Identity breaches are extremely serious. In centralized identity systems, individuals do not have control over their personal information, and in a worst case scenario, services have been known to maliciously leak or share personal information without the user's knowledge.

After the development of blockchain technology, the proposition of a blockchain-based decentralized ID system, where digital identities would no longer be provided by centralized commercial services, but rather be recorded on the decentralized blockchain began to arise. In this new decentralized system, digital identity is under the complete control of the user, and no longer relies on the former control that centralized organizations have over data. DID can also be verified and used for multiple scenarios, such as identity verification and data encryption.

DID is completely under the control of the DID subject, without reliance on any centralized registration body, commercial identity provider, or organization issuing certificates. The DID is described in the DID documents. Each DID document includes at least two items: encryption materials and verification methods. The encryption materials integrated with the verification methods provides a set of identify verification mechanisms (such as a public key, anonymous biological identification agreement, etc.), with other optional parts that can be used according to the needs of the application and of the user.

### Goals of this Specification

There are two goals for the creation of this specification. The first is to define an internal Elastos system standard for the DID documents description format and a basic DID property set, allowing Elastos ecosystem apps to understand the basic digital identity information defined in the standard set, thereby achieving standardization of digital identities in the Elastos ecosystem. The secondary goal is to define a set of general operation methods that can be used for any dApp to reference to the Elastos DID service.

### Design Goals

The design of this specification is based on [W3C DID Specification Working Draft](https://w3c-ccg.github.io/did-spec/), with reference to the relevant files and code of [DIF](https://identity.foundation/), utilizing a subset of W3C DID, and conforms to the W3C DID specification requirements.

Having a clean and usable design were major considerations, and we hope that the DID documents have the ability to achieve a reasonable equilibrium in areas like human readability, structure, and ease of processing and use. The methods of DID-related operations have favorable usability, reducing the engineering costs of using DID in dApps.

Some terminology used in [W3C DID Specification Working Draft](https://w3c-ccg.github.io/did-spec/) will not be explained again in this document; please refer to the original document for more information.

## DID Method

Elastos DID method is fixed as `elastos`. In the Elastos application system, “method” can be omitted by directly utilizing did:specific-idstring, application default distinguished as the Elastos method, i.e., the effective Elastos DID.

## Goal System

The Elastos DID’s DLT back-end utilizes the Elastos ID Sidechain, and applies to all Elastos systems and dApps.

DID-related operations are implemented by the DID subject holder through sending a transaction through the DID client, and the DID-related operation and DID documents are all stored in the transaction payload.

## DID Identifier

Elastos DID utilizes the identifier format defined in [W3C DID Specification Working Draft](https://w3c-ccg.github.io/did-spec/), conforms to the standard URI [RFC3986](https://tools.ietf.org/html/rfc3986), and consists of a DID plus an optional DID path and DID fragment.

The following is the ABNF definition of Elastos DID identifier that uses [RFC5234](https://tools.ietf.org/html/rfc5234) syntax:

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

## DID Text String

ID text strings within Elastos DID is an ID Sidechain address encoded using  Bitcoin-style Base58 and starting with the letter "i", such as`icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN`. The DID text string is case sensitive.

## DID Document

### DID Document Format

DID Document **must** be a single JSON object conforming to [RFC8259](https://tools.ietf.org/html/rfc8259), while DID document data saved on the ID Sidechain is also based on the JSON format. Elastos DID documents do not utilize the [JSON-LD](https://www.w3.org/TR/json-ld/) format specified in the W3C DID specification working draft, and only use simple JSON. This type of design is intended to maintain the simplicity of the DID documents and the convenience of app development. Related properties and naming are defined in accordance with the W3C DID specification; at the same time, for interoperations with the W3C DID standard, Elastos DID will provide a mapping mechanism to the W3C DID documents.

### DID Document Properties

#### Subject

The subject is a unique identifier of a DID document and is used to represent the corresponding DID document. The rules of the subject are as follows:

- DID documents must have one and only one DID subject.
- The subject’s key must be `id`.
- The subject’s value must be a valid DID identifier.
- When such a DID document is registered on the ID Sidechain, this DID identifier should be consistent with the holder's public key.

For example:

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN"
}
```

#### Public Keys

Public keys are used for digital signatures, encryption and other cryptographic operations, which in turn are the basis for purposes such as authentication or establishing secure communication with service endpoints. In addition, the public key can also be used for DID authorization and delegation and may play a role in DID CRUD operations to verify the legality of operations.

Elastos DID documents will need to include at least one public key that corresponds with the DID identifier.

Public key rules are as follows:

- DID documents must include a  `publicKey` property.
- The value of the `publicKey` property must be an array of public key.
- Each public key object must have an  `id` and `type` properties. The array of public keys should not contain multiple entries with the same `id` and duplicate items with different value properties in different formats.
- The value of the `id` property is composed of this DID’s identifier and a self-defined URI fragment, such as `did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key`, and with the goal of maintaining tight data, the DID identifier can be omitted using only the URI fragment, such as `#master-key`.
- Elastos public key’s `type` default is `ECDSAsecp256r1` and can be omitted.
- Each public key can include a `controller` property used to show the DID of the corresponding private key’s controller; the default is the DID where the document is located, and in this case, the property can be omitted.
- Each public key must include a `publicKeyBase58` property used to store the Base58 encoded public key.

For example:

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  "publicKey": [{ // public key corresponding to the DID address
    "id": "#master-key",
    "type": "ECDSAsecp256r1",
    "controller": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
    "publicKeyBase58": "zNxoZaZLdackZQNMas7sCkPRHZsJ3BtdjEvM2y5gNvKJ"
  }, { // the second public key that this DID owner holds, type and controller are omitted here
    "id": "#key-2",
    "publicKeyBase58": "273j8fQ1ZZVM6U6d5XE3X8SyULuJwjyYXbxNopXVuftBe"
  }, { //  used for recovering a public key, the trustful third party holder for this DID owner
    "id": "#recovery-key",
    "type": "ECDSAsecp256r1",
    "controller": "did:elastos:ip7ntDo2metGnU8wGP4FnyKCUdbHm4BPDh",
    "publicKeyBase58": "zppy33i2r3uC1LT3RFcLqJJPFpYuZPDuKMeKZ5TdAskM"
  }],
  ...
}
```

#### Authentication (Optional)

Authentication is the mechanism by which an entity can cryptographically prove that they are associated with a DID. Authentication property can define which DID’s public keys can be used for identity authentication. Based on the technical implementation characteristics of the ID Sidechain, the public key corresponding to the DID subject possesses identity authentication functionality by default, even if there is no explicit statement.

The identity verification rules are:

- DID documents can include a maximum of one `authentication` property.
- The value of `authentication` property is an array of verification methods, that is an array of public keys used for the identity verification.
- The verification method can be embedded or referenced. When it is referenced, it can either be a complete public key URI or just a part of a fragment, such as: `did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-key` and `#master-key` are identical, where both reference the same public key. If an embedded public key is used, the rules for writing public keys are consistent with [Public Keys property](#public-keys).

For example:

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "authentication": [
    // the reference of the public key which can be used for authenticating
    "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#master-keys",
    // the reference of the public key which can be used for authenticating
    "#key-2",
    // the public key is *only* authorized for authentication, and its full description is embedded here,
    // this public key cannot be used for any other purpose
    {
      "id": "#keys-3",
      "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  ],
  ...
}
```

#### Authorization and Delegation (Optional)

Authorization is the mechanism used to state how operations may be performed on behalf of the DID subject. Delegation is the mechanism by which the DID subject uses to authorize others to act on their behalf. This is especially important for DID security in the case of DID’s key loss. When the subject no longer has access to their keys or the key has been leaked, the trustful third party authorized by the DID holder can declare to deactivate this DID, thus prohibiting malicious actions in the case that keys are leaked.

Elastos DID’s authorization and delegation only support necessary minimum authorization, i.e. authorizing a trusted third-party is used for deactivating the target DID and does not support other operations.

The rules for authorization and delegation are:

- DID documents can include a maximum of one `authorization` property.
- The value of the `authorization` property should be one array of verification methods, which can be an array of public keys used for delegation.
- Each verification method can be embedded or referenced. When referenced, it can either be one complete public key URI, or just a fragment, such as: `did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#recovery-key` and `#recovery-key` are identical, and both reference the same public key. If an embedded public key is used, the rules for writing public keys are consistent with [Public Keys property](#public-keys).

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "authorization": [
    // the reference of delegated target public key which can be abbreviated as "#recovery-key"
    "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN#recovery-key",
    // *only used for* delegated public keys, and its full description is embedded in “authorization”,
    // this public key cannot be used for other purposes
    {
      "id": "#keys-3",
      "controller": "did:elastos:iZUeeATkmBwLuEx4B41P6cvvFQYuzSQJxf",
      "publicKeyBase58": "cheFmhCj9DcSvjYEFeCQQFrrVJnG8sg3xvXRPeP1xRxi"
    }
  ],
  ...
}
```

#### Verifiable Credential (Optional)

The DID is designed with the intent that, in principle, the DID documents should not bring any personal information/Personally-Identifiable Information (PII), or any other information that can be connected to the critical information of the entity. But from the perspective of real world needs, some DID entities need to have an open identity, such as well-known Credential Issuers, so the Elastos DID documents can embed public verifiable credentials to support the need to bind DID with entity information publicly. Please see [Elastos Verifiable Claims Specification](/VerifiableClaims/Elastos-Verifiable-Claims-Specification_cn.md) for the specific definition of verifiable credentials.

The rules for verifiable credentials are:

- The DID documents can include a maximum of one `verifiableCredential` property.
- The value of the `verifiableCredential` property should be a single array of  verifiable credentials.
- In verifiable credentials, the `id` property in `credentialSubject` can be omitted, and the default is the current DID subject; if the credential is a complete self statement, then the `proof` property of the `verifiableCredential` can be omitted, because this claim is updated under the control of the user's private key.

For example:

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

#### Service Endpoints (Optional)

One of the primary purposes of a DID document is to enable discovery of service endpoints. A service endpoint can be any type of service DID subject wishes to advertise, including decentralized identity management services for further discovery, authentication, authorization, or interaction.

The rules for Service Endpoints are:

- DID document can include a maximum of one `service` property.
- The value of the `service` property should be an array of service endpoints.
- Each service endpoint must include `id`, `type` and `serviceEndpoint` properties, and can include other properties set by the application.
- Service endpoint agreements should be released with open standard specifications.
- The value of the `serviceEndpoint` property must be a valid URI conforming to [RFC3986](https://tools.ietf.org/html/rfc3986), and are normalized according to the rules in [RFC3986 Section 6](https://tools.ietf.org/html/rfc3986#section-6).

For example:

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

#### Created (None)

An Elastos DID document does not include the `created` property, because ID Sidechain transactions are used for recording, so naturally they have a timestamp and the document does not need to explicitly state the creation time. The Elastos DID resolver will automatically add this property at the time it obtains the DID documents from the blockchain.

#### Updated (None)

An Elastos DID document does not include the `updated` property, because they are recorded by the ID Sidechain transactions. Therefore, they naturally have a timestamp and the documents do not need to explicitly state the creation time.  The Elastos DID resolver will automatically add this property at the time it obtains the DID documents from the blockchain.

#### Proof

The `proof` property on a DID document is cryptographic proof of the integrity of the DID document . In order to ensure the authenticity and integrity of the DID document, the proof must include a signature for this DID document, and the signature must be generated using the private key corresponding to the DID subject, thus guaranteeing the integrity of the document and preventing attacks from intermediaries.

The rules for proof are:

- DID documents must include a maximum of one `proof`.
- The default `type` is `ECDSAsecp256r1`, and can be omitted.
- `created` indicates the signature creation time, and can be omitted.
- `creator` indicates the reference to the private key for the signature validation, the value of which must be a reference to the private key corresponding to the DID subject; this can be omitted.
- `signatureValue` indicates the signature value using Base64 encoding.

For example:

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

#### Expires

Due to security concerns, the Elastos DID subject has an expiration date which can be set to a maximum of 5 years and users can also set a shorter expiration date according to their own needs. DID subjects that surpass the expiration date will be identified by the DID resolver as invalid.

The rules for expiration are:

- DID documents must include one `expires` property.
- The value of this key must be a valid text string value conforming to [RFC3339](https://tools.ietf.org/html/rfc3339) combining date and time.
- The datetime value must be normalized to UTC time, followed by "Z."

For example:

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  ...
  "expires": "2024-02-10T17:00:00Z"
}
```

### Extensibility

In addition to abiding by the aforementioned standard properties for document definition, the application can extend the DID documents according to the needs of the application. The extended portion should abide by the aforementioned stipulations for document format, and when adding properties defined by the application itself, we recommend using JSON-LD to state the context of the extended properties, for the convenience of correctly processing application-extended properties as Elastos DID clients normalize the Elastos DID documents as a W3C standards-compatible DID documents; otherwise, the application's extensibility properties will be ignored, and will not be included in the final normalized DID documents.

> Regarding processing extensibility properties and the constraints of JSON-LD context, the aforementioned rules are only hypothetical as there are no actual applications at present. In the future, this can be adjusted according to the needs of the application, as well as the DID documents normalized processing situation.

## DID Operations

Elastos DID documents storage is based on ID Sidechain, so related operations all must be performed on clients that support ID Sidechain, and must be implemented by sending themselves a transaction using a public key corresponding to the DID subject. But there is an exception in deactivating DID, which allows the DID public keys that have been authorized and delegated by the DID subject to issue a deactivation operation.

DID operations and corresponding documents are saved in the transaction’s payload using the JSON format, and the properties of the JSON document operated by the DID are defined as follows:

- Must contain one `header` property, which includes basic information for DID operations. `header` property is defined as follows:
  - Must contain one `specification` property, indicating the specification and version on which the DID operation conforms; currently, only `elastos/did/1.0` is supported.
  - Must include one `operation` property, which is used to explain which kind of operation is being executed; for more information about the property value, reference the section of the specific operation.
  - According to the needs of the operation, it can include one `previousTxid` property. See the section on the specific operation for more information about property definition.
- Must include one `payload` property, which is the target DID document or DID subject of the operation.
- Must include one `proof` property, which includes the signature and the DID holder’s public key to operate  the DID, used to prove that the operation is being executed by the holder themselves or a delegate. The `proof` property is defined as follows:
  - The default `type` is `ECDSAsecp256r1`, and can be omitted.
  - `verificationMethod` indicates the verification method, the value is the reference of the public key in the issuer's DID document for signing and verification.
  - `signature` indicates the signature value, using Base64 encoding.

Because blockchain is powered by an economic process, transactions must include a transaction fee, and an additional fee is collected according to the size of the payload, so from the perspective economization, DID documents should be as brief as possible to control the size of information being added to the blockchain. So, in general, DID documents being added to the blockchain can omit default properties, such as public key’s `type` and `controller` which is the same as the current DID subject; URIs referenced by properties in the documents must be written as little as possible, which can effectively reduce the cost of DID operations.

### Create DID

Creating a DID requires the DID client to generate a DID document locally based on the key pair owned by the holder, and populate the relevant properties, including the public key by default, and generation of a DID subject based on the public key, and authentication with the public key. Other properties are filled in by the application or user as required.

The rules for creating DID are:

- Must include one `header` property, wherein the `operation` property value is `create`, indicate to create a new DID.
- Must include a `payload` property, and the value is the base64 URL encoded document for the DID document.
- Must include one `proof` property, including the DID holder's public key reference and signature, used to prove that this operation is sent by the DID holder.

For example:

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

The payload is the result of the following DID document encoded by Base64 URL:

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

### Update DID

Only the DID subject holder has the right to update the DID. Non-DID subject holders who initiate DID update transactions will be filtered out by the DID resolver, and will not affect the target DID in any way. When updating the DID, incremental update is not supported, and complete DID documents must be attached to the update transaction.

The rules for DID updates are as follows:

- Must include one `header` property, wherein the `operation` property value is `update`, indicate to update a DID.
- `header` must include the `previousTxid` property, and the value is the transaction ID of the previous DID document operation. Used to avoid unnecessary erroneous update operations.
- Must include the `payload` property, and the value is the new DID document.
- Must include one `proof` property, including the DID holder's public key reference and signature, used to prove that this operation is initiated by the DID holder.

For example:

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

### Deactivate DID

A DID holder can deactivate a DID. For example, if a DID holder is no longer using a DID, or believes that the DID's corresponding private key has been leaked, then they may choose to deactivate the DID, because at this time, the DID the holder still has the private key.

If a DID holder loses the private key, then he or she will have lost the right to control the DID, and it would be impossible to directly perform any update operations to the DID, including deactivation. If the DID document contains delegation and authorization, then the trusted third party delegate can issue a deactivation transaction to the DID's corresponding address, and thereby deactivate the DID on behalf of the DID holder, thus preventing the DID from being maliciously used.

The rules for deactivating a DID are:

- Must include one `header` property, wherein the `operation` property value is `deactivate`, indicate to deactivate the DID.
- Must include the `payload` property, and the value is the target DID subject.
- Must include one `proof` property, including the DID holder's or delegate's public key reference and signature, used to prove that this operation is initiated by the DID holder or delegate.

For example:

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

### Reading/Verifying DID

DID documents are saved on the ID Sidechain, and any ID Sidechain node can read any DID subject's corresponding document, and as long as it reads the DID's corresponding transactions. Among those, valid DID transactions must be verified; valid transaction includes: transaction initiated by the DID holder to his or her self, as well as DID deactivation transactions initiated by DID holder with the delegation public key which is authorized and delegated in the DID document.

In actual use, reading and verification of the DID will generally be performed by the DID resolver.

## DID Resolver

DID resolver is a functional component for carrying out reading and verification of the DID, and its API design is used for accepting DID search requests and implementing corresponding DID methods to retrieve authoritative DID documents. In order to conform to W3C specifications, Elastos DID resolver processing is as follows:

1. Read all transactions related to the` DID subject from the blockchain, and process them in order chronologically;
2. Illegal transactions will be deleted during the course of processing (these can be understood as unauthorized transactions);
3. Then the final DID document is produced;
4. Verify whether the DID is valid according to the DID method specification, and if it is found to be invalid, then an error is produced;
5. According to the request return the properties or service corresponding to the DID document.

The current resolver design relies on the SPV client and is completely decentralized with good security, but at the same time it could be relatively time consuming during DID resolving. Application developers can provide trusted off-chain DID resolver services for their own apps according to needs. The service should be designed with security in mind to avoid attacks such as replay, message insertion, deletion, modification, imitators and man-in-the-middle.

## Security Considerations

Elastos DID uses the ID Sidechain as the DID decentralized storage system and the hashrate obtained through both the main chain and BTC merged mining to act as a security guarantee for the ID Sidechain. On this basis, the self-sovereign needs of the DID's are fully considered.

DID operations are completed by blockchain-based transactions, and the security of DID operations is supported by the security of the transaction; at the same time, DID operation entities are strictly limited to ensure the DID will not be attacked by replay, message insertion, deletion, modification, imitators and man-in-middle, thus ensuring the security of the DID.

Based on the aforementioned security strategies, through the authorization and delegation mechanism, the DID deactivation mechanism is implemented in the abnormal case of the DID after the holder loses the key, which ensures that the DID is not used maliciously.

## Privacy Considerations

Privacy and self-sovereignty are the basic principles of the DID design, and the Elastos DID privacy protection strategy abides by the stipulations of W3C. In principle, the DID documents should not contain any entity information, or any critical information that can be associated with a specific entity. The personal information credentials of an entity are generally managed and saved on the terminal device by the entity or individual, and that person or entity has the full power to control it. It can then be provided to the verifier in the form of verifiable claims; but some applications, scenarios or entities need to disclose entity information, so for the convenience of similar needs, Elastos DID has added an **optional** embedded verifiable credential attribute, and the entity of the DID subject’s holder can autonomously decide whether or not to use it. This embodies the self-sovereign spirit of the DID while also conforming to the requirements of [GDPR](https://eugdpr.org/).

## Examples

### The simplest possible DID Document

```json
{
  "id": "did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN",
  "publicKey": [{
    "id": "#key",
    "publicKeyBase58": "zNxoZaZLdackZQNMas7sCkPRHZsJ3BtdjEvM2y5gNvKJ"
  }]
}
```

This DID document contains a public key by default, and is the public key that corresponds to the DID subject; other DID default properties are as follows:

- The public key `#key`'s `type` is `ECDSAsecp256r1`.
- The public key `#key`'s `controller` is the holder of the target DID subject `did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN`.
- Public key `#key` can be used to authenticate the identity of the DID subject holder.
- Valid for 5 years.

### A Complex/Redundant DID Document

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
