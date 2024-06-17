---
date: 2024-06-12
title: Crypto4A QASM
sideMenu: true
---

# Crypto4A QASM

The Crypto4A Quantum Assured Security Module (QASM) has an attestation capability that can be used to verify various claims in the HSM. For more detailed explanations on how this capability is implemented, please read [this document](https://support.crypto4a.com/public/documentation/C4A-302-0043-AttestationInQasm.html).

# Table of contents

1. [Trust](#trust)
2. [Generating Attestations](#generating-attestations)
3. [Verifying Attestations](#verifying_attestations)
   1. [Verifying Integrity](#verifying-integrity)
   2. [Matching the Key in the CSR](#matching-the-key-in-the-csr)
   3. [Verifying Key Attributes](#verifying-key-attributes)
4. [Examples](#examples)

## Trust

Trust for the QASM attestations is anchored in the current root certificate from Crypto4A's Authority Key Infrastructure (AKI) version 1.0. Version 2.0 is scheduled to have PQC roots.

```
-----BEGIN CERTIFICATE-----
MIICVDCCAdqgAwIBAgIULNg2gybw50xYlV7zvhXBRcggip8wCgYIKoZIzj0EAwMw
WTEPMA0GA1UEBgwGQ2FuYWRhMRAwDgYDVQQIDAdPbnRhcmlvMQ8wDQYDVQQHDAZP
dHRhd2ExETAPBgNVBAoMCENyeXB0bzRBMRAwDgYDVQQDDAdDNEFfUkNBMB4XDTIw
MDcxMzEzMDYzM1oXDTQ1MDcxMzEzMDYzM1owWTEPMA0GA1UEBgwGQ2FuYWRhMRAw
DgYDVQQIDAdPbnRhcmlvMQ8wDQYDVQQHDAZPdHRhd2ExETAPBgNVBAoMCENyeXB0
bzRBMRAwDgYDVQQDDAdDNEFfUkNBMHYwEAYHKoZIzj0CAQYFK4EEACIDYgAEsUwR
2USPKBZ9pXJqiYG7tAO8MEsyQFVgK1qwdhLtV/HRSgjoQcp8Y3C2g/c8WRn87s2f
QZYTxuFB0VBLo7dCMYnQlAAxE5P0uAu7GOfhh3IDWxf9HoSnm7/G6YaJMGffo2Mw
YTAPBgNVHRMBAf8EBTADAQH/MA4GA1UdDwEB/wQEAwIBBjAfBgNVHSMEGDAWgBR9
0A5hkSyKifm5taKiJlbiitUtbTAdBgNVHQ4EFgQUfdAOYZEsion5ubWioiZW4orV
LW0wCgYIKoZIzj0EAwMDaAAwZQIxAICTsGjiIwc4BMyOf6st1eZIiGSgReQxyZXg
HjCXDRN3iqjM2Tq37YdAcDGw/X5iwwIwNFnyuELboI6A5yTfoZZuCYQrLbAnX9lH
3c3cdoK2Gu5PufZUgQptXznFvg1fz2Pb
-----END CERTIFICATE-----
```

The certificate chain goes as follows:

- **QASM_AA_ECC/QASM_AA_HSS:** EC-P384 and HSS ($L = 3, H_0 = 10, H_1 = 10, H_2 = 10, W=8, N=32, \text{Digest}=\text{SHA256}$ ) attestation private keys unique to each QASM. Both keys have their own certificate of the same name.
- **C4A_SCA:** Crypto4A's AKI v1.0 sub-CA.
- **C4A_RCA:** Crypto4A's AKI v2.0 root-CA.

## Generating Attestations

To generate an attestation, you can use the "skm" command line utility, the Keyman REST API, or the Keyman gRPC API.  Note that the remainder of this document will focus on the command line arguments for simplicity. To see the list of supported claims, run the following command:

```bash
skm attest claim --help
```

In order to generate an attestation message, specify the attestation key(s) to be used, the output file, and combine a number of claims. See the examples below for more information.

## Verifying Attestations

In order to verify attestations, you can use the "spa-attest" command line tool. It will verify the signatures on the attestation message and will return the list of claims that it contains. It's up to the verifier to assert that the claims it is expecting, given a scenario, are in fact present.

### Verifying Integrity

In order to verify the integrity of an HSM, you can assert that some or all of the following claims are present in the attestation message:

- qasm-uuid: 1.3.6.1.4.1.39901.6.1.1.0
- qasm-serial: 1.3.6.1.4.1.39901.6.1.1.1
- attestation-time: 1.3.6.1.4.1.39901.6.1.1.2
- qasm-firmware-version: 1.3.6.1.4.1.39901.6.1.1.3
- qasm-certified-production: 1.3.6.1.4.1.39901.6.1.1.4
- qasm-is-in-fips-mode: 1.3.6.1.4.1.39901.6.1.1.5
- attestation-keys-are-unique: 1.3.6.1.4.1.39901.6.1.2.0

Note that the "spa-attest" tool can automatically verify all the claims relating to the integrity of the HSM with the "--private-key-is-on-hsm" flag:

```
spa-attest verify --require private-key-is-on-hsm <attestation.pem>
```



### Matching the Key in the CSR

In order to match the key in the CSR with the private key, you can assert that some or all of the following claims are present in the attestation message and that the corresponding values match the expected values:

- key-spki: 1.3.6.1.4.1.39901.6.1.2.1
- key-fingerprint: 1.3.6.1.4.1.39901.6.1.2.2

Note that the "spa-attest" tool can automatically match the key in the CSR the "--csr" flag:

```
spa-attest verify --require private-key-for-csr-is-on-hsm --csr <csr.pem> <attestation.pem>
```



### Verifying Key Attributes

In order to verify a key's attribute, you can assert that some or all of the following claims are present in the attestation message:

- object-class: 1.3.6.1.4.1.39901.6.1.2.4
- object-type: 1.3.6.1.4.1.39901.6.1.2.5
- object-keystore: 1.3.6.1.4.1.39901.6.1.2.6
- key-is-confined: 1.3.6.1.4.1.39901.6.1.2.7
- key-is-hardware-generated: 1.3.6.1.4.1.39901.6.1.2.8
- key-never-extracted: 1.3.6.1.4.1.39901.6.1.2.9
- key-is-managed: 1.3.6.1.4.1.39901.6.1.2.10
- key-is-not-managed: 1.3.6.1.4.1.39901.6.1.2.11
- key-has-capability: 1.3.6.1.4.1.39901.6.1.2.13
- key-does-not-have-capability: 1.3.6.1.4.1.39901.6.1.2.14
- key-is-related-to-authority: 1.3.6.1.4.1.39901.6.1.2.15
- key-is-archived-by: 1.3.6.1.4.1.39901.6.1.2.16

Note that the "spa-attest" tool can automatically verify all the claims relating to the attributes of the key with the "--private-key-is-on-hsm" flag:

```
spa-attest verify --require private-key-is-on-hsm <attestation.pem>
```

## Examples

This example generates a private key and its CSR, as well as a corresponding attestation message. Then the attestation message is verified. The commands start with the "$" symbol, comments start with the "#" symbol, and response messages from the HSM start with the ">" symbol.

```
# Generate a key pair
$ skm key generate EC-P256 sign label tlsKey
> Public Key: 079004e7-4093-46fe-948c-d6333844c75d
> Private Key: ba17a9b1-3663-4f18-98da-f1bd5bdbf96a

# Generating a CSR
$ skm certificate csr ba17a9b1-3663-4f18-98da-f1bd5bdbf96a cn SomeTlsKey out someTls.csr
> Output written to someTls.csr

# Finding some keys/certificates that are helpful to generate the attestation message
$ skm find label QASM_AA_ECC class private-key
> 950b6118-3ce7-e646-4d5a-40077ab68c4b - PRIVATE_KEY     QASM_AA_ECC

$ skm find label QASM_AA_HSS class private-key
9d2a70d0-c8fd-c546-0e71-e70428f4e1d4 - PRIVATE_KEY     QASM_AA_HSS

$ skm find label QASM_AA_ECC class certificate
90b813c3-436b-e14d-20ec-1eaf0c50156f - CERTIFICATE     QASM_AA_ECC

$ skm find label QASM_AA_HSS class certificate
93d7d67c-b60a-9441-2a94-33813dda5776 - CERTIFICATE     QASM_AA_HSS

$ skm find label C4A_SCA class certificate
> 915b178f-51b2-9146-c5fc-5d5b048ef88a - CERTIFICATE     C4A_SCA

$ skm find label C4A_RCA class certificate
> 9131ef7f-96e7-b346-c731-675c0950a928 - CERTIFICATE     C4A_RCA

# Generate the attestation message. The claims being used can change
# from one use case to another, but it's generally good to add more
# claims when generating the attestation message. The verifier can
# always ignore some claims (e.g., object keystore).
$ skm attest claim --qasm-uuid --qasm-serial --qasm-firmware-version --attestation-time --qasm-certified-production --attestation-keys-are-unique --key-spki ba17a9b1-3663-4f18-98da-f1bd5bdbf96a --object-class ba17a9b1-3663-4f18-98da-f1bd5bdbf96a=private-key --object-type ba17a9b1-3663-4f18-98da-f1bd5bdbf96a=ecc --key-is-confined ba17a9b1-3663-4f18-98da-f1bd5bdbf96a --key-is-hardware-generated ba17a9b1-3663-4f18-98da-f1bd5bdbf96a --key-never-extracted ba17a9b1-3663-4f18-98da-f1bd5bdbf96a --key-has-capability ba17a9b1-3663-4f18-98da-f1bd5bdbf96a=sign --key-does-not-have-capability ba17a9b1-3663-4f18-98da-f1bd5bdbf96a=derive  --object-keystore ba17a9b1-3663-4f18-98da-f1bd5bdbf96a=independent --attesting-key 950b6118-3ce7-e646-4d5a-40077ab68c4b --certificate 90b813c3-436b-e14d-20ec-1eaf0c50156f --attach-certificate 915b178f-51b2-9146-c5fc-5d5b048ef88a --attach-certificate 9131ef7f-96e7-b346-c731-675c0950a928 --attesting-key 9d2a70d0-c8fd-c546-0e71-e70428f4e1d4 --certificate 93d7d67c-b60a-9441-2a94-33813dda5776
> -----BEGIN ATTESTATION MESSAGE-----
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
9lSBCm1fOcW+DV/PY9s=
-----END ATTESTATION MESSAGE-----


# Verifying the attestation message
$ spa-attest verify --require private-key-for-csr-is-on-hsm --csr someTls.csr attestation.pem
> ======================== SIGNATURES ========================

ECDSA_SHA384 signature by C4A000129 Assertion Authority EC, certified by C4A_SCA_MFG4, certified by C4A_RCA
HSS signature by C4A000129 Assertion Authority HSS, certified by C4A_SCA_MFG4, certified by C4A_RCA

Message signed by 2 signer(s).


========================== CLAIMS ===========================

  ✔  QASM is a certified production HSM
  ✔  Attestation key is unique to QASM
  ✔  QASM has UUID: 8817407a-d04d-e944-e4d6-eadfcdb9494d
  ✔  QASM has platform serial number: QSM000129
  ✔  Attestation was performed at 2024-06-12 14:33:07 +0000 UTC
  ✔  QASM has firmware version 4.0.2.493

ba17a9b1-3663-4f18-98da-f1bd5bdbf96a == someTls.csr
    ✔  Key has never been extracted
    ✔  Key does not have the derive capability
    ✔  Key is associated with the SPKI: 3059301306072a8648ce3d020106082a8648ce3d03010703420004c5ddf0d66dd21a0bf1a82649484769045d9c26655de9032ce8dce4d4facc176e60af9f4700f69da2f3007f6ad41100c5eacf19b4e250578a827731bb3fa897a6
    ✔  Object is of the private-key class
    ✔  Object is in the independent key store type
    ✔  Key is confined to claiming QASM
    ✔  Key is hardware generated
    ✔  Object is of the ecc type
    ✔  Key has the sign capability
    ✔  Matches the CSR in someTls.csr
```

