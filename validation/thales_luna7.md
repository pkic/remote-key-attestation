# Thales Luna 7

The Thales Luna HSM and the DPoD Luna Cloud HSM can create [public key confirmation (PKC) packages](https://data-protection-updates.gemalto.com/2020/04/15/public-key-confirmation-meeting-ca-browser-forum-standards-with-luna-and-luna-cloud-hsms/). The PKC can be used to verify that the keypair was generated in the HSM, and can not be exported.


be accomplished using standard tools provided with both the Luna HSM and the DPoD Luna Cloud HSM.
A Luna (Cloud) HSM will issue confirmations only for private keys that were created by the HSM and that can never exist outside of the HSM. A valid confirmation is cryptographic proof that a specific key is inside the identified HSM. The confirmation is also proof that that the identified HSM is authentic

## Trust

Trust for the Luna attestations are anchored in a root certificate, which is the HSM manufacturers Root Certificate. The certificate chain is described in the documentation about [Verifying the HSM's Authenticity](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/admin_partition/confirm/confirm_hsm.htm).
 
## Generating Attestations

To generate an attestation (PKC) to send to a CA you first generate the key pair and the CSR. To generate the PKC you need to know the PKCS#11 handle of the public key. There are many ways to find this out, one way using Luna tools is using the cmu tool, available both for [Luna](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/Utilities/cmu/cmu_getpkc.htm?Highlight=getpkc) and [DPoD](https://thalesdocs.com/dpod/services/luna_cloud_hsm/extern/client_guides/Content/Utilities/cmu/cmu_getpkc.htm). 
```
cmu list -class private -label=mykeyalias
```
Where mykeyalias is the PKCS#11 label of the private key. This outputs a *handle* (number) on Luna and an *ouid* on DPoD, this is what we input to the *getpkc* command. Download the PKC with:
```
cmu getpkc -outputfile=attestation.pkc –handle=5
```
or
```
cmu getpkc -outputfile=attestation.pkc -ouid=8dac020013000002badb0800
```
The PKC attestation file is now stored as attestation.pkc, a DER encoded PKCS#7 (CMS) file.

## Verifying Attestations

Verifying attestations can be done with cmu, for [Luna](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/Utilities/cmu/cmu_verifypkc.htm) or [DPoD](https://thalesdocs.com/dpod/services/luna_cloud_hsm/extern/client_guides/Content/Utilities/cmu/cmu_verifypkc.htm).

For example
```
cmu verifypkc -inputFile=attestation.pkc
```

The downside is that the cmu command requires access to the Luna HSM where the key was generated. So this is not a good solution. The PKC package however is a standard PKCS#7 file that can be parsed with tools like openssl.
```
openssl pkcs7 -in attestation.pkc -inform DER -print
```

## Examples
```
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

