# QA Report
## Finding Summary

[**Low Severity**](#Low-Severity)
1. [**Relatively Small block.timestamp Cast**](#1-Relatively-Small-blocktimestamp-Cast)

[**Non-Critical**](#Non-Critical)
1. [**No License Indication**](#1-No-License-Indication)
2. [**Inconsistent Comment Initial Spacing**](#2-Inconsistent-Comment-Initial-Spacing)
3. [**Inconsistent Named Returns**](#3-Inconsistent-Named-Returns)
4. [**Spelling Mistakes**](#4-Spelling-Mistakes)
5. [**ERC20 Token Recovery**](#5-ERC20-Token-Recovery)
6. [**Reserved Variable Name now**](#6-Reserved-Variable-Name-now)
7. [**Assembly Not Heavily Documented**](#7-Assembly-Not-Heavily-Documented)
8. [**Inconsistent NatSpec Trailing Period**](#8-Inconsistent-NatSpec-Trailing-Period)
9. [**Inconsistent Trailing Period**](#9-Inconsistent-Trailing-Period)
10. [**Inconsistent Capitilization**](#10-Inconsistent-Capitilization)

[**Style Guide Violations**](#Style-Guide-Violations)
1. [**Maximum Line Length**](#1-Maximum-Line-Length)
2. [**Function Declaration Style**](#2-Function-Declaration-Style)
3. [**Order of Layout**](#3-Order-of-Layout)

# Low Severity

## 1. Relatively Small block.timestamp Cast

As of now `uint32(now)` will overflow when the `now` is greater than `type(uint32).max := 4294967295`. [`block.timestamp` returns the current seconds since unix epoch](https://docs.soliditylang.org/en/v0.8.13/units-and-global-variables.html). A unix time of `4294967295` would imply a date and time of **Sunday, 7 February 2106 06:28:15 GMT** (can be verified [here](https://www.epochconverter.com/)). In approximetly 83 years all functions that implement the call `uint32(now)` will experience protocol integrity issues.

*/contracts/dnssec-oracle/DNSSECImpl.sol*

```solidity
160: if (!RRUtils.serialNumberGte(rrset.expiration, uint32(now))) {
161: revert SignatureExpired(rrset.expiration, uint32(now));
166: if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {
167: revert SignatureNotValidYet(rrset.inception, uint32(now));
```

# Non-Critical

## 1. No License Indication

Some contracts are missing a license indication. If no license is used `SPDX-License-Identifier: UNLICENSED` should be at the top of a contract.

The following contracts are missing a license:

[BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol), [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol), [RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol), [EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol), [P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol), [ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol), [RSAVerify.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol), [SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol), [SHA256Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol), and [SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol) are missing a license.

## 2. Inconsistent Comment Initial Spacing

Some comments have an initial space after `//` or `///` while others do not. It is best for code clearity to keep a consistent style.

1. The following contracts only have initial space comments (EX. `// foo`): [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol), [RecordParser.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol), [BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol), [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol), [RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol), [EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol), [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol), [SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol), [NameEncoder.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol), and [HexUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol).
2. No contracts have only no initial space comments (EX. `//foo`).
3. The following contracts have both: [DNSClaimChecker.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol), and [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol).

## 3. Inconsistent Named Returns

Some functions use named returns and others do not. It is best for code clearity to keep a consistent style.

1. The following contracts only have named returns (EX. `returns(uint256 foo)`): [RecordParser.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol), [ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol), [SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol), and [NameEncoder.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol).
2. The following contracts only have non-named returns (EX. `returns(uint256)`): [DNSClaimChecker.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol), [OffchainDNSResolver.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol), [RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol), [P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol), [RSAVerify.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol), [SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol), and [SHA256Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol).
3. The following contracts have both: [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol), [BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol), [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol), [EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol), [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol), and [HexUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol).

## 4. Spelling Mistakes

There are some spelling mistakes throughout the codebase. Consider fixing all spelling mistakes.

*contracts/dnssec-oracle/algorithms/EllipticCurve.sol*

* The word `parameterized` is misspelled as [`parametrized`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L8).

## 5. ERC20 Token Recovery

Consider adding a recovery function for Tokens that are accidently sent to the core contracts of the protocol. 

## 6. Reserved Variable Name now

`now` is the deprecated version of `block.timestamp`. There are times in the codebase where a variable is named `now`. Consider changing the varaible name in order to lessen developer confusion.

See [DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol).

## 7. Assembly Not Heavily Documented

It is best practice to heavily comment all lines of assembly. Assembly can be seen commented in the codebase; however, there are large code blocks that have no commenting at all ([ex](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L59-L123)). Assembly documentation should be at the instruction level.

## 8. Inconsistent NatSpec Trailing Period

There is an inconsistency in the capitilization of NatSpec comments. Some NatSpec tags do not have a trailing period ([ex1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L221), [ex2](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L205), [ex3](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L381)) while others do ([ex1](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L204), [ex2](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L220)). Consider sticking to a single style.

## 9. Inconsistent Trailing Period

There is an inconsistency in the trailing period of comments. For example, [this](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L320) line has a trailing period, while [this](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L354) line does not. Consider removing all trailing periods.

## 10. Inconsistent Capitilization

There is an inconsistency in the capitalization of comments. For example, [this](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L22) line is not capitilized, while [this](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L351) line is. Consider capitilizing all comments.

# Style Guide Violations

## 1. Maximum Line Length

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

The following lines are longer than 120 characters, it is suggested to shorten these lines:

*contracts/dnssec-oracle/BytesUtils.sol*
*  [323](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L323). 

*contracts/dnssec-oracle/DNSSECImpl.sol*
*  [81](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L81), [100](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L100).

## 2. Function Declaration Style

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#function-declaration) states that "[f]or short function declarations, it is recommended for the opening brace of the function body to be kept on the same line as the function declaration". It is not clear what length is exactly meant by "short" or "long". The [maximum line length](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) of 120 characters may be an indication, and in that case any expanded function declaration under 120 characters should be on a single line.

The following functions in their respective contracts are not compliant by this standard: 

*contracts/dnsregistrar/DNSClaimChecker.sol*
* `getOwnerAddress`, `parseRR`, `parseString`. 

*contracts/dnsregistrar/OffchainDNSResolver.sol*
* `resolve`, `resolveCallback`, `parseRR`, `readTXT`, `resolveName`, `textNamehash`. 

*contracts/dnsregistrar/DNSRegistrar.sol*
* `proveAndClaim`, `supportsInterface`, `_enableNode`. 

*contracts/dnssec-oracle/BytesUtils.sol*
* `keccak`, `compare`, `equals`, `equals`, `readUint8`, `readUint16`, `readUint32`, `readBytes32`, `readBytes20`, `readBytesN`, `substring`, `base32HexDecodeWord`, `find`. 

*contracts/dnssec-oracle/RRUtils.sol*
* `nameLength`, `readName`, `labelCount`, `readSignedSet`, `rrs`, `iterateRRs`, `rdata`, `readDNSKEY`, `readDS`, `isSubdomainOf`, `compareNames`, `serialNumberGte`, `progress`. 

*contracts/dnssec-oracle/algorithms/EllipticCurve.sol*
* `toProjectivePoint`, `toAffinePoint`, `zeroProj`, `isZeroCurve`, `twiceProj`, `add`, `twice`, `multiplyPowerBase2`, `multiplyScalar`, `multipleGeneratorByScalar`. 

*contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol*
* `parseSignature`, `parseKey`. 

*contracts/dnssec-oracle/algorithms/RSAVerify.sol*
* `rsarecover`. 

*contracts/dnssec-oracle/digests/SHA1Digest.sol*
* `verify`. 

*contracts/dnssec-oracle/digests/SHA256Digest.sol*
* `verify`. 

*contracts/dnssec-oracle/DNSSECImpl.sol*
* `verifyDSHash`. 

*contracts/utils/NameEncoder.sol*
* `dnsEncodeName`. 

*contracts/utils/HexUtils.sol*
* `hexToAddress`. 

## 3. Order of Layout

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol): Modifiers positioned after Functions (Constructor).
* [BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol): State is positioned after Functions.
* [RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol): State is positioned after Functions.