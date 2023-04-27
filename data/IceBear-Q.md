
## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [N-1](#N-1) | Use of block.timestamp | 1 
| [N-2](#N-2) | Functions not used internally could be marked external  | 5 |
| [N-3](#N-3) | Adding a return statement when the function defines a named return variable, is redundant   | 2 |
| [N-4](#N-4) | File does not contain an SPDX  | 10 |
| [N-5](#N-5) | Assembly Codes Specific – Should Have Comments  | 15 |

### [N-1] Use of block.timestamp
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
#### Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers — i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

Instances where block.timestamp is used:

*Find (1) instance(s) in contracts*:
```solidity
File: dnssec-oracle/DNSSECImpl.sol

96:         return verifyRRSet(input, block.timestamp);

```
[dnssec-oracle/DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)


### [N-2] Functions not used internally could be marked external 

*Find (5) instance(s) in contracts*:
```solidity
File: dnsregistrar/DNSRegistrar.sol

80:     function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {

90:     function proveAndClaim(

101:     function proveAndClaimWithResolver(

```
[dnsregistrar/DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)

```solidity
File: dnssec-oracle/DNSSECImpl.sol

64:     function setAlgorithm(uint8 id, Algorithm algo) public owner_only {

75:     function setDigest(uint8 id, Digest digest) public owner_only {

```
[dnssec-oracle/DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)

### [N-3] Adding a return statement when the function defines a named return variable, is redundant

similar finding:
https://code4rena.com/reports/2022-04-phuture/#n-01-adding-a-return-statement-when-the-function-defines-a-named-return-variable-is-redundant
*Find (2) instance(s) in contracts*:
```solidity
File: dnssec-oracle/DNSSECImpl.sol

127:       return (proof, inception);

173:       return rrset;

```
[dnssec-oracle/DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)

### [N-4] File does not contain an SPDX
similar finding:
https://code4rena.com/reports/2022-05-cudos/#14-file-does-not-contain-an-spdx-identifier

*Find (11) instance(s) in contracts*:
```solidity
File: dnssec-oracle/BytesUtils.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol)

```solidity
File: dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)

```solidity
File: dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

```
[dnssec-oracle/SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol)

```solidity
File: dnssec-oracle/algorithms/EllipticCurve.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/algorithms/EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol)

```solidity
File: dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/algorithms/ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol)

```solidity
File: dnssec-oracle/algorithms/P256SHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/algorithms/P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol)

```solidity
File: dnssec-oracle/algorithms/RSASHA1Algorithm.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/algorithms/RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol)

```solidity
File: dnssec-oracle/algorithms/RSASHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/algorithms/RSASHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol)

```solidity
File: dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/algorithms/RSAVerify.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol)

```solidity
File: dnssec-oracle/digests/SHA1Digest.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/digests/SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol)

```solidity
File: dnssec-oracle/digests/SHA256Digest.sol

1: pragma solidity ^0.8.4;

```
[dnssec-oracle/digests/SHA256Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol)

### [N-5] Assembly Codes Specific – Should Have Comments
Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

*Find (15) instance(s) in contracts*:
```solidity
File: dnssec-oracle/BytesUtils.sol

19:         assembly {

73:         assembly {

80:             assembly {

197:         assembly {

213:         assembly {

229:         assembly {

245:         assembly {

267:         assembly {

276:             assembly {

286:             assembly {

311:         assembly {

```
[dnssec-oracle/BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol)

```solidity
File: dnssec-oracle/RRUtils.sol

386:                 assembly {

```
[dnssec-oracle/RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)

```solidity
File: dnssec-oracle/SHA1.sol

7:         assembly {

```
[dnssec-oracle/SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol)

```solidity
File: dnssec-oracle/algorithms/ModexpPrecompile.sol

23:         assembly {

```
[dnssec-oracle/algorithms/ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol)

```solidity
File: utils/HexUtils.sol

17:         assembly {

```
[utils/HexUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol)

## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Initializers could be front-run  | 2 |
| [L-2](#L-2) | Yul call return value not checked  | 1 |

### [L-1] Initializers could be front-run 
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Find (2) instance(s) in contracts*:
```solidity
File: dnsregistrar/DNSClaimChecker.sol

25:         buf.init(name.length + 5);

```
[dnsregistrar/DNSClaimChecker.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol)

```solidity
File: dnssec-oracle/DNSSECImpl.sol

398:             buf.init(keyname.length + keyrdata.length);

```
[dnssec-oracle/DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)


### [L-2]Yul call return value not checked
The Yul call return value on function modexp is not checked.
similar finding:
https://github.com/code-423n4/2022-11-non-fungible-findings/issues/90
```solidity
File: dnssec-oracle/algorithms/ModexpPrecompile.sol

23:  assembly {
24:            success := staticcall(
25:                gas(),
26:                5,
27:                add(input, 32),
28:                mload(input),
28:                add(output, 32),
30:                mload(modulus)
31:            )
32:        }

```
[dnssec-oracle/algorithms/ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol)
#### Recommended Mitigation Steps
Add checks to ensure function calls succeed or fail. When the return value is false, strictly revert the transaction.