# Thales Luna 7

The Thales Luna HSM and the DPoD Luna Cloud HSM can create [public key confirmation (PKC) packages](https://data-protection-updates.gemalto.com/2020/04/15/public-key-confirmation-meeting-ca-browser-forum-standards-with-luna-and-luna-cloud-hsms/). The PKC can be used to verify that the keypair was generated in the HSM, and can not be exported.


be accomplished using standard tools provided with both the Luna HSM and the DPoD Luna Cloud HSM.
A Luna (Cloud) HSM will issue confirmations only for private keys that were created by the HSM and that can never exist outside of the HSM. A valid confirmation is cryptographic proof that a specific key is inside the identified HSM. The confirmation is also proof that that the identified HSM is authentic

## Trust

Trust for the Luna attestations are anchored in a root certificate, which is the HSM manufacturers Root Certificate. The certificate chain is described in the documentation about [Verifying the HSM's Authenticity](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/admin_partition/confirm/confirm_hsm.htm).
 
## Generating Attestations

To generate an attestation (PKC) to send to a CA you first generate the key pair and the CSR. To generate the PKC you need to know the PKCS#11 handle of the public key. There are many ways to find this out, one way using Luna tools is using the cmu tool, available both for [Luna](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/Utilities/cmu/cmu_getpkc.htm?Highlight=getpkc) and [DPoD](https://thalesdocs.com/dpod/services/luna_cloud_hsm/extern/client_guides/Content/Utilities/cmu/cmu_getpkc.htm). 
```
cmu list -class public -label=mykeyalias
```
Where mykeyalias is the PKCS#11 label of the public key. The handle is a plain integer number. Download the PKC with:
```
cmu getpkc –handle=handleNr -outputfile=attestation.pkc -verify
```
The PKC attestation file is now stored as attestation.pkc, a DER encoded PKCS#7 (CMS) file.

## Verifying Attestations

Verifying attestations are also done with cmu, for [Luna](https://thalesdocs.com/gphsm/luna/7/docs/network/Content/Utilities/cmu/cmu_verifypkc.htm) or [DPoD](https://thalesdocs.com/dpod/services/luna_cloud_hsm/extern/client_guides/Content/Utilities/cmu/cmu_verifypkc.htm).

For example
```
cmu verifypkc -inputFile=attestation.pkc
```

## Examples

