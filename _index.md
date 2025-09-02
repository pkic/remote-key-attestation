---
date: 2024-02-01T7:00:00Z
title: Remote Key Attestation
summary: Remote key attestation is a capability for an entity to show to a remote party certain attributes of cryptographic keys. For example that they are generated in a hardware cryptographic module and not exportable. The PKI Consortium is working to promote the adoption of remote key attestations and is managing a list of cryptographic modules describing their attestation capabilities.
sideMenu: true

heroTitle: Remote Key Attestation
heroDescription: A list of cryptographic devices that includes support for remote key attestations

tags:
- resource
---

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
| Azure Managed HSM              | :heavy_check_mark: |                       |[Validate Azure Managed HSM keys with key attestation](https://learn.microsoft.com/en-us/azure/key-vault/managed-hsm/key-attestation) / [Python validation script](https://github.com/Azure/azure-managed-hsm-key-attestation/blob/main/src/vendor/marvell/marvell_validate_key_attestation.py)| |
| **HSMs**                       |
| Crypto4A QASM                  |:heavy_check_mark:  | Proprietary/PEM       | https://support.crypto4a.com/public/documentation/C4A-302-0043-AttestationInQasm.html||
| Entrust nShield                | :heavy_check_mark: | JSON                  | https://nshielddocs.entrust.com/key-attestation-docs/v1.0.2/intro.html  | |
| Utimaco CryptoServer           | :x:                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          |                                                                                                                       |
| Thales Luna                    | :heavy_check_mark: | CMS/PKCS#7            | [Meeting CA/Browser Forum Standards with Luna and Luna Cloud HSMs](https://data-protection-updates.gemalto.com/2020/04/15/public-key-confirmation-meeting-ca-browser-forum-standards-with-luna-and-luna-cloud-hsms/) / [Public Key Confirmations](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/admin_partition/confirm/confirm_hsm.htm) | |
| Marvell HSMCMS/PKCS#7          | :heavy_check_mark: | Proprietary/Binary    | https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/software-key-attestation.html | GCP Cloud HSM, AWS CloudHSM and MS Managed HSM are using Marvell hardware in the background                           |
| Securosys Primus HSM           | :heavy_check_mark: | XML with external sig | [HSM User Guide Docs](https://www.securosys.com/hubfs/Securosys_PrimusHSM_KeyAttestation_SB-E01.pdf) | |
| I4P Trident HSM                | :heavy_check_mark: | CMS/PKCS#7            | https://www.i4p.com/documents/Trident_RSS_summary_sheet_200929.pdf | No detailed documentation about using key attestation available publicly. |
| Fortanix                       | :heavy_check_mark: | JSON                  |[Verifying Key Attestation Statements Doc](https://support.fortanix.com/hc/en-us/articles/18924491083028-Using-Fortanix-DSM-for-Verifying-Key-Attestation-Statements) | |
| **Tokens**                     |
| Yubico                         | :heavy_check_mark: | X.509                 | [Attestation Concept](https://developers.yubico.com/YubiHSM2/Concepts/Attestation.html) [PIV Attestation](https://developers.yubico.com/PIV/Introduction/PIV_attestation.html) | |
| Trusted Platform Module        | :heavy_check_mark: | TPMS_ATTEST/PKCS#10   | [TPM Fundamentals](https://www.cs.unh.edu/~it666/reading_list/Hardware/tpm_fundamentals.pdf) / [MS Key Attestation](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation) / [MS CSP with Key Attestation](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-wcce/f596c7df-a72c-4323-b27f-3c8646604ddb?redirectedfrom=MSDN) / [TCG Trusted Attestation Protocol](https://trustedcomputinggroup.org/wp-content/uploads/TNC_TAP_Information_Model_v1.00_r0.29A_publicreview.pdf) |                                                                                                                       |
| Century Longmai PKI Token      | :x::clock1:        | CMS/PKCS#7            |                                                                                                                                                                                                                                                                                                                                                                                                                                          | Claimed roadmap item                                                                                                  |
| TrustSec SLCOS - Bio/PKI token | :x:                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                          |                                                                                                                       |
| SmartCard-HSM                  | :heavy_check_mark: | CVC, BSI TR-03110-3   | [Remote Key Attestation explained](https://www.smartcard-hsm.com/2024/02/24/Key_Attestation_explained.html)                                                                                                                                                                                                                                                                                                                              |                                                                                                                       |
| **Other Devices**              |
| Apple iOS                      | :heavy_check_mark: | X.509/ACME/CBOR/WebAuthn         | [Apple](#apple) |  |
| Android                        | :heavy_check_mark: | ASN.1/CBOS/COSE                 | [Android](#android)  |  |

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

