## Summary 

### Gas Optimization

|no |Issue|Instances|
|----|-------|------|
| [G-01] | Avoid state variable in emit | 2 | - |  
| [G-02] | Duplicated require()/if() checks should be refactored to a modifier or function | 4 | - | 
| [G-03] | Use != 0 instead of > 0 for unsigned integer comparison | 2 | - | 
| [G-04] | Avoid emitting constants. | 5 | - | 
| [G-05] | Using XOR (^) and OR (single pipe line ) bitwise equivalents | 2 | - | 
| [G-06] | Using a positive conditional flow to save a NOT opcode | 6 | - | 
| [G-07] | Change public state variable visibility to private | 8 | - | 
| [G-08] | Structs can be packed into fewer storage slots | 8 | - | 
| [G-09] | use nested if instead of &&  | 2 | - |
| [G-10] | Use != 0 instead of > 0 for unsigned integer comparison  | 2 | - |
| [G-11] | It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed. | 2 | - |
| [G-12] |There is no need to initialize variables with default values  | 8 | - |
| [G-13] |State variables only set in the constructor should be declared immutable  | 2 | - |
| [G-14] | Do not calculate constants  | 6 | - |
| [G-15] | Use of Bit shift operators | 7 | - |
| [G-16] |Use custom errors rather than revert()/require() strings to save gas | 3 | - |
| [G-17] |  >= costs less gas than > | 5 | - |
| [G-18] | Multiple accesses of a mapping/array should use a storage pointer | 4 | - |
| [G-19] | Use hardcode address instead address(this) | 1 | - |


## [G-01] Avoid state variable in emit
```solidity
file:   DNSRegistrar.sol
66      emit NewPublicSuffixList(address(suffixes));
82      emit NewPublicSuffixList(address(suffixes));
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L66     
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82

## [G-02]Duplicated require()/if() checks should be refactored to a modifier or function

```solidity 
file:   EllipticCurve.sol 
128     if (x0 == 0 && y0 == 0) 

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128
```solidity
file:   OffchainDNSResolver.sol
178     if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x")

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178

```solidity
file:   RRUtils.sol
304     while (counts > 0 && !self.equals(off, other, otheroff)) 
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304
```solidity
file:  BytesUtils.sol
343    require(char >= 0x30 && char <= 0x7A);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L343

## [G-03] Use != 0 instead of > 0 for unsigned integer comparison

```solidity
file:     EllipticCurve.sol
361       while (scalar > 0) 

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361

```solidity
file:   RRUtils.so
304     while (counts > 0 && !self.equals(off, other, otheroff))
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304

## [G-04]    Avoid emitting constants.

```solidity   
file;    DNSRegistrar.sol
66       emit NewPublicSuffixList(address(suffixes));
82       emit NewPublicSuffixList(address(suffixes));
163      emit Claim(node, addr, name, inception);

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L66
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L163    

```solidity
file:   DNSSECImpl.sol
66      emit AlgorithmUpdated(id, address(algo));
77      emit DigestUpdated(id, address(digest));
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L66
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L77

## [G-5] Using XOR (^) and OR (|) bitwise equivalents

```solidity
file:        DNSSECImpl.sol
201              if (
                    name.length != iter.data.nameLength(iter.offset) ||
                    !name.equals(0, iter.data, iter.offset, name.length)
                )
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L201-L204

```solidity
file:   OffchainDNSResolver.sol
86      if (
                !rrname.equals(name) ||
                iter.class != CLASS_INET ||
                iter.dnstype != TYPE_TXT
            )
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L90

## [G-06] Using a positive conditional flow to save a NOT opcode

```solidity
file:
152      if (!RRUtils.serialNumberGte(inception, inceptions[node])) {
            revert StaleProof();
        }
159     if (!found) {
            revert NoOwnerRecordFound();
        }       
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L152-L154
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L159-L161

```solidity
file:    DNSSECImpl.sol
233      if (!name.isSubdomainOf(rrset.signerName)) {
            revert InvalidSignerName(name, rrset.signerName);
        }
262     if (!proofName.equals(rrset.signerName)) {
                revert ProofNameMismatch(rrset.signerName, proofName);
            }
382       if (!proofName.equals(keyname)) {
                revert ProofNameMismatch(keyname, proofName);
            }  

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L233-L235
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L262-L264
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L382-L384

```solidity
file:   OffchainDNSResolver.sol
86      if (
                !rrname.equals(name) ||
                iter.class != CLASS_INET ||
                iter.dnstype != TYPE_TXT
            )
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L90

## [G-7]   Change public state variable visibility to private

```solidity
file:    DNSRegistrar.sol
26       ENS public immutable ens;
         DNSSEC public immutable oracle;
         PublicSuffixList public suffixes;
         address public immutable previousRegistrar;
         address public immutable resolver;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L26-L30

```solidity
file:  OffchainDNSResolver.sol
37     ENS public immutable ens;
       DNSSEC public immutable oracle;
       string public gatewayURL;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L37-L39

## [G-08]    Structs can be packed into fewer storage slots
```solidity
file:   DNSRegistrar.sol
40      struct OwnerRecord {
        bytes name;
        address owner;
        address resolver;
        uint64 ttl;
    }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L40-L45

```solidity
file:   RRUtils.sol
81      struct SignedSet {
        uint16 typeCovered;
        uint8 algorithm;
        uint8 labels;
        uint32 ttl;
        uint32 expiration;
        uint32 inception;
        uint16 keytag;
        bytes signerName;
        bytes data;
        bytes name;
    }
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
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L81-L92
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L120-L128

## [G-9] use nested if instead of &&
```solidity
file:  OffchainDNSResolver.sol
178    if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178

```solidity
file: EllipticCurve.sol
128   if (x0 == 0 && y0 == 0) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128

 ##  [G-10] Use != 0 instead of > 0 for unsigned integer comparison
 
```solidity
file:   EllipticCurve.sol
361     while (scalar > 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361

```solidity
file:  RRUtils.sol
304    while (counts > 0 && !self.equals(off, other, otheroff)) {
```   
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304

 ## [G-11] It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity
file:  SHA1.sol
4      event Debug(bytes32 x);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4
 
 ```solidity
 file:  DNSRegistrar.sol
 53     event Claim(
        bytes32 indexed node,
        address indexed owner,
        bytes dnsname,
        uint32 inception
    );
    event NewPublicSuffixList(address suffixes);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L53


 ## [G-12] There is no need to initialize variables with default values
 ### Note: these default values missed from automation

```solidity
file:  NameEncoder.sol
12     uint8 labelLength = 0;
16     node = 0;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L12
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L16

```solidity
file:  RRUtils.so
59     uint256 count = 0;
72     uint256 constant RRSIG_TYPE = 0;
236    uint256 constant DS_KEY_TAG = 0;
263    uint256 off = 0;

 ```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L59
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L236
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L263

```solidity
file:  BytesUtils.sol
77     for (uint256 idx = 0; idx < shortest; idx += 32) {
339    uint256 ret = 0;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L77
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L339

## [G-13] State variables only set in the constructor should be declared immutable

```solidity
file:  OffchainDNSResolver.sol
46     string public gatewayURL;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L39-L46

```solidity
file:   DNSRegistrar.sol
28      PublicSuffixList public suffixes;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L28

## [G-14] Do not calculate constants
```solidity
file:     DNSClaimChecker.sol
16        uint16 constant CLASS_INET = 1;
17        uint16 constant TYPE_TXT = 16;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17
```solidity
file: 
27    uint16 constant DNSCLASS_IN = 1;
29    uint16 constant DNSTYPE_DS = 43;
30    uint16 constant DNSTYPE_DNSKEY = 48;
32    uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L27-L32
```solidity
file:  EllipticCurve.sol  
21  uint256 constant a =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;
    uint256 constant b =
        0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;
    uint256 constant gx =
        0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;
    uint256 constant gy =
        0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;
    uint256 constant p =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;
    uint256 constant n =
        0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;

    uint256 constant lowSmax =
        0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0;

   
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L35

```solidity
file:   OffchainDNSResolver.sol
29      uint16 constant CLASS_INET = 1;
30      uint16 constant TYPE_TXT = 16;

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L29-L30

```solidity
file:  RRUtils.sol 

72  uint256 constant RRSIG_TYPE = 0;
    uint256 constant RRSIG_ALGORITHM = 2;
    uint256 constant RRSIG_LABELS = 3;
    uint256 constant RRSIG_TTL = 4;
    uint256 constant RRSIG_EXPIRATION = 8;
    uint256 constant RRSIG_INCEPTION = 12;
    uint256 constant RRSIG_KEY_TAG = 16;
    uint256 constant RRSIG_SIGNER_NAME = 18;
210 uint256 constant DNSKEY_FLAGS = 0;
    uint256 constant DNSKEY_PROTOCOL = 2;
    uint256 constant DNSKEY_ALGORITHM = 3;
    uint256 constant DNSKEY_PUBKEY = 4;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72-L79
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L210-L213

## [G-15]Use of Bit shift operators

```solidity
file:   BytesUtils.sol
352     uint256 bitlen = len * 5;
353     if (len % 8 == 0) {
356     } else if (len % 8 == 2) {
360     } else if (len % 8 == 4) {
364     } else if (len % 8 == 5) {
368     } else if (len % 8 == 7) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L352
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L353
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L356
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L360
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L364
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L368

```solidity
file:   RRUtils.sol 
390     uint256 unused = 256 - (data.length - i) * 8;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L390


## [G-16] Use custom errors rather than revert()/require() strings to save gas

```solidity
file:   RRUtils.sol
381     require(data.length <= 8192, "Long keys not permitted");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L381

```solidity
file:   SHA1Digest.sol
17      require(hash.length == 20, "Invalid sha1 hash length");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L17

```solidity
file:   SHA256Digest.sol
16      require(hash.length == 32, "Invalid sha256 hash length");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L16

## [G-17] >= costs less gas than >

```solidity
file:  BytesUtils.sol
60     if (offset + len > self.length) {
63     if (otheroffset + otherlen > other.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L60
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L63

```solidity
file:  EllipticCurve.sol
43     if (u > m) u = u % m;
361    while (scalar > 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L43
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361

```solidity
file:   OffchainDNSResolver.sol
150     if (lastTxtIdx > txt.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150

## [G‑18] Multiple accesses of a mapping/array should use a storage pointer

```solidity
file:   DNSRegistrar.sol 
152     if (!RRUtils.serialNumberGte(inception, inceptions[node])) {
155     inceptions[node] = inception;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L152
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L155

```solidity
file:   DNSSECImpl.sol
420     if (address(digests[digesttype]) == address(0)) {  
423     return digests[digesttype].verify(data, digest);    
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L423

## [G-19] Use hardcode address instead address(this)

```solidity
file:  OffchainDNSResolver.sol
57     address(this),
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L57