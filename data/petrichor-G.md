# Gas optmization 

## summary 

|no |Issue|Instances|
|----|-------|------|
| [G-1] |  use nested if instead of && | 2 |
| [G-2]  | Use != 0 instead of > 0 for unsigned integer comparison | 2 |
| [G-3]| It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.| 2 |
| [G-4] | There is no need to initialize variables with default values | 6|
|[G-5]| State variables only set in the constructor should be declared immutable |2|
| [G-6]|Avoid state variable in emit|2|
|[G-7]|Use of Bit shift operators|7|
|[G-8]|Use custom errors rather than revert()/require() strings to save gas|3|
|[G-9]|>= costs less gas than >|5|
|[G‑10] |Multiple accesses of a mapping/array should use a storage pointer|4|
|[G-11] |Use hardcode address instead address(this)|1|
|[G-12]| Using XOR (^) and OR (single pipe line) bitwise equivalents|2|
|[G-13]| Using a positive conditional flow to save a NOT opcode | 6




### [G-1] use nested if instead of &&
```solidity
 if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178

```solidity
if (x0 == 0 && y0 == 0) {

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128


 ###  [G-2] Use != 0 instead of > 0 for unsigned integer comparison
 
```solidity
 while (scalar > 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361

```solidity
 while (counts > 0 && !self.equals(off, other, otheroff)) {
```   
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304
 
 ### [G-3] It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity
  event Debug(bytes32 x);
```
 https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4
 ```solidity
  event Claim(
        bytes32 indexed node,
        address indexed owner,
        bytes dnsname,
        uint32 inception
    );
    event NewPublicSuffixList(address suffixes);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L47#L53

 ### [G-4]There is no need to initialize variables with default values 
 ### Note: these default values missed from automation


```solidity
  uint8 labelLength = 0;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L12

```solidity
        node = 0;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L16
```solidity
 uint256 constant RRSIG_TYPE = 0;
 ```
 https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L72
 ```solidity
uint256 count = 0;

 ```
 https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L59

 ```solidity
 uint256 constant DS_KEY_TAG = 0;
 ```
  https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L236
  ```solidity
  uint256 off = 0;

  ```
  https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L263

  ```solidity
for (uint256 idx = 0; idx < shortest; idx += 32) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L77

```solidity
  uint256 ret = 0;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L339

### [G-5] State variables only set in the constructor should be declared immutable

```solidity

  string public gatewayURL;


```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L39#L46

```solidity
   PublicSuffixList public suffixes;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L28#L59

### [G-6] Avoid state variable in emit
```solidity
        emit NewPublicSuffixList(address(suffixes));
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L66       
```solidity

        emit NewPublicSuffixList(address(suffixes));
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82

### [G-7] Use of Bit shift operators

```solidity
     uint256 bitlen = len * 5;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L352

```solidity
353     if (len % 8 == 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L353

```solidity
356     } else if (len % 8 == 2) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L356

```solidity
360     } else if (len % 8 == 4) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L360

```solidity
364     } else if (len % 8 == 5) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L364

```solidity
368     } else if (len % 8 == 7) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L368

```solidity
390     uint256 unused = 256 - (data.length - i) * 8;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L390

### [G-8] Use custom errors rather than revert()/require() strings to save gas

```solidity
381     require(data.length <= 8192, "Long keys not permitted");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L381

```solidity
17      require(hash.length == 20, "Invalid sha1 hash length");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L17

```solidity
16      require(hash.length == 32, "Invalid sha256 hash length");
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L16

### [G-9]>= costs less gas than >

```solidity
60     if (offset + len > self.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L60

```solidity
63     if (otheroffset + otherlen > other.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L63

```solidity
43     if (u > m) u = u % m;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L43

```solidity
361    while (scalar > 0) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361

```solidity
150     if (lastTxtIdx > txt.length) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150

### [G‑10] Multiple accesses of a mapping/array should use a storage pointer

```solidity
152     if (!RRUtils.serialNumberGte(inception, inceptions[node])) {
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L152

```solidity
155     inceptions[node] = inception;
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L155

```solidity
420     if (address(digests[digesttype]) == address(0)) {   
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420

```solidity
423     return digests[digesttype].verify(data, digest);    
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L423

### [G-11] Use hardcode address instead address(this)
### missed from automation


```solidity
57     address(this),
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L57

### [G-12] Using XOR (^) and OR (|) bitwise equivalents

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

### [G-13] Using a positive conditional flow to save a NOT opcode

```solidity
152      if (!RRUtils.serialNumberGte(inception, inceptions[node])) {
            revert StaleProof();
        }

```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L152-L154

```solidity
159     if (!found) {
            revert NoOwnerRecordFound();
        }       
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L159-L161

```solidity
file:    DNSSECImpl.sol
233      if (!name.isSubdomainOf(rrset.signerName)) {
            revert InvalidSignerName(name, rrset.signerName);
        }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L233-L235

```solidity
262     if (!proofName.equals(rrset.signerName)) {
                revert ProofNameMismatch(rrset.signerName, proofName);
            }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L262-L264

```solidity
382       if (!proofName.equals(keyname)) {
                revert ProofNameMismatch(keyname, proofName);
            }  

```
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
