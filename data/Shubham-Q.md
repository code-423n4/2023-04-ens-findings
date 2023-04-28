## Low Risk Issues

| |Issue|Instances|
|-|:-|:-:|
| [L-01](#L-01) | Update codes to avoid Compile Errors |  |
| [L-02](#L-02) | In the constructor, there is no return of incorrect address identification | 8 |
| [L-03](#L-03) | Use fixed length `bytes1,..,bytes32` variable type rather than default `bytes` |  | 


## Non-Critical Issues

| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Using while for unbounded loops isn’t recommended | 9 |
| [NC-2](#NC-2) | Events are missing `indexed` field | 3 |
| [NC-3](#NC-3) | Inconsistent spacing in comments | 3 |
| [NC-4](#NC-4) | Unused variables | 2 |
| [NC-5](#NC-5) | Assembly Codes Specific – Should Have Comments | 12 |



## [L-01] Update codes to avoid Compile Errors

*There are several instances of this issue.*
https://docs.soliditylang.org/en/develop/security-considerations.html#take-warnings-seriously

```solidity
Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> @ensdomains/solsha1/contracts/SHA1.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnsregistrar/PublicSuffixList.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnsregistrar/SimplePublicSuffixList.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnsregistrar/TLDPublicSuffixList.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnsregistrar/mocks/DummyDnsRegistrarDNSSEC.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/BytesUtils.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/Owned.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/RRUtils.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/SHA1.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/Algorithm.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/DummyAlgorithm.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/EllipticCurve.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/algorithms/RSAVerify.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/digests/Digest.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/digests/DummyDigest.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/digests/SHA1Digest.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/dnssec-oracle/digests/SHA256Digest.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/ethregistrar/IBaseRegistrar.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/ethregistrar/mocks/DummyProxyRegistry.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/registry/ENS.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/registry/ENSRegistry.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/registry/ENSRegistryWithFallback.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/registry/FIFSRegistrar.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/registry/TestRegistrar.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/resolvers/OwnedResolver.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/resolvers/mocks/DummyNameWrapper.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/reverseRegistrar/IReverseRegistrar.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/reverseRegistrar/ReverseRegistrar.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/root/Controllable.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/root/Ownable.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/root/Root.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> test/dnssec-oracle/TestBytesUtils.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> test/dnssec-oracle/TestRRUtils.sol


Warning: Source file does not specify required compiler version! Consider adding "pragma solidity ^0.8.17;"
--> contracts/ethregistrar/IBaseRegistrar.sol


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:20:9:
   |
20 |         address owner = records[node].owner;
   |         ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:143:5:
    |
143 |     function owner(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:41:9:
   |
41 |         address owner,
   |         ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:143:5:
    |
143 |     function owner(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:42:9:
   |
42 |         address resolver,
   |         ^^^^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:159:5:
    |
159 |     function resolver(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:43:9:
   |
43 |         uint64 ttl
   |         ^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:170:5:
    |
170 |     function ttl(bytes32 node) public view virtual override returns (uint64) {
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:60:9:
   |
60 |         address owner,
   |         ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:143:5:
    |
143 |     function owner(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:61:9:
   |
61 |         address resolver,
   |         ^^^^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:159:5:
    |
159 |     function resolver(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:62:9:
   |
62 |         uint64 ttl
   |         ^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:170:5:
    |
170 |     function ttl(bytes32 node) public view virtual override returns (uint64) {
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:75:9:
   |
75 |         address owner
   |         ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:143:5:
    |
143 |     function owner(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistry.sol:90:9:
   |
90 |         address owner
   |         ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:143:5:
    |
143 |     function owner(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/registry/ENSRegistry.sol:105:9:
    |
105 |         address resolver
    |         ^^^^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:159:5:
    |
159 |     function resolver(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/registry/ENSRegistry.sol:118:9:
    |
118 |         uint64 ttl
    |         ^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:170:5:
    |
170 |     function ttl(bytes32 node) public view virtual override returns (uint64) {
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/registry/ENSRegistry.sol:192:9:
    |
192 |         address owner,
    |         ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:143:5:
    |
143 |     function owner(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/registry/ENSRegistry.sol:198:38:
    |
198 |     function _setOwner(bytes32 node, address owner) internal virtual {
    |                                      ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:143:5:
    |
143 |     function owner(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/registry/ENSRegistry.sol:204:9:
    |
204 |         address resolver,
    |         ^^^^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:159:5:
    |
159 |     function resolver(
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/registry/ENSRegistry.sol:205:9:
    |
205 |         uint64 ttl
    |         ^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/registry/ENSRegistry.sol:170:5:
    |
170 |     function ttl(bytes32 node) public view virtual override returns (uint64) {
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/dnsregistrar/DNSRegistrar.sol:104:9:
    |
104 |         address resolver,
    |         ^^^^^^^^^^^^^^^^
Note: The shadowed declaration is here:
  --> contracts/dnsregistrar/DNSRegistrar.sol:30:5:
   |
30 |     address public immutable resolver;
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning: This declaration shadows a builtin symbol.
   --> contracts/dnssec-oracle/DNSSECImpl.sol:109:9:
    |
109 |         uint256 now
    |         ^^^^^^^^^^^


Warning: This declaration shadows a builtin symbol.
   --> contracts/dnssec-oracle/DNSSECImpl.sol:143:9:
    |
143 |         uint256 now
    |         ^^^^^^^^^^^


Warning: This declaration shadows an existing declaration.
  --> contracts/registry/ENSRegistryWithFallback.sol:58:38:
   |
58 |     function _setOwner(bytes32 node, address owner) internal override {
   |                                      ^^^^^^^^^^^^^
Note: The shadowed declaration is here:
  --> contracts/registry/ENSRegistryWithFallback.sol:37:5:
   |
37 |     function owner(bytes32 node) public view override returns (address) {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: This declaration shadows an existing declaration.
   --> contracts/reverseRegistrar/ReverseRegistrar.sol:140:9:
    |
140 |         bytes32 node = claimForAddr(addr, owner, resolver);
    |         ^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> contracts/reverseRegistrar/ReverseRegistrar.sol:150:5:
    |
150 |     function node(address addr) public pure override returns (bytes32) {
    |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
  --> contracts/registry/ENSRegistry.sol:28:5:
   |
28 |     constructor() public {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
  --> contracts/root/Root.sol:18:5:
   |
18 |     constructor(ENS _ens) public {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
  --> contracts/root/Ownable.sol:16:5:
   |
16 |     constructor() public {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
  --> contracts/dnssec-oracle/Owned.sol:14:5:
   |
14 |     constructor() public {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Interface functions are implicitly "virtual"
  --> contracts/dnssec-oracle/algorithms/Algorithm.sol:14:5:
   |
14 |     function verify(
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Interface functions are implicitly "virtual"
  --> contracts/dnssec-oracle/digests/Digest.sol:13:5:
   |
13 |     function verify(
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
 --> contracts/ethregistrar/mocks/DummyProxyRegistry.sol:6:5:
  |
6 |     constructor(address _target) public {
  |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
  --> contracts/registry/ENSRegistryWithFallback.sol:15:5:
   |
15 |     constructor(ENS _old) public ENSRegistry() {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
  --> contracts/registry/FIFSRegistrar.sol:25:5:
   |
25 |     constructor(ENS ensAddr, bytes32 node) public {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> contracts/ethregistrar/mocks/DummyProxyRegistry.sol:10:22:
   |
10 |     function proxies(address a) external view returns (address) {
   |                      ^^^^^^^^^


Warning: Function state mutability can be restricted to pure
  --> contracts/dnsregistrar/TLDPublicSuffixList.sol:12:5:
   |
12 |     function isPublicSuffix(
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Function state mutability can be restricted to pure
  --> contracts/dnsregistrar/mocks/DummyExtendedDNSSECResolver.sol:14:5:
   |
14 |     function resolve(
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Function state mutability can be restricted to pure
  --> contracts/dnsregistrar/mocks/DummyLegacyTextResolver.sol:14:5:
   |
14 |     function text(
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Function state mutability can be restricted to pure
  --> contracts/dnssec-oracle/algorithms/DummyAlgorithm.sol:10:5:
   |
10 |     function verify(
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Function state mutability can be restricted to pure
  --> contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol:17:5:
   |
17 |     function verify(
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Function state mutability can be restricted to pure
   --> test/dnssec-oracle/TestRRUtils.sol:144:5:
    |
144 |     function testKeyTag() public view {
    |     ^ (Relevant source part starts here and spans across multiple lines).

```

## [L-02] In the constructor, there is no return of incorrect address identification

In case of incorrect address definition in the constructor , there is no way to fix it because of the variables are immutable.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

L:29         address public immutable previousRegistrar;
L:30         address public immutable resolver;

L:55         constructor(
             ......
L:62         previousRegistrar = _previousRegistrar;
L:63         resolver = _resolver;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62-L63

## [L-03] Use fixed length `bytes1,..,bytes32` variable type rather than default `bytes`

An important practical difference is that the fixed length bytes32 can be used in function arguments to pass data in or return data out of the contract. The variable length bytes type can be a function argument also, but only for internal use (inside the same contract) because the interface, called the ABI, doesn't support variable length types.

Some possibly disorienting situations are possible if bytes is used as a function argument and the contract successfully compiles. Always use fixed length types for any function that will called from outside.

*Instances occur in almost every contract.*

## [NC-1] Using while for unbounded loops isn’t recommended

Don’t write loops that are unbounded as this can hit the gas limit, causing your transaction to fail.

For the reason above, while and do while loops are rarely used.

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

L:51         while (idx < endIdx) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L51

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

L:24          while (true) {
L:60          while (true) {
L:267         while (counts > othercounts) {
L:291         while (counts > othercounts) {
L:297         while (counts > othercounts) {
L:304         while (counts > 0 && !self.equals(off, other, otheroff)) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L24

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

L:51          while (r2 != 0) {
L:361         while (scalar > 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L51
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361


## [NC-2] Events are missing `indexed` field

Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute indexed which will cause the respective arguments to be treated as log topics instead of data.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

L:47         event Claim(
L:48             bytes32 indexed node,
L:49             address indexed owner,
L:50             bytes dnsname,
L:51             uint32 inception
L:52         );

L:53         event NewPublicSuffixList(address suffixes);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L47-L53

```solidity
File: contracts/dnssec-oracle/SHA1.sol

L:4          event Debug(bytes32 x);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4

## [NC-3] Inconsistent spacing in comments

Some lines use `// x` and some use `//x`. The instances below point out the usages that don’t follow the majority, within each file.

*There are 3 instances of this issue.*

## [NC-4] Unused variables

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

L:16    uint16 constant CLASS_INET = 1;
L:17    uint16 constant TYPE_TXT = 16;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17

## [NC-5] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

*There are 12 instances of this issue.*

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

L:286       assembly {
                let srcpart := and(mload(src), not(mask))
                let destpart := and(mload(dest), mask)
                mstore(dest, or(destpart, srcpart))
            }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L19
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L73
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L80
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L197
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L213
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L229
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L245
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L267
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L276
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L286
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L311

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L7-L239
