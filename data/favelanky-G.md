# Table of Contents

| Number | Issues Details                                                                         | Count |
| :----- | :------------------------------------------------------------------------------------- | :---- |
| [G-1]  | Use assembly to check for address(0)                                                   | 5     |
| [G-2]  | Use Custom Errors                                                                      | 7     |
| [G-3]  | Create variables outside the loop                                                      | 12    |
| [G-4]  | State variables only set in the constructor should be declared `immutable`             | 1     |
| [G-5]  | Pre-increments and pre-decrements are cheaper than post-increments and post-decrements | 4     |
| [G-6]  | Non efficient zero initialization.                                                     | 10    |

Total number of issues: 39

# Findings

## [G-1] Use assembly to check for address(0)

<details>
<summary><i>There are 5 instances of this issue:</i></summary>

```solidity
File: dnssec-oracle/DNSSECImpl.sol

317:  if (address(algorithm) == address(0)) {

420:  if (address(digests[digesttype]) == address(0)) {

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L317

```solidity
File: dnsregistrar/OffchainDNSResolver.sol

197:  if (resolver == address(0)) {

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197

```solidity
File: dnsregistrar/DNSRegistrar.sol

116:  if (resolver == address(0)) {

187:  if (owner == address(0) || owner == previousRegistrar) {

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L116

</details>

## [G-2] Use Custom Errors

[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

<details>
<summary><i>There are 7 instances of this issue:</i></summary>

```solidity
File: dnssec-oracle/RRUtils.sol

381:             require(data.length <= 8192, "Long keys not permitted");

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L381

```solidity
File: dnssec-oracle/algorithms/P256SHA256Algorithm.sol

33:         require(data.length == 64, "Invalid p256 signature length");

40:         require(data.length == 68, "Invalid p256 key length");

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L33

```solidity
File: dnssec-oracle/digests/SHA1Digest.sol

17:         require(hash.length == 20, "Invalid sha1 hash length");

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L17

```solidity
File: dnssec-oracle/digests/SHA256Digest.sol

16:         require(hash.length == 32, "Invalid sha256 hash length");

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L16

```solidity
File: wrapper/BytesUtils.sol

35:             require(offset == self.length - 1, "namehash: Junk at end of name");

53:         require(idx < self.length, "readLabel: Index out of bounds");

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L35

</details>

## [G-3] Create variables outside the loop

Can make the variable outside the loop to save gas.

<details>
<summary><i>There are 12 instances of this issue:</i></summary>

```solidity
File: dnsregistrar/DNSClaimChecker.sol

51:         while (idx < endIdx) {
52:             uint256 len = rdata.readUint8(idx);
53:             ...
61:         }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L51-L61

```solidity
File: dnsregistrar/OffchainDNSResolver.sol

79:         for (
80:             RRUtils.RRIterator memory iter = data.iterateRRs(0);
81:             !iter.done();
82:             iter.next()
83:         ) {
84:             // Ignore records with wrong name, type, or class
85:             bytes memory rrname = RRUtils.readName(iter.data, iter.offset);
86:             ...
130:         }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L79-L130

```solidity
File: dnssec-oracle/BytesUtils.sol

77:         for (uint256 idx = 0; idx < shortest; idx += 32) {
78:             ...
84:             if (a != b) {
86:                 uint256 mask;
87:                 ...
97:         }

341:         for (uint256 i = 0; i < len; i++) {
342:             bytes1 char = self[off + i];
343:             ...
350:         }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L77-L97

```solidity
File: dnssec-oracle/DNSSECImpl.sol

118:         for (uint256 i = 0; i < input.length; i++) {
119:             RRUtils.SignedSet memory rrset = validateSignedSet(
120:                 input[i],
121:                 proof,
122:                 now
123:             );
124:             ...
126:         }

260:         for (; !proof.done(); proof.next()) {
261:             bytes memory proofName = proof.name();
262:             ...
274:         }

336:         for (
337:             RRUtils.RRIterator memory iter = rrset.rrs();
338:             !iter.done();
339:             iter.next()
340:         ) {
341:             if (iter.dnstype != DNSTYPE_DNSKEY) {
342:                 revert InvalidProofType(iter.dnstype);
343:             }
344:
345:             bytes memory keyrdata = iter.rdata();
346:             RRUtils.DNSKEY memory dnskey = keyrdata.readDNSKEY(
347:                 0,
348:                 keyrdata.length
349:             );
350:             ...
361:         }

380:         for (; !dsrrs.done(); dsrrs.next()) {
381:             bytes memory proofName = dsrrs.name();
382:             if (!proofName.equals(keyname)) {
383:                 revert ProofNameMismatch(keyname, proofName);
384:             }
385:
386:             RRUtils.DS memory ds = dsrrs.data.readDS(
387:                 dsrrs.rdataOffset,
388:                 dsrrs.nextOffset - dsrrs.rdataOffset
389:             );
390:             ...
404:         }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L118-L126

```solidity
File: dnssec-oracle/RRUtils.sol

24:         while (true) {
25:             assert(idx < self.length);
26:             uint256 labelLen = self.readUint8(idx);
27:             ...
31:         }

60:         while (true) {
61:             assert(offset < self.length);
62:             uint256 labelLen = self.readUint8(offset);
63:             ...
68:         }

384:             for (uint256 i = 0; i < data.length + 31; i += 32) {
385:                 uint256 word;
386:                 ...
399:             }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L24-L31

```solidity
File: resolvers/Multicallable.sol

13:         for (uint256 i = 0; i < data.length; i++) {
14:             if (nodehash != bytes32(0)) {
15:                 bytes32 txNamehash = bytes32(data[i][4:36]);
16:                 require(
17:                     txNamehash == nodehash,
18:                     "multicall: All records must have a matching namehash"
19:                 );
20:             }
21:             ...
26:         }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/resolvers/Multicallable.sol#L13-L26

```solidity
File: resolvers/profiles/DNSResolver.sol

64:         for (
65:             RRUtils.RRIterator memory iter = data.iterateRRs(0);
66:             !iter.done();
67:             iter.next()
68:         ) {
69:             if (resource == 0) {
70:                 resource = iter.dnstype;
71:                 name = iter.name();
72:                 nameHash = keccak256(abi.encodePacked(name));
73:                 value = bytes(iter.rdata());
74:             } else {
75:                 bytes memory newName = iter.name();
76:                 ...
93:             }
94:         }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/resolvers/profiles/DNSResolver.sol#L64-L94

```solidity
File: utils/UniversalResolver.sol

530:         for (uint256 i = 0; i < length; i++) {
531:             bytes memory item = multicallData.data[i];
532:             bool failure = multicallData.failures[i];
533:             ...
566:         }

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/UniversalResolver.sol#L530-L566

</details>

## [G-4] State variables only set in the constructor should be declared `immutable`

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a `PUSH32` (3 gas).

<details>
<summary><i>There are 1 instances of this issue:</i></summary>

```solidity
File: dnsregistrar/OffchainDNSResolver.sol

46:         gatewayURL = _gatewayURL;

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L46

</details>

## [G-5] Pre-increments and pre-decrements are cheaper than post-increments and post-decrements1

Saves 5 gas per iteration.

<details>
<summary><i>There are 4 instances of this issue:</i></summary>

```solidity
File: dnssec-oracle/RRUtils.sol

269:             counts--;

294:             counts--;

300:             othercounts--;

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L269

```solidity
File: resolvers/profiles/DNSResolver.sol

193:                 versionable_nameEntriesCount[version][node][nameHash]--;

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/resolvers/profiles/DNSResolver.sol#L193

</details>

## [G-6] Non efficient zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

There are several places where an int is initialized to zero, which looks like:

<details>
<summary><i>There are 10 instances of this issue:</i></summary>

```solidity
File: dnsregistrar/OffchainDNSResolver.sol

215:         bytes32 parentNode = bytes32(0);

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L215

```solidity
File: dnssec-oracle/BytesUtils.sol

339:         uint256 ret = 0;

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#339

```solidity
File: dnssec-oracle/RRUtils.sol

59:         uint256 count = 0;

72:     uint256 constant RRSIG_TYPE = 0;

210:     uint256 constant DNSKEY_FLAGS = 0;

236:     uint256 constant DS_KEY_TAG = 0;

263:         uint256 off = 0;

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L59

```solidity
File: resolvers/profiles/DNSResolver.sol

57:         uint16 resource = 0;

58:         uint256 offset = 0;

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/resolvers/profiles/DNSResolver.sol#L57

```solidity
File: utils/NameEncoder.sol

12:         uint8 labelLength = 0;

```

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L12

</details>
`
