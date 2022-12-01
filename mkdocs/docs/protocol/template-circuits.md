# Template Circuits

The template circuits are not are not used independently inside Iden3 protocols but rather as a building blocks inside the main circuits. 

Note: not all the templates are described here but only a few of them. Check [Iden3 Circuits](https://github.com/iden3/circuits) and [Iden3 Circomlib](https://github.com/iden3/circomlib) to see all the templates.

## idOwnershipBySignature

- [**Github**](https://github.com/iden3/circuits/blob/master/circuits/lib/idOwnershipBySignature.circom)

#### Inputs

| Input                          | Description              | 
| -----------                    | -----------          | 
| userState                      | Identity State of the prover                | 
| userClaimsTreeRoot             | Prover's Claims Tree Root                 | 
| userAuthClaimMtp[nLevels]    | Merkle Tree Proof of Auth Claim inside Prover's Claims tree           | 
| userAuthClaim[8]               | Prover's Auth Claim                 | 
| userRevTreeRoot                | Prover's Revocation Tree Root                | 
| userAuthClaimNonRevMtp[nLevels]| Merkle Tree Proof of non membership of Auth Claim inside Prover's Revocation Tree                 | 
| userAuthClaimNonRevMtpNoAux    | Flag that indicates whether to check the auxiliary Node                 | 
| userAuthClaimNonRevMtpAuxHi    | Auxiliary Node Index                 | 
| userAuthClaimNonRevMtpAuxHv    | Auxiliary Node Value                | 
| userRootsTreeRoot              | Prover's Roots Tree Root                 | 
| challenge                      | Message to be signed by the prover              | 
| challengeSignatureR8x          | Signature of the challenge (Rx point)           | 
| challengeSignatureR8y          | Signature of the challenge (Ry point)           | 
| challengeSignatureS            | Signature of the challenge (S point)           | 

#### Scope

- Verifies that the `signature` was performed by the key contained in the `authClaim` and performs further verification on the auth claim using the [`VerifyAuthClaimAndSignature(nLevels)`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/treeUtils.circom#L136) template
- The `state` generated by hashing the three roots should match the expected state from the blockchain (or be the genesis state) using the [`checkIdenStateMatchesRoots()`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/treeUtils.circom#L72) template

## VerifyAuthClaimAndSignature

- [**Github**](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/treeUtils.circom#L136)

#### Inputs

| Input                          | Description              | 
| -----------                    | -----------          | 
| claimsTreeRoot                      | Prover's Claims Tree Root                | 
| authClaimMtp[nLevels]    | Merkle Tree Proof of Auth Claim inside Prover's Claims tree           | 
| authClaim[8]               | Prover's Auth Claim                 | 
| revTreeRoot                | Prover's Revocation Tree Root                | 
| userAuthClaimNonRevMtp[nLevels]| Merkle Tree Proof of non membership of Auth Claim inside Prover's Revocation Tree                 | 
| userAuthClaimNonRevMtpNoAux    | Flag that indicates whether to check the auxiliary Node                 | 
| userAuthClaimNonRevMtpAuxHi    | Auxiliary Node Index                | 
| userAuthClaimNonRevMtpAuxHv    | Auxiliary Node Value                | 
| challenge                      | Message to be signed by the prover              | 
| challengeSignatureR8x          | Signature of the challenge (Rx point)           | 
| challengeSignatureR8y          | Signature of the challenge (Ry point)           | 
| challengeSignatureS            | Signature of the challenge (S point)     

#### Scope

- The schema hash of the claim must match the expected AUTH_SCHEMA_HASH (hardcoded inside the circuit). Verification performed using the [`verifyCredentialSchema()`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/claimUtils.circom#L151) template
- The authClaim exists inside the claims tree root by using the [`checkClaimExists()`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/treeUtils.circom#L27) template
- The authClaim hasn't been revoked, namely verifies that the authClaim is not included inside the revocation tree root, using the [`checkClaimNotRevoked()`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/treeUtils.circom#L47) template
- Verifies that the signature on the challenge has been performed by the publickey contained in the authClaim using the [`checkDataSignatureWithPubKeyInClaim()`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/claimUtils.circom#L187) template
