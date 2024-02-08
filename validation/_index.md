---
date: 2024-02-01T7:00:00Z
title: Validation
sideMenu: true
---

## Validation

Usage of key attestations have many use cases. Due to this creation and validation of cryptographically secured attestations can look very different. There are standardization efforts on-going of attributes and attestation bundles but waiting for such standards to be widely used we need to use vendor specific attestations. In addition where cryptographically secured attestations are not available, verification of nin-signed attestations will also be needed.

For the purpose in the PKI Consortium we focus on a limited use case of key attestations, for keys residing on cryptograhic modules. The generic steps can be summarized in:

1. Creation of an attestation bundle from a crypgtographic module for a specific key-pair.
2. Creation of a CSR containing the public key of the key-pair.
3. Verification of the integrity of the attestation bundle.
4. Matching the public key in the CSR with the attestated key pair in the attestation bundle.
5. Verification of key attributes in the attestation.

Attributes to be verified are typically that the key pair was generated in the cryptographic module and that the private key is not exportable, but we are not prescriptive in attributes needed and leave that up to the module vendor to describe and the validating party to validate.

