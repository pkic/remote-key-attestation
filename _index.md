---
date: 2023-03-21T7:00:00Z
title: Remote Key Attestation

heroTitle: Remote Key Attestation
heroDescription: Key attestation, in this context, is the technical ability to prove to a remote party that a private key was generated inside, and is managed inside, and not exportable from, a hardware cryptographic module

tags:
- resource
---

## Contribute

This list is a collaborative effort. To contribute please raise an issue or a PR on the [GitHub Repository](https://github.com/pkic/remote-key-attestation).

## Use cases

Common use cases for key attestation are:
* Issuing code signing certificates for subscriber keys, verifying that the subscribers private signature key is generated and managed in an approved cryptographic device.
* Issuing digital signature certificate for subscriber keys, verifying that the subscribers private signature key is generated and managed in an approved cryptographic device.

There are other ways to achieve the same purpose, such as shipping hardware devices (USB tokens, smart cards, etc) to the subscriber, or requiring a formal audit of the key generation procedure from the subscriber. Using remote key attestation makes this process more efficient and possible to automate in a larger scale.

## Implementations

The table lists known hardware cryptographic devices and their support, or non-support, for remote key attestation.

| Vendor/Model                   | Capability         | Format                | Documentation                                                                                                                                                                                                                                                                                                                                                                                                                            | Notes                                                                                                                 |
| ------------------------------ | :----------------- | :-------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------- |
| **Cloud HSMs**                 |
| Google CloudHSM                | :heavy_check_mark: | JSON                  | https://cloud.google.com/kms/docs/attest-key                                                                                                                                                                                                                                                                                                                                                                                             |                                                                                                                       |
| AWS CloudHSM                   | :x:                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          |                                                                                                                       |
| AWS KMS                        | :x:                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          |                                                                                                                       |
| Azure Key Vault                | :x:                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          |                                                                                                                       |
| Azure Managed HSM              | :x::clock1:        |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          | Claimed to be on the roadmap                                                                                          |
| **HSMs**                       |
| Entrust nShield                | :x::clock1:        |                       | https://github.com/pkic/remote-key-attestation/issues/3                                                                                                                                                                                                                                                                                                                                                                                  | Claimed to be on the roadmap                                                                                          |
| Utimaco CryptoServer           | :x:                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          |                                                                                                                       |
| Thales Luna                    | :heavy_check_mark: | CMS/PKCS#7            | [Meeting CA/Browser Forum Standards with Luna and Luna Cloud HSMs](https://data-protection-updates.gemalto.com/2020/04/15/public-key-confirmation-meeting-ca-browser-forum-standards-with-luna-and-luna-cloud-hsms/) / [Public Key Confirmations](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/admin_partition/confirm/confirm_hsm.htm) | |
| Marvell HSMCMS/PKCS#7          | :heavy_check_mark: | Proprietary/Binary    | https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/software-key-attestation.html | |
| GCP Cloud HSM, AWS CloudHSM and MS Managed HSM are using Marvell hardware in the background                           |
| Securosys Primus HSM           | :heavy_check_mark: | XML with external sig | [HSM User Guide Docs](https://www.securosys.com/hubfs/Securosys_PrimusHSM_KeyAttestation_SB-E01.pdf) | |
| I4P Trident HSM                | :heavy_check_mark: | CMS/PKCS#7            | https://www.i4p.com/documents/Trident_RSS_summary_sheet_200929.pdf | No detailed documentation about using key attestation available publicly. |
| Fortanix                       | :heavy_check_mark: | JSON                  |[Verifying Key Attestation Statements Doc](https://support.fortanix.com/hc/en-us/articles/18924491083028-Using-Fortanix-DSM-for-Verifying-Key-Attestation-Statements) | |
| **Tokens**                     |
| Yubico                         | :heavy_check_mark: | X.509                 | [Attestation Concept](https://developers.yubico.com/YubiHSM2/Concepts/Attestation.html) [PIV Attestation](https://developers.yubico.com/PIV/Introduction/PIV_attestation.html) | |
| Trusted Platform Module        | :heavy_check_mark: | TPMS_ATTEST/PKCS#10   | [TPM Fundamentals](https://www.cs.unh.edu/~it666/reading_list/Hardware/tpm_fundamentals.pdf) / [MS Key Attestation](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation) / [MS CSP with Key Attestation](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-wcce/f596c7df-a72c-4323-b27f-3c8646604ddb?redirectedfrom=MSDN) / [TCG Trusted Attestation Protocol](https://trustedcomputinggroup.org/wp-content/uploads/TNC_TAP_Information_Model_v1.00_r0.29A_publicreview.pdf) |                                                                                                                       |
| Century Longmai PKI Token      | :x::clock1:        | CMS/PKCS#7            |                                                                                                                                                                                                                                                                                                                                                                                                                                          | Claimed roadmap item                                                                                                  |
| TrustSec SLCOS - Bio/PKI token | :x:                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          |                                                                                                                       |
| **Other Devices**              |
| Apple iOS                      | :heavy_check_mark: | X.509/ACME/CBOR/WebAuthn         | [Apple](#apple) |  |
| Android                        | :heavy_check_mark: | ASN.1/CBOS/COSE                 | [Android](#android)  |  |

## Standards

There are a few standardization efforts available. Known efforts include:

* Recently (June 2022) draft submitted to IETF, [Key Attestation Extension for Certificate Management Protocols](https://datatracker.ietf.org/doc/html/draft-wallace-lamps-key-attestation-ext-01)

* This draft makes use of [WebAuthn Defined Attestation Statement Formats](https://www.w3.org/TR/webauthn-2/#sctn-defined-attestation-formats)

* [Attestation Attributes for use with Certificate Requests](https://datatracker.ietf.org/doc/draft-stjohns-csr-attest/)

* [ACME Device Attestation Extension](https://datatracker.ietf.org/doc/draft-bweeks-acme-device-attest/)

Other suggestions, which hasn't been seen live in any of the above implementations:

* [SKAE extension (Trusted Computing Group)](https://trustedcomputinggroup.org/wp-content/uploads/IWG_SKAE_Extension_1-00.pdf)

* [Remote ATtestation ProcedureS (rats)](https://datatracker.ietf.org/wg/rats/about/)

* [Entity Attestation Token (EAT)](https://datatracker.ietf.org/doc/draft-ietf-rats-eat/)

* [Using Attestation in Transport Layer Security](https://datatracker.ietf.org/doc/draft-fossati-tls-attestation/)

There is also a draft with [Use cases for Remote Attestation common encodings](https://datatracker.ietf.org/doc/html/draft-richardson-rats-usecases-08). Section 7 describes several of the encodings listed in the table above.

## Resources

* A [set of key attestations and corresponding X.509 certificate files](https://github.com/Purebred/SampleAttestations) generated by various Pixel, Surface Pro and YubiKey devices. By Carl Wallace.

## Vendor Details

### Android

Android provides multiple resources.

Key attestation using a custom ASN.1 format.

* [Android Developers Key Attestation](https://developer.android.com/training/articles/security-key-attestation)
* [AOSP Key and ID Attestation](https://source.android.com/security/keystore/attestation)

A (not well documented) certificate management protocol called KeyMint, which is conceptually a CBOR/COSE-based version of a CSR plus response, the utilized attestation technology is DICE.

* [Keymint cddl files](https://cs.android.com/android/platform/superproject/+/main:hardware/interfaces/security/rkp/aidl/android/hardware/security/keymint/)
* [Keymint DeviceInfoV3.cddl](https://cs.android.com/android/platform/superproject/+/main:hardware/interfaces/security/rkp/aidl/android/hardware/security/keymint/DeviceInfoV3.cddl)

### Apple

Apple provides multiple resources.

[Managed Device Attestation](https://support.apple.com/en-gb/guide/deployment/dep28afbde6a/web), in iOS 16 and later, can be used for key attestation. 

[DeviceCheck](https://developer.apple.com/documentation/devicecheck) [app integrity attestation](https://developer.apple.com/documentation/devicecheck/establishing_your_app_s_integrity) is  not usedfor the purpose of key attestation as defined here.

