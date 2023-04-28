# Gas Optimization
#### note: the first 4 type is mis from bots

# Summary

|     | Issue |  Instance  | 
|------|-------|-------------|
|[G-01]| Use hardcode address instead address(this) | 1
|[G-02]| It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied | 1
|[G‑03]| ++i/i++ Should Be unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 5
|[G-04]| public functions to external | 1
|[G-05]|Make 3 event parameters indexed when possible | 3
|[G-06]|Use double if statements instead of && | 2
|[G-07]| >= costs less gas than > | 10
|[G-08]|Use of Bit shift operators | 9
|[G-09]| Using bools for storage incurs overhead | 32
|[G-10]|Use != 0 instead of > 0 for unsigned integer comparison | 2
|[G-11]| Use custom errors rather than revert()/require() strings to save gas | 5
|[G-12]| Multiple Address/id Mappings Can Be Combined Into A Single Mapping Of An Address/id To A Struct, Where Appropriate | 1
|[G-13]| State variables only set in the constructor should be declared immutable | 1
|[G-14]|Avoid using state variable in emit | 2
|[G‑15]| Multiple accesses of a mapping/array should use a storage pointer | 4
|[G-16]| Use constants instead of type(uintx).max | 5
|[G‑17]| Structs can be packed into fewer storage slots | 1


## [G-01] Use hardcode address instead address(this)

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol
57     address(this),
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L57

## [G-02] It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol
77     for (uint256 idx = 0; idx < shortest; idx += 32) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L77

## [G‑03] ++i/i++ Should Be unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

```solidity
File: /contracts/dnsregistrar/DNSClaimChecker.sol
        for (
            RRUtils.RRIterator memory iter = data.iterateRRs(0);
            !iter.done();
            iter.next()
        ) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L29#L33

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol
        for (
            RRUtils.RRIterator memory iter = rrset.rrs();
            !iter.done();
            iter.next()
        ) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L186-#L190

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol
        for (
            RRUtils.RRIterator memory iter = rrset.rrs();
            !iter.done();
            iter.next()
        ) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L1336-#L340

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol
        for (
            RRUtils.RRIterator memory iter = data.iterateRRs(0);
            !iter.done();
            iter.next()
        ) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L79#L83

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol
384     for (uint256 i = 0; i < data.length + 31; i += 32) {     
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L384


## [G-04] public functions to external
External call cost is less expensive than of public functions.
Contracts are allowed to override their parents’ functions and change the visibility from external to public.
The following functions could be set external to save gas and improve code quality:

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol
    function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    )
        public
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)
    {

```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L107-#L116

## [G-05]Make 3 event parameters indexed when possible
It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes value);

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes indexed value);

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol
47    event Claim(
        bytes32 indexed node,
        address indexed owner,
        bytes dnsname,
        uint32 inception
      );

53   event NewPublicSuffixList(address suffixes);      
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/SHA1.sol
4   event Debug(bytes32 x);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4

## [G-06]Use double if statements instead of &&
If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

pragma solidity 0.8.10;

