# GCP CloudHSM

Google’s [Cloud HSM](https://cloud.google.com/kms/docs/hsm) service uses devices manufactured by Marvell (formerly Cavium), which can produce signed attestation statements for cryptographic keys.

## Trust

Trust for the GCP CloudHSM attestations are anchored in a root certificate, which is the HSM manufacturers Root Certificate, in this case Marvell.
As of January 2024 you can download a zip file with the certificate chain from [Marvell](https://www.marvell.com/content/dam/marvell/en/public-collateral/security-solutions/liquid_security_certificate.zip). This is a self signed certificate with Subject/Issuer DN `C=US, ST=California, L=San Jose, O="Cavium, Inc.", OU=LiquidSecurity, CN=localca.liquidsecurity.cavium.com`
 
## Generating Attestations

To generate an attestation to send to a CA you generate a key, a CSR and an attestation statement. The Google documentation mentions multiple ways to verify attestation bundles, but since our aim is for different entities generate and verify the attestation the process for [Verifying the attestation manually](https://cloud.google.com/kms/docs/attest-key#verify_chains) is most suitable.

Generating the attestation is done by the entity generating the key and the CSR, in their Google Cloud console *Key Management* page. The result will be a zip file containing the attestation and certificate chains. This is the zip file that will be sent to the CA.

## Verifying Attestations

As with generating attestations, Google documents multiple ways to verify attestations. Again the process for [Verifying the attestation manually](https://cloud.google.com/kms/docs/attest-key#verify_chains) is suitable for the entity that recieves the attestation zip file generated above.

Verification is done with the [verify_attestation_chains.py](https://github.com/GoogleCloudPlatform/python-docs-samples/tree/main/kms/attestations) script.
Actually matching the public key received in the CSR with the attestation is a complex process with manual steps. It is described in the Google documentation [Parsing the attestation's values](https://cloud.google.com/kms/docs/attest-key#parse_attestation), and Marvell's documentation for [Parsing an Attestation](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/software-key-attestation.html#ParseAttestation) and [Verifying a Public Key](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/software-key-attestation.html#VerifyPubKey),

## Examples

