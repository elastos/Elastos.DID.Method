# Elastos DID Method v0.2

This document is the Elastos DID Method Technical Specification. It is published and maintained by the Elastos Foundation. Its aim is to explain the general Elastos DID data model and format, as well as related operations. In the future, Elastos Foundation will continue to upgrade this document so that it reflects the current state of development of the Elastos DID technology.

## Abstract

On the current internet, identity systems are centralized, with most of these systems being offered by internet giants. Under these market circumstances, the public is forced to trust these commercial internet services (be it a company, organization, or other bodies). The process is simple: the company collects user data, while simultaneously taking on the responsibility of securing the data. On the surface, the problem does not seem apparent to most. Yet the issue of data security consistently arises in centralized identity systems revealing their inherent flaws and the risks they present to users with little alternative. Identity breaches are extremely serious. In centralized identity systems, individuals do not have control over their personal information, and in a worst-case scenario, services have been known to maliciously leak or share personal information without the user's knowledge.

After the development of blockchain technology, the proposition of a blockchain-based decentralized ID system, where digital identities would no longer be provided by centralized commercial services, but rather be recorded on the decentralized blockchain began to arise. In this new decentralized system, digital identity is under the complete control of the user, and no longer relies on the former control that centralized organizations have over data. DID can also be verified and used for multiple scenarios, such as identity verification and data encryption.

DID is completely under the control of the DID unit, without reliance on any centralized registration body, commercial identity provider, or organization issuing certificates. The DID is described in the DID document. Each DID document includes at least two items: encryption materials and an identity verification kit. The encryption materials integrated with the identity verification kit provides a set of identify verification mechanisms (such as a public key, anonymous biological identification agreement, etc.), with other optional parts that can be used according to the needs of the application and of the user.

## Specifications

| Sspecification    | Link                                                                  |
| ----------------- | --------------------------------------------------------------------- |
| Method            | [CN](/DID/Elastos-DID-Method-Specification_cn.md)                     |
| Resolve           | [CN](/Resolver/Elastos-DID-Resolver-Specification_cn.md)              |
| Verifiable Claims | [CN](/VerifiableClaims/Elastos-Verifiable-Claims-Specification_cn.md) |

## DID SDK

| Language | Link                                                                    |
| -------- | ----------------------------------------------------------------------- |
| Java     | [Elastos.DID.Java.SDK](https://github.com/elastos/Elastos.DID.Java.SDK) |
