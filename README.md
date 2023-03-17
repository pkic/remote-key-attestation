# Description
Key attestation, in this context, is the technical ability to prove to a remote party that a private key was generated inside, and is managed inside, and not exportable from, a hardware cryptographic module.


# Use cases
------------
Common use cases for key attestation are:
* Issuing code signing certificates for subscriber keys, verifying that the subscribers private signature key is generated and managed in an approved cryptographic device.
* Issuing digital signature certificate for subscriber keys, verifying that the subscribers private signature key is generated and managed in an approved cryptographic device.

There are other ways to achieve the same purpose, such as shipping hardware devices (USB tokens, smart cards, etc) to the subscriber, or requiring a formal audit of the key generation procedure from the subscriber. Using remote key attestation makes this process more efficient and possible to automate in a larger scale.

# Implementations
-----
The table lists known hardware cryptographic devices and their support, or non-support, for remote key attestation.

| Vendor/Model            | Capability | Format | Documentation                                                                                       | Notes |
| ----------------------- |:-----------|:-------|:----------------------------------------------------------------------------------------------------|:------|
|**Cloud HSMs**|
| Google CloudHSM         |:heavy_check_mark:| JSON   |https://cloud.google.com/kms/docs/attest-key                                                         |       |
| AWS CloudHSM            |:x:         |        |                                                                                                     |       |
| AWS KMS                 |:x:         |        |                                                                                                     |       |
| Azure Key Vault         |:x:         |        |                                                                                                     |       |
| Azure Managed HSM       |:x::clock1: |        |                                                                                                     |Claimed to be on the roadmap|
|**HSMs**|
| Entrust nShield         |:x::clock1: |        | https://github.com/pkic/remote-key-attestation/issues/3                                             |Claimed to be on the roadmap|
| Utimaco CryptoServer    |:x:         |        |                                                                                                     |       |
| Thales Luna             |:heavy_check_mark:|CMS/PKCS#7 |https://thalesdocs.com/gphsm/luna/7/docs/network/Content/admin_partition/confirm/confirm_hsm.htm<br>https://thalesdocs.com/gphsm/luna/7/docs/network/Content/Utilities/cmu/cmu_getpkc.htm|    |
| Marvell HSMCMS/PKCS#7             |:heavy_check_mark:|Proprietary/Binary|https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/software-key-attestation.html |GCP Cloud HSM, AWS CloudHSM and MS Managed HSM are using Marvell hardware in the background|
| Securosys Primus HSM    |:heavy_check_mark:| XML with external sig|https://www.securosys.com/hubfs/Securosys_PrimusHSM_KeyAttestation_SB-E01.pdf (Documentation in HSM User Guide)            |       |
| I4P Trident HSM         |:heavy_check_mark:|CMS/PKCS#7 |https://www.i4p.com/documents/Trident_RSS_summary_sheet_200929.pdf | No detailed documentation about using key attestation available publicly.      |
| Fortanix         |:x::clock1:|       |  | Claimed roadmap item for H1 2023 |
|**Tokens**|
| Yubico                  |:heavy_check_mark:| X.509  |https://developers.yubico.com/YubiHSM2/Concepts/Attestation.html<br>https://developers.yubico.com/yubico-piv-tool/Attestation.html<br>https://developers.yubico.com/PIV/Introduction/PIV_attestation.html                                       |       |
| Trusted Platform Module |:heavy_check_mark:| TPMS_ATTEST/PKCS#10|https://www.cs.unh.edu/~it666/reading_list/Hardware/tpm_fundamentals.pdf<br>https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation<br>https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-wcce/f596c7df-a72c-4323-b27f-3c8646604ddb?redirectedfrom=MSDN<br>https://trustedcomputinggroup.org/wp-content/uploads/TNC_TAP_Information_Model_v1.00_r0.29A_publicreview.pdf|       |
| Century Longmai PKI Token|:x::clock1:| CMS/PKCS#7 | | Claimed roadmap item |
| TrustSec SLCOS - Bio/PKI token|:x:|  | |  |
|**Other Devices**|
| Apple iOS                  |:heavy_check_mark:| CBOR/WebAuthn  |https://developer.apple.com/documentation/devicecheck<br>https://developer.apple.com/documentation/devicecheck/dcappattestservice/3573911-attestkey<br>https://developer.apple.com/documentation/devicecheck/validating_apps_that_connect_to_your_server                                       |Not for the purpose of key attestation as defined here, perhaps it can be used for additional purposes than intended?       |
| Android                  |:heavy_check_mark:| ASN.1  |https://developer.android.com/training/articles/security-key-attestation<br>https://source.android.com/security/keystore/attestation  | Custom ASN.1 format      |

# Standards
--------
There are a few standardizaton efforts avaialable. Known efforts include:

* Recently (June 2022) draft submitted to IETF, [Key Attestation Extension for Certificate Management Protocols](https://datatracker.ietf.org/doc/html/draft-wallace-lamps-key-attestation-ext-01)

* This draft makes use of [WebAuthn Defined Attestation Statement Formats](https://www.w3.org/TR/webauthn-2/#sctn-defined-attestation-formats)

Other suggestions, which hasn't been seen live in any of the above implementations:

* SKAE  extension (Trusted Computing Group): https://trustedcomputinggroup.org/wp-content/uploads/IWG_SKAE_Extension_1-00.pdf

* Remote ATtestation ProcedureS (rats):
https://datatracker.ietf.org/wg/rats/about/

* Entity Attestation Token (EAT):
https://datatracker.ietf.org/doc/draft-ietf-rats-eat/

* Using Attestation in Transport Layer Security: https://datatracker.ietf.org/doc/draft-fossati-tls-attestation/

There is also a draft with [Use cases for Remote Attestation common encodings](https://datatracker.ietf.org/doc/html/draft-richardson-rats-usecases-08). Section 7 describes several of the encodings listed in the table above.
