1: Using shah1 is vulnerable in RSHAH1Algorithm

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

RSASHA1 is a cryptographic algorithm that uses the RSA algorithm for digital signature generation and verification, and SHA-1 as the hash function to create the message digest. The use of RSASHA1 in general is considered vulnerable because SHA-1, the hash function used to create the message digest, has known weaknesses and is no longer recommended for cryptographic purposes. The use of SHA-1 has been deprecated by NIST (National Institute of Standards and Technology) since 2011, and it has been completely banned by some organizations, including Google and Microsoft. Therefore, using the RSASHA1 algorithm in DNSSEC, or in any other context, is not recommended due to the known weaknesses of SHA-1. It is recommended to use stronger and more secure hash functions, such as SHA-256 or SHA-3, for digital signatures. The main vulnerability of SHA-1 is its susceptibility to collision attacks. A collision attack is an attack that involves finding two different inputs that produce the same hash value. In the case of SHA-1, it is possible to find two different inputs that produce the same hash value, which can be exploited to create fraudulent digital signatures, certificates, or other security-related data.
In 2005, an attack on SHA-1 was demonstrated that could find collisions with a complexity of 2^69 operations, which was still considered infeasible at the time. However, in 2017, a team of researchers were able to break SHA-1 using a collision attack with a complexity of 2^63 operations, which is within the range of modern computing power.
As a result of these known vulnerabilities, SHA-1 is no longer considered a secure cryptographic hash function and has been deprecated by NIST (National Institute of Standards and Technology) since 2011. It is recommended to use stronger and more secure hash functions, such as SHA-256 or SHA-3, for digital signatures and other security-related applications.

2: Signature malleability

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

Signature malleability is a weakness in some signature schemes where an attacker can modify a valid signature and produce another valid signature for the same message. In the context of this code, an attacker could potentially modify a valid signature and create a new signature that still validates against the same public key and message hash. This could lead to unexpected behavior in the smart contract, as the modified signature could be used to authorize transactions or perform other actions that the original signer did not intend. One common method of addressing signature malleability is to use a normalization procedure that maps each signature to a unique value. For example, in the ECDSA signature scheme, the r value of the signature is normalized by taking the minimum between r and the order of the curve's generator point. This ensures that each signature corresponds to a unique value of r, which helps prevent malleability.









