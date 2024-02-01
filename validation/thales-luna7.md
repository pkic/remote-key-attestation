# Thales Luna 7

The Thales Luna HSM and the DPoD Luna Cloud HSM can create [public key confirmation (PKC) packages](https://data-protection-updates.gemalto.com/2020/04/15/public-key-confirmation-meeting-ca-browser-forum-standards-with-luna-and-luna-cloud-hsms/). The PKC can be used to verify that the keypair was generated in the HSM, and can not be exported.

# Table of contents
1. [Trust](#trust)
2. [Generating Attestations](#generating-attestations)
3. [Verifying Attestations](#verifying_attestations)
    1. [Verifying Integrity](#verifying-integrity)
    2. [Matching the Key in the CSR](#matching-the-key-in-the-csr)
    3. [Verifying Key Attributes](#verifying-key-attributes)
4. [Examples](#examples)

## Trust

Trust for the Luna attestations are anchored in a root certificate, which is the HSM manufacturers Root Certificate. The certificate chain is described in the documentation about [Verifying the HSM's Authenticity](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/admin_partition/confirm/confirm_hsm.htm).

For reference, for Trust ensure a trusted download from Thales.
```
-----BEGIN CERTIFICATE-----
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
-----END CERTIFICATE-----
```

## Generating Attestations

To generate an attestation (PKC) to send to a CA you first generate the key pair and the CSR. To generate the PKC you need to know the PKCS#11 handle of the public key. There are many ways to find this out, one way using Luna tools is using the cmu tool, available both for [Luna](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/Utilities/cmu/cmu_getpkc.htm?Highlight=getpkc) and [DPoD](https://thalesdocs.com/dpod/services/luna_cloud_hsm/extern/client_guides/Content/Utilities/cmu/cmu_getpkc.htm). 
```sh
cmu list -class private -label=mykeyalias
```
Where mykeyalias is the PKCS#11 label of the private key. This outputs a *handle* (number) on Luna and an *ouid* on DPoD, this is what we input to the *getpkc* command. Download the PKC with:
```sh
cmu getpkc -outputfile=attestation.pkc –handle=5
```
or
```sh
cmu getpkc -outputfile=attestation.pkc -ouid=8dac020013000002badb0800
```
The PKC attestation file is now stored as attestation.pkc, a DER encoded PKCS#7 (CMS) file.

## Verifying Attestations

Validating attestations for code signing (and other) purposes consist of three steps:
1. Verifying integrity of the attestation bundle.
2. Matching the public key in the CSR with the attestated key pair in the attestation bundle.
3. Verifying key attributes in the attestation, that the key pair was generated in the hardware security module and that the private key is not exportable.

Verifying attestations can be done with cmu, for [Luna](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/Utilities/cmu/cmu_verifypkc.htm) or [DPoD](https://thalesdocs.com/dpod/services/luna_cloud_hsm/extern/client_guides/Content/Utilities/cmu/cmu_verifypkc.htm).

For example
```sh
cmu verifypkc -inputFile=attestation.pkc
```

The downside is that the cmu command requires access to the Luna HSM where the key was generated. So this is not a good solution. The PKC package however is a standard PKCS#7 file that can be parsed with tools like openssl.
```
openssl pkcs7 -in attestation.pkc -inform DER -print
```

### Verifying Integrity
TODO: rovide steps

### Matching the Key in the CSR
TODO: provide steps

### Verifying Key Attributes
TODO: provide steps

## Examples
```sh
> ./cmu list -class private -label=priv-rsa_1
Certificate Management Utility (64-bit) v10.5.0-470. Copyright (c) 2022 SafeNet. All rights reserved.

Please enter password for token in slot 3 : ****************

ouid=8dac020013000002badb0800	label=priv-rsa_1

> ./cmu getpkc -outputfile=attestation.pkc -ouid=8dac020013000002badb0800
Certificate Management Utility (64-bit) v10.5.0-470. Copyright (c) 2022 SafeNet. All rights reserved.

Please enter password for token in slot 3 : ****************

./cmu verifypkc -inputfile=attestation.pkc 
Certificate Management Utility (64-bit) v10.5.0-470. Copyright (c) 2022 SafeNet. All rights reserved.

Please enter password for token in slot 3 : ****************

> openssl pkcs7 -in attestation.pkc -inform DER -print
PKCS7: 
  type: pkcs7-signedData (1.2.840.113549.1.7.2)
  d.sign: 
    version: 1
    md_algs:
      <EMPTY>
...

> openssl pkcs7 -in attestation.pkc -inform DER -print_certs -out certs.pem

> openssl smime -verify -inform DER -in attestation.pkc -certfile certs.pem -nointern -noverify -nodetach
```
TODO: verification fails in example, find the right commands

