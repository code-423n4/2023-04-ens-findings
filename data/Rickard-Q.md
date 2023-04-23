# [N-01] Files do not contain a `SPDX-License-Identifier`
## Lines of code
[EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol), [ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol), [P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol), [RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol), [RSASHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol), [RSAVerify.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol), [SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol), [SHA256Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol), [BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol), [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol), [SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol)
## Vulnerability details
### Proof of concept
````solidity
1:    pragma solidity
````
## Recommended mitigation steps
Add the `SPDX-License-Identifier` before `pragma` statement.