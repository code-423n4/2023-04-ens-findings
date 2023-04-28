| | issue |
| ----------- | ----------- |
| 1 | [not using the named return variables when a function returns, wastes deployment gas](#1-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 2 | [can make the variable outside the loop to save gas](#2-can-make-the-variable-outside-the-loop-to-save-gas) |
| 3 | [change sorting to save gas](#3-change-sorting-to-save-gas) |
| 4 | [use custom errors rather than revert()/require() strings to save deployment gas](#4-use-custom-errors-rather-than-revertrequire-strings-to-save-deployment-gas) |
| 5 | [internal or private functions not called by the contract or inherited ones should be removed to save deployment gas](#5-internal-or-private-functions-not-called-by-the-contract-or-inherited-ones-should-be-removed-to-save-deployment-gas) |
| 6 | [ Ternary over if ... else](#6-ternary-over-if--else) |
| 7 | [should use arguments instead of state variable](#7-should-use-arguments-instead-of-state-variable) |
| 8 | [`constant` is defined but not used anywhere inside the code](#8-constant-is-defined-but-not-used-anywhere-inside-the-code) |
| 9 | [Use assembly to check for address(0)](#9-use-assembly-to-check-for-address0) |
| 10 | [dont use a function to get the length value inside to for statement just cache it before the loop](#10-dont-use-a-function-to-get-the-length-value-inside-to-for-statement-just-cache-it-before-the-loop) |
| 11 | [Non-strict inequalities are cheaper than strict ones](#11-non-strict-inequalities-are-cheaper-than-strict-ones) |



## 1. not using the named return variables when a function returns, wastes deployment gas

no need to name the variable inside returns. this wastes gas

- [DNSRegistrar.sol#L166](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L166)
- [DNSRegistrar.sol#L177](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L177)

- [BytesUtils.sol#L182](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L182)

- [RRUtils.sol#L44](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L44)

- [NameEncoder.sol#L11](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L11)

- [EllipticCurve.sol#L109](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L109)
- [EllipticCurve.sol#L117](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L117)
- [EllipticCurve.sol#L127](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L127)
- [EllipticCurve.sol#L339](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L339)

- [DNSSECImpl.sol#L94](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L94)
- [DNSSECImpl.sol#L115](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L115)
- [DNSSECImpl.sol#L144](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L144)


## 2. can make the variable outside the loop to save gas

make the variable outside the loop and only give the value to variable inside

`proofName` and `keyrdata` and `dnskey`
- [DNSSECImpl.sol#L261-L267](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L261-L267)

`keyrdata` and `dnskey`
- [DNSSECImpl.sol#L345-346](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L345-346)

`proofName` and `ds` and `buf`
- [DNSSECImpl.sol#L381-L397](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L381-L397)

`found` and `addr`
- [DNSClaimChecker.sol#L35-L36](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L35-L36)

`rrname`
- [OffchainDNSResolver.sol#L85](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L85)

`dnsresolver` and `context`
- [OffchainDNSResolver.sol#L95](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L95)

`char`
- [BytesUtils.sol#L342](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L342)

`word` and `unused`
- [RRUtils.sol#L384-L390](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L384-L390)


## 3. change sorting to save gas

There is a chance that the first part will be true so the next parts don’t need to be checked, so it is better to use the part that is cheaper first instead.

bring the first part to last because it uses a function call.
- [OffchainDNSResolver.sol#L87-L89](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L87-L89)


## 4. use custom errors rather than revert()/require() strings to save deployment gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

https://blog.soliditylang.org/2021/04/21/custom-errors/

- [RRUtils.sol#L381](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L381)

- [SHA1Digest.sol#L17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L17)

- [SHA256Digest.sol#L16](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L16)

- [P256SHA256Algorithm.sol#L33](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L33)
- [P256SHA256Algorithm.sol#L40](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L40)


## 5. internal or private functions not called by the contract or inherited ones should be removed to save deployment gas

if the functions are required by the interface, the contract should inherit from that interface and use override keyword

- [RecordParser.sol#L14](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L14)

- [BytesUtils.sol#L332](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L332)


## 6. Ternary over if ... else

Using ternary operator instead of the if else statement saves gas.

- [OffchainDNSResolver.sol#L123-L127](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L123-L127)
- [OffchainDNSResolver.sol#L216-L220](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L216-L220)

- [BytesUtils.sol#L87-L91](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L87-L91)

- [EllipticCurve.sol#L234-L237](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L237)


## 7. should use arguments instead of state variable

This will save gas because it gets rid of a storage read and instead uses a argument that we already have which gives the same result

can use `_suffixes` here instead of the state variable used. this will reduce one SLOAD
- [DNSRegistrar.sol#L66](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L66)
- [DNSRegistrar.sol#L82](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82)


## 8. `constant` is defined but not used anywhere inside the code

consider removing them or make them into comments if there is a reason.

- [DNSClaimChecker.sol#L16](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16)
- [DNSClaimChecker.sol#L17](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L17)


## 9. Use assembly to check for address(0)

saves 6 gas per instance

- [OffchainDNSResolver.sol#L102](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L102)
- [OffchainDNSResolver.sol#L197](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197)

- [DNSRegistrar.sol#L115](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L115)
- [DNSRegistrar.sol#L116](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L116)
- [DNSRegistrar.sol#L187](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L187)

- [DNSSECImpl.sol#L317](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L317)
- [DNSSECImpl.sol#L420](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420)


## 10. dont use a function to get the length value inside to for statement just cache it before the loop

- [DNSClaimChecker.sol#L31](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L31)

- [OffchainDNSResolver.sol#L81](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L81)

- [DNSSECImpl.sol#L188](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L188)
- [DNSSECImpl.sol#L260](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L260)
- [DNSSECImpl.sol#L338](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L338)
- [DNSSECImpl.sol#L380](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L380)


## 11. Non-strict inequalities are cheaper than strict ones

In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).
consider replacing >= with the strict counterpart > and add `- 1` to the second side

- [BytesUtils.sol#L18](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L18)
- [BytesUtils.sol#L87](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L87)
- [BytesUtils.sol#L275](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L275)
- [BytesUtils.sol#L343](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L343)
- [BytesUtils.sol#L196](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L196)
- [BytesUtils.sol#L212](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L212)
- [BytesUtils.sol#L228](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L228)
- [BytesUtils.sol#L244](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L244)
- [BytesUtils.sol#L265](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L265)
- [BytesUtils.sol#L266](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L266)
- [BytesUtils.sol#L305](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L305)
- [BytesUtils.sol#L337](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L337)
- [BytesUtils.sol#L345](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L345)

- [RRUtils.sol#L151](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L151)
- [RRUtils.sol#L160](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L160)
- [RRUtils.sol#L337](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L337)
- [RRUtils.sol#L381](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L381)

- [NameEncoder.sol#L25](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L25)

- [EllipticCurve.sol#L392](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L392)