contract NestedIfTest {

    //Execution cost: 22334 gas
    function funcBad(uint256 input) public pure returns (string memory) { 
       if (input<10 && input>0 && input!=6){ 
           return "If condition passed";
       } 

   }

    //Execution cost: 22294 gas
    function funcGood(uint256 input) public pure returns (string memory) { 
    if (input<10) { 
        if (input>0){
            if (input!=6){
                return "If condition passed";
            }
        }
    }
}
}

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
128     if (x0 == 0 && y0 == 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol
178     if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178



## [G-07] >= costs less gas than >
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol
60     if (offset + len > self.length) {

63     if (otheroffset + otherlen > other.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
43     if (u > m) u = u % m;

361    while (scalar > 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol
```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol
150     if (lastTxtIdx > txt.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol
267     while (counts > othercounts) {

291     while (counts > othercounts) {

297     while (othercounts > counts) {    

304     while (counts > 0 && !self.equals(off, other, otheroff)) {

389     if (i + 32 > data.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

## [G-08]Use of Bit shift operators
Check if arithmethic operations can be achieved using bitwise operators, if yes, implement same operations using bitwise operators and compare the gas consumed. Usually bitwise logic will be cheaper (ofc we cannot use bitwise everywhere, there some limitations)

Note: Bit shift << and >> operators are not among the arithmetic ones, and thus don’t revert on overflow.
exmple:
-    uint256 alpha = someVar / 256;
-    uint256 beta = someVar % 256;
+    uint256 alpha = someVar >> 8;
+    uint256 beta = someVar & 0xff;


-    uint 256 alpha = delta / 2;
-    uint 256 beta = delta / 4; 
-    uint 256 gamma = delta * 8;
+    uint 256 alpha = delta >> 1;
+    uint 256 beta = delta >> 2; 
+    uint 256 gamma = delta << 3;

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol
352     uint256 bitlen = len * 5;

353     if (len % 8 == 0) {

356     } else if (len % 8 == 2) {

360     } else if (len % 8 == 4) {

364     } else if (len % 8 == 5) {

368     } else if (len % 8 == 7) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol
390     uint256 unused = 256 - (data.length - i) * 8;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L390

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
355     if (scalar % 2 == 0) {

364     if (scalar % 2 == 1) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

## [G-09] Using bools for storage incurs overhead
  
      // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
    
    Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol 
117     ) internal pure returns (bool) {

134     ) internal pure returns (bool) {

152     ) internal pure returns (bool) {

167     ) internal pure returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol    

```solidity
File: /contracts/dnsregistrar/DNSClaimChecker.sol
22     ) internal pure returns (address, bool) {

35     bool found;

50     ) internal pure returns (address, bool) {

55     bool found;

70     ) internal pure returns (address, bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol
127     ) external pure override returns (bool) {

157     bool found;    
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol
290     ) internal view returns (bool) {

378     ) internal view returns (bool) {

419     ) internal view returns (bool) {    
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
127     ) internal pure returns (bool isZero) {

137     function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {

390     ) internal pure returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
File: /contracts/utils/HexUtils.sol
15     ) internal pure returns (bytes32 r, bool valid) {

72     ) internal pure returns (address, bool) {

74      (bytes32 r, bool valid) = hexStringToBytes32(str, idx, lastIdx);    
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol
11     ) internal view returns (bool success, bytes memory output) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L11

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol
121      (bool ok, bytes memory ret) = address(dnsresolver)

179      (address ret, bool valid) = nameOrAddress.hexToAddress(
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
21     ) external view override returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol
150     function done(RRIterator memory iter) internal pure returns (bool) {

256     ) internal pure returns (bool) {

315     ) internal pure returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol
18     ) external view override returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L18
```solidity
File: /contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol
17     ) external view override returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L17

```solidity
File: /contracts/dnssec-oracle/algorithms/RSAVerify.sol
18     ) internal view returns (bool, bytes memory) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L18

```solidity
File: /contracts/dnssec-oracle/digests/SHA1Digest.sol
16     ) external pure override returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L16

```solidity
File: /contracts/dnssec-oracle/digests/SHA256Digest.sol
15     ) external pure override returns (bool) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L15

## [G-10]Use != 0 instead of > 0 for unsigned integer comparison

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0.

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol
361     while (scalar > 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol
304     while (counts > 0 && !self.equals(off, other, otheroff)) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304

## [G-11] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```solidity
File: /contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
33      require(data.length == 64, "Invalid p256 signature length");

40      require(data.length == 68, "Invalid p256 key length");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol
381     require(data.length <= 8192, "Long keys not permitted");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
File: /contracts/dnssec-oracle/digests/SHA1Digest.sol
17      require(hash.length == 20, "Invalid sha1 hash length");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L17

```solidity
File: /contracts/dnssec-oracle/digests/SHA256Digest.sol
16      require(hash.length == 32, "Invalid sha256 hash length");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L16

## [G-12] Multiple Address/id Mappings Can Be Combined Into A Single Mapping Of An Address/id To A Struct, Where Appropriate

mapping(address => address) public owner;    // Mapping of locker contract address to its owner i.e owner[locker] = Owner of the collateral locker
mapping(address => bool)    public isLocker;  // True if collateral locker was created by this factory, otherwise false

refactor

      struct CollateralLockerOwner{
        address owner;
        bool isLocker;
    }

mapping(address => CollateralLockerOwner) public owners; // True if collateral locker was created by this factory, otherwise false


https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

Consider mixing different mappings into an struct when able in order to save gas.


	mapping(bytes32 => bool) public cancelledOrFilled;
     mapping(address => uint256) public nonces;

If both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol
    mapping(uint8 => Algorithm) public algorithms;
    mapping(uint8 => Digest) public digests;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L45-#L46

## [G-13] State variables only set in the constructor should be declared immutable

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol
39    string public gatewayURL;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L39-#L46

## [G-14]Avoid using state variable in emit

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol
66     emit NewPublicSuffixList(address(suffixes));

82     emit NewPublicSuffixList(address(suffixes));
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

## [G‑15] Multiple accesses of a mapping/array should use a storage pointer
Caching a mapping’s value in a storage pointer when the value is accessed multiple times saves ~40 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable’s reference instead of repeatedly fetching it.

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol
152     if (!RRUtils.serialNumberGte(inception, inceptions[node])) {

155     inceptions[node] = inception;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol
420     if (address(digests[digesttype]) == address(0)) {

423     return digests[digesttype].verify(data, digest);    
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol


## [G-16] Use constants instead of type(uintx).max
type(uint120).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol
88     mask = type(uint256).max;

398    return type(uint256).max;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
File: /contracts/dnsregistrar/RecordParser.sol
24      if (separator == type(uint256).max) {

25      return ("", "", type(uint256).max);

33      if (terminator == type(uint256).max) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

## [G‑17] Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol
120     struct RRIterator {
        bytes data;
        uint256 offset;
        uint16 dnstype;
        uint16 class;
        uint32 ttl;
        uint256 rdataOffset;
        uint256 nextOffset;
    }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L120-L128