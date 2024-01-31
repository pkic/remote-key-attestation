# GCP CloudHSM

Google’s [Cloud HSM](https://cloud.google.com/kms/docs/hsm) service uses devices manufactured by Marvell (formerly Cavium), which can produce signed attestation statements for cryptographic keys.

# Table of contents
1. [Trust](#trust)
2. [Generating Attestations](#generating-attestations)
3. [Verifying Attestations](#verifying_attestations)
    1. [Verifying Integrity](#verifying-integrity)
    2. [Matching the Key in the CSR](#matching-the-key-in-the-csr)
    3. [Verifying Key Attributes](#verifying-key-attributes)
4. [Examples](#examples)

## Trust

Trust for the GCP CloudHSM attestations are anchored in a root certificate, which is the HSM manufacturers Root Certificate, in this case Marvell.
As of January 2024 you can download a zip file with the certificate chain from [Marvell](https://www.marvell.com/content/dam/marvell/en/public-collateral/security-solutions/liquid_security_certificate.zip). This is a self signed certificate with Subject/Issuer DN `C=US, ST=California, L=San Jose, O="Cavium, Inc.", OU=LiquidSecurity, CN=localca.liquidsecurity.cavium.com`

For reference, for Trust ensure a trusted download from Marvell.
**Note** that this certificate expires Nov 2025, and would be updated at some point.
```
-----BEGIN CERTIFICATE-----
MIIDoDCCAogCCQDA6q30NN7cFzANBgkqhkiG9w0BAQsFADCBkTELMAkGA1UEBhMC
VVMxEzARBgNVBAgMCkNhbGlmb3JuaWExETAPBgNVBAcMCFNhbiBKb3NlMRUwEwYD
VQQKDAxDYXZpdW0sIEluYy4xFzAVBgNVBAsMDkxpcXVpZFNlY3VyaXR5MSowKAYD
VQQDDCFsb2NhbGNhLmxpcXVpZHNlY3VyaXR5LmNhdml1bS5jb20wHhcNMTUxMTE5
MTM1NTI1WhcNMjUxMTE2MTM1NTI1WjCBkTELMAkGA1UEBhMCVVMxEzARBgNVBAgM
CkNhbGlmb3JuaWExETAPBgNVBAcMCFNhbiBKb3NlMRUwEwYDVQQKDAxDYXZpdW0s
IEluYy4xFzAVBgNVBAsMDkxpcXVpZFNlY3VyaXR5MSowKAYDVQQDDCFsb2NhbGNh
LmxpcXVpZHNlY3VyaXR5LmNhdml1bS5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IB
DwAwggEKAoIBAQDckvqQM4cvZjdyqOLGMTjKJwvfxJOhVqw6pojgUMz10VU7z3Ct
JrwHcESwEDUxUkMxzof55kForURLaVVCjedYauEisnZwwSWkAemp9GREm8iX6BXt
oZ8VDWoO2H0AJiHCM62qJeZVXhm8A/zWG0PyLrCINH0yz9ah6BcwdsZGLvQvkpUN
JhwVMrb9nI9BlRmTWhoot1YSTf7jfibEkc/pN+0Ez30RFaL3MhyIaNJS22+10tny
4sOUTsPEtXKah5mPlHpnrGcB18z5Yxgr0vDNYx+FCPGo95XGrq9NYfNMlwsSeFSr
8D1VQ7HZmipeTB1hQTUQw/K/Rmtw5NiljkYTAgMBAAEwDQYJKoZIhvcNAQELBQAD
ggEBAJjqbFaa3FOXEXcXPX2lCHdcyl8TwOR9f3Rq87MEfb3oeK9FarNkUCdvuGs3
OkAWoFib/9l2F7ZgaNlJqVrwBaOvYuGguQoUpDybqttYUJVLcu9vA9eZA+UCJdhd
P7fCyGMO+G96cnG3GTS1/SrIDU+YCnVElQ0P/73/de+ImoeMkwcqiUi2lsf3vGGR
YXMt/DxUwjXwjIpWCs+37cwbNHAv0VKDOR/jmNf5EZf+sy4x2rJZ1NS6eDZ9RBug
CLaN6ntybV4YlE7jDI9XIOm/tPJULZGLpLolngWVB6qtzn1RjBw1HIqpoXg+9s1g
pLFFinSrEL1fkQR0YZQrJckktPs=
-----END CERTIFICATE-----
```
 
## Generating Attestations

To generate an attestation to send to a CA you generate a key, a CSR and an attestation statement. The Google documentation mentions multiple ways to verify attestation bundles, but since our aim is for different entities generate and verify the attestation the process for [Verifying the attestation manually](https://cloud.google.com/kms/docs/attest-key#verify_chains) is most suitable.

Generating the attestation is done by the entity generating the key and the CSR, in their Google Cloud console *Key Management* page. The result will be a zip file containing the attestation and certificate chains. This is the zip file that will be sent to the CA.

## Verifying Attestations

Validating attestations for code signing (and other) purposes consist of three steps:
1. Verifying integrity of the attestation bundle.
2. Matching the public key in the CSR with the attestated key pair in the attestation bundle.
3. Verifying key attributes in the attestation, that the key pair was generated in the hardware security module and that the private key is not exportable.

As with generating attestations, Google documents multiple ways to verify attestations. Again the process for [Verifying the attestation manually](https://cloud.google.com/kms/docs/attest-key#verify_chains) is suitable for the entity that recieves the attestation zip file generated above.

Verification is done with the [verify_attestation_chains.py](https://github.com/GoogleCloudPlatform/python-docs-samples/tree/main/kms/attestations) script.
Actually matching the public key received in the CSR with the attestation is a complex process with manual steps. It is described in the Google documentation [Parsing the attestation's values](https://cloud.google.com/kms/docs/attest-key#parse_attestation), and Marvell's documentation for [Parsing an Attestation](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/software-key-attestation.html#ParseAttestation) and [Verifying a Public Key](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/software-key-attestation.html#VerifyPubKey),

### Verifying Integrity
TODO: rovide steps

### Matching the Key in the CSR
TODO: provide steps

### Verifying Key Attributes
TODO: provide steps

## Examples
TODO: Full output examples

