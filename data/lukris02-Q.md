# QA Report for ENS contest
## Overview
During the audit, 3 low and 9 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Use the two-step-transfer of ownership | Low | 1
L-2 | Missing check for zero address | Low | 2
L-3 | Add more information in the events | Low | 3
NC-1 | Typos | Non-Critical | 3
NC-2 | Unused event | Non-Critical | 1
NC-3 | Order of Layout | Non-Critical | 5
NC-4 | Unused named return variables | Non-Critical | 8
NC-5 | Use mixedCase for state variables and SNAKE_CASE for constants | Non-Critical | 7
NC-6 | Visibility is not set | Non-Critical | 11
NC-7 | Missing leading underscores | Non-Critical | 15
NC-8 | Natspec is incomplete | Non-Critical | 5
NC-9 | Missing NatSpec | Non-Critical | 31

## Low Risk Findings(3)
### L-1. Use the two-step-transfer of ownership
##### Description
If the owner accidentally transfers ownership to an incorrect address, protected functions may become permanently inaccessible.
##### Instances
- [```import "./Owned.sol";```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L5)
- (https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/Owned.sol#L18-L20)

##### Recommendation
Consider using a two-step-transfer of ownership: the current owner would nominate a new owner, and to become the new owner, the nominated account would have to approve the change, so that the address is proven to be valid.
#
### L-2. Missing check for zero address
##### Description
If address(0x0) is set it may cause the contract to revert or work wrong.
##### Instances
- [```previousRegistrar = _previousRegistrar;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62) 
- [```resolver = _resolver;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L63) 

##### Recommendation
Add checks.
#
### L-3. Add more information in the events
##### Description
Some events are missing important information.
##### Instances
- [```emit NewPublicSuffixList(address(suffixes));```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82) (include old ```suffixes``` value)
- [```emit AlgorithmUpdated(id, address(algo));```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L66) (include old ```id``` and ```algo``` values)
- [```emit DigestUpdated(id, address(digest));```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L77) (include old ```id``` and ```digest``` values)

#
## Non-Critical Risk Findings(9)
### NC-1. Typos
##### Instances
- [```*      contents of the two bytes are equal. Comparison is done per-rune,```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L42) (```per-rune```)
- [```* @dev Compares a range of 'self' to all of 'other' and returns True iff```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L141) (```iff``` => ```if```)
- [```* @return True iff the signature is valid.```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L15) (```iff``` => ```if```)

#
### NC-2. Unused event
##### Instances
- [```event Debug(bytes32 x);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4)

##### Recommendation
Check if the event was meant to be used but forgotten. Consider deleting it if it is not needed.
#
### NC-3. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
Modifiers should be placed before functions and constructor:
- [```modifier onlyOwner() {```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73) 

Constants should be placed before functions:
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322-L323
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72-L79
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L210-L213
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L236-L239

##### Recommendation
Place modifiers and all constants before constructor.
#
### NC-4. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.
##### Instances
- [```return _enableNode(domain, 0);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L171) 
- [```return uint8(self[idx]);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L183) 
- [```return self.substring(offset, len);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L46) 
- [```return true;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L129) 
- [```return false;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L131) 
- [```return verifyRRSet(input, block.timestamp);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L96) 
- [```return (proof, inception);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L127) 
- [```return rrset;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L173) 

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```
#
### NC-5. Use mixedCase for state variables and SNAKE_CASE for constants
##### Description
According to [Naming Conventions](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#naming-conventions), state variables should use mixedCase ([See](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#local-and-state-variable-names)), and constants should use SNAKE_CASE ([See](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#constants)).
##### Instances
Use mixedCase:
- [```uint256 otheroffset,```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L57)
- [```uint256 otherlen```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L58)
- [```uint256[2] memory Q```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L389)
- [```uint256[3] memory P = addAndReturnProjectivePoint(x1, y1, x2, y2);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L408) 
- [```uint256 Px = inverseMod(P[2], p);```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L414)

Use SNAKE_CASE:
- [```bytes constant base32HexTable =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L322) 
- [```uint256 constant lowSmax =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L34) 

##### Recommendation
For example, change to:
```uint256 otherOffset```
#
### NC-6. Visibility is not set
##### Instances
- [```uint256 constant a =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21) 
- [```uint256 constant b =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L23) 
- [```uint256 constant gx =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L25) 
- [```uint256 constant gy =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L27) 
- [```uint256 constant p =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L29) 
- [```uint256 constant n =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L31) 
- [```uint256 constant lowSmax =```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L34) 
- [```uint16 constant DNSCLASS_IN = 1;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L27) 
- [```uint16 constant DNSTYPE_DS = 43;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L29) 
- [```uint16 constant DNSTYPE_DNSKEY = 48;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L30) 
- [```uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L32) 

##### Recommendation
It is better to specify visibility explicitly.
#
### NC-7. Missing leading underscores
##### Description
Internal and private functions should have a leading underscore.
##### Instances
- [```function parseRR(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L136) 
- [```function readTXT(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L162) 
- [```function parseAndResolve(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L173) 
- [```function resolveName(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L190) 
- [```function textNamehash(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L209) 
- [```function parseSignature(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L30) 
- [```function parseKey(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L37) 
- [```function validateSignedSet(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L140) 
- [```function validateRRs(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L181) 
- [```function verifySignature(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L225) 
- [```function verifyWithKnownKey(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L254) 
- [```function verifySignatureWithKey(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L285) 
- [```function verifyWithDS(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L330) 
- [```function verifyKeyWithDS(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L373) 
- [```function verifyDSHash(```](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L415) 

##### Recommendation
Add leading underscores where needed.
#
### NC-8. Natspec is incomplete
##### Description
Not all function parameters are described in NatSpec.
##### Instances
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L330 (describe ```i1``` and ```i2```)
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L384 (describe ```message```, ```rs```, and ```Q```)
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L5 (describe ```base```, ```exponent```, and ```modulus```)
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L227 (describe ```rrset```)
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L287 (describe ```keyrdata```) missing)

#
### NC-9. Missing NatSpec
##### Description
NatSpec is missing for 31 functions in 12 contracts:
##### Instances
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L19
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L46
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L66
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L32 (6 out of 7 functions are missing NatSpec)
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L21 (6 out of 7 functions are missing NatSpec)
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L273
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L94
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L111
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L222
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L248
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L259
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L275
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L341
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L14
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L30
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L37
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L13
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L13
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L12
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L6
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L9

##### Recommendation
Add NatSpec for all functions.