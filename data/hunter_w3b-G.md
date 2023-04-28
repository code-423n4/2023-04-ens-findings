## Summary

### Gas Optimizations

|               | Issue                                                                                                             | Instances | Total Gas Saved |
| ------------- | :---------------------------------------------------------------------------------------------------------------- | :-------: | :-------------: |
| [G&#x2011;01] | Avoid `contract existence` checks by using solidity version 0.8.10 or later                                       |     7     |       700       |
| [G&#x2011;02] | Add unchecked {} for subtractions where the operands can't underflow because of previous require and if statement |     5     |       425       |
| [G&#x2011;03] | Using private rather than public for `constants`, save gas                                                        |    32     |        -        |
| [G&#x2011;04] | State variables should be cached in `stack variables` rather than re-reading them from `storage`                  |    12     |      1164       |
| [G&#x2011;05] | Stack variable used as a cheaper cache for `state variables` is `only used once`                                  |     4     |       12        |
| [G&#x2011;06] | Not Using the named `return` variables when a function returns, `WASTES DEPLOYMENT GAS`                           |    34     |        -        |
| [G&#x2011;07] | Can make the variable outside the loop to save gas                                                                |    11     |        -        |
| [G&#x2011;08] | Public functions not callled by the contract should be declared external instead                                  |     5     |        -        |
| [G&#x2011;09] | Use assembly to check for address(0)                                                                              |     7     |        -        |
| [G&#x2011;10] | The result of function calls should be cached rather than re-calling the function                                 |    22     |        -        |
| [G&#x2011;11] | State variables only set in the constructor should be declared `immutalbe`                                        |     2     |      4194       |
| [G&#x2011;12] | Using storage instead of memory for structs/arrays saves gas                                                      |     1     |      2100       |
| [G&#x2011;13] | Duplicated if() checks should be refactored to a modifier or function                                             |     3     |        -        |
| [G&#x2011;14] | Use constants instead of `type(uintx).max`                                                                        |     4     |        -        |
| [G&#x2011;15] | Use nested if and, avoid multiple check combinations                                                              |     3     |        -        |
| [G&#x2011;16] | Use `assembly` to write address storage values                                                                    |     2     |        -        |
| [G&#x2011;17] | Multiple accesses of a `mapping/array` should use a local variable cache                                          |     3     |       126       |

Total: 157 instances over 17 issues

## Gas Optimizations

## [G-01] Avoid `contract existence` checks by using solidity version 0.8.10 or later

### Summary

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

### Details

There are **7** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol


/// @audit  resolve()
109                         IExtendedDNSResolver(dnsresolver).resolve(

/// @audit resolve()
119                    return IExtendedResolver(dnsresolver).resolve(name, query);

/// @audit addr()
200        return IAddrResolver(resolver).addr(node);
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

/// @audit  getOwnerAddress()
158        (addr, found) = DNSClaimChecker.getOwnerAddress(name, data);
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

/// @audit  rsarecover()
41        (ok, result) = RSAVerify.rsarecover(modulus, exponent, sig);
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

/// @audit  rsarecover()
40        (ok, result) = RSAVerify.rsarecover(modulus, exponent, sig);
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/RSAVerify.sol

/// @audit  modexp()
19          return ModexpPrecompile.modexp(S, E, N);
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol

## [G-02] Add unchecked {} for subtractions where the operands can't underflow because of previous require and if statement

### Summary

This will stop the check for overflow and underflow so it will save gas.

### Details

There are **5** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

/// @audit  exponentLen  is check in if statement
27                key.length - exponentLen - 5
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L27

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

53                (t1, t2, r1, r2) = (t2, t1 - int256(q) * t2, r2, r1 - q * r2);

56            if (t1 < 0) return (m - uint256(-t1));
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

26                key.length - exponentLen - 5

33                key.length - exponentLen - 7
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

## [G-03] Using private rather than public for `constants`, save gas

### Details

There are **32** instances of this issue.

### Github Permalinks

```solidity
File:/contracts/dnsregistrar/DNSClaimChecker.sol

/// @audit  default visiblity is public
16    uint16 constant CLASS_INET = 1;

17    uint16 constant TYPE_TXT = 16;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

```solidity
File:/contracts/dnsregistrar/OffchainDNSResolver.sol

29  uint16 constant CLASS_INET = 1;

30  uint16 constant TYPE_TXT = 16;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol

322    bytes constant base32HexTable =

```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol

72    uint256 constant RRSIG_TYPE = 0;

73    uint256 constant RRSIG_ALGORITHM = 2;

74    uint256 constant RRSIG_LABELS = 3;

75    uint256 constant RRSIG_TTL = 4;

76    uint256 constant RRSIG_EXPIRATION = 8;

77    uint256 constant RRSIG_INCEPTION = 12;

78    uint256 constant RRSIG_KEY_TAG = 16;

79    uint256 constant RRSIG_SIGNER_NAME = 18;

210    uint256 constant DNSKEY_FLAGS = 0;

211    uint256 constant DNSKEY_PROTOCOL = 2;

212    uint256 constant DNSKEY_ALGORITHM = 3;

213   uint256 constant DNSKEY_PUBKEY = 4;

236    uint256 constant DS_KEY_TAG = 0;

237    uint256 constant DS_ALGORITHM = 2;

238    uint256 constant DS_DIGEST_TYPE = 3;

239    uint256 constant DS_DIGEST = 4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

21    uint256 constant a =

23    uint256 constant b =

25    uint256 constant gx =

27    uint256 constant gy =

29    uint256 constant p =

31    uint256 constant n =

34    uint256 constant lowSmax =
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

27    uint16 constant DNSCLASS_IN = 1;

29    uint16 constant DNSTYPE_DS = 43;

30    uint16 constant DNSTYPE_DNSKEY = 48;

32    uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [G-04] State variables should be cached in `stack variables` rather than re-reading them from `storage`

### Summary

Caching will replace each Gwarmaccess (100 gas) with a much cheaper stack read.
Less obvious fixes/optimizations include having local storage variables of mappings within state variable mappings or mappings within state variable structs, having local storage variables of structs within mappings, having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

### Details

There are **12** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

/// @audit  suffixes    -->     state variable
66        emit NewPublicSuffixList(address(suffixes));

/// @audit  suffixes
82        emit NewPublicSuffixList(address(suffixes));

/// @audit resolver
116            if (resolver == address(0)) {

/// @audit  suffixes
168        if (!suffixes.isPublicSuffix(domain)) {

/// @audit  previousRegistrar
187        if (owner == address(0) || owner == previousRegistrar) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

/// @audit  a
145        if (a != 0) {

/// @audit  b
148        if (b != 0) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

192            if (iter.class != DNSCLASS_IN) {

239        if (proofRR.dnstype == DNSTYPE_DS) {

241        } else if (proofRR.dnstype == DNSTYPE_DNSKEY) {

312        if (dnskey.flags & DNSKEY_FLAG_ZONEKEY == 0) {

341            if (iter.dnstype != DNSTYPE_DNSKEY) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [G-05] Stack variable used as a cheaper cache for `state variables` is `only used once`

### Summary

if a state variable used once use instead stack variable

### Details

There are **4** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol

29  uint16 constant CLASS_INET = 1;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L29

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

27    uint16 constant DNSCLASS_IN = 1;

29    uint16 constant DNSTYPE_DS = 43;

32    uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [G-06] Not Using the named `return` variables when a function returns, `WASTES DEPLOYMENT GAS`

### Details

There are **34** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/RecordParser.sol

21        returns (bytes memory key, bytes memory value, uint256 nextOffset)
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L21

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

136    ) internal returns (bytes32 parentNode, bytes32 labelHash, address addr) {

166    function enableNode(bytes memory domain) public returns (bytes32 node) {

177    ) internal returns (bytes32 node) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol

17    ) internal pure returns (bytes32 ret) {

182    ) internal pure returns (uint8 ret) {

195    ) internal pure returns (uint16 ret) {

211    ) internal pure returns (uint32 ret) {

227    ) internal pure returns (bytes32 ret) {

243    ) internal pure returns (bytes20 ret) {

264    ) internal pure returns (bytes32 ret) {

```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol

44    ) internal pure returns (bytes memory ret) {

96    ) internal pure returns (SignedSet memory self) {

139    ) internal pure returns (RRIterator memory ret) {

226    ) internal pure returns (DNSKEY memory self) {

252    ) internal pure returns (DS memory self) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

68    ) internal pure returns (uint256[3] memory P) {

82    ) internal pure returns (uint256[3] memory P) {

96    ) internal pure returns (uint256 x1, uint256 y1) {

109        returns (uint256 x, uint256 y, uint256 z)

117    function zeroAffine() internal pure returns (uint256 x, uint256 y) {

127    ) internal pure returns (bool isZero) {

163    ) internal pure returns (uint256 x1, uint256 y1, uint256 z1) {

215    ) internal pure returns (uint256 x2, uint256 y2, uint256 z2) {

253    ) private pure returns (uint256 x2, uint256 y2, uint256 z2) {

339    ) internal pure returns (uint256 x1, uint256 y1) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

11    ) internal view returns (bool success, bytes memory output) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L11

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

94        returns (bytes memory rrs, uint32 inception)

115        returns (bytes memory rrs, uint32 inception)

144    ) internal view returns (RRUtils.SignedSet memory rrset) {

184    ) internal pure returns (bytes memory name) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

```solidity
File: /contracts/dnssec-oracle/SHA1.sol

6    function sha1(bytes memory data) internal pure returns (bytes20 ret) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L6

```solidity
File: /contracts/utils/NameEncoder.sol

11    ) internal pure returns (bytes memory dnsName, bytes32 node) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L11

```solidity
File: /contracts/utils/HexUtils.sol

15    ) internal pure returns (bytes32 r, bool valid) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L15

## [G-07] Can make the variable outside the loop to save gas

### Summary

Consider making the stack variables before the loop which gonna save gas

### Details

There are **11** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/DNSClaimChecker.sol

/// @audi  bool found     declare variable outside from loop
35            bool found;

36            address addr;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol

85            bytes memory rrname = RRUtils.readName(iter.data, iter.offset);
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L85

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol

78            uint256 a;

79            uint256 b;

342            bytes1 char = self[off + i];
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol

385                uint256 word;

390                    uint256 unused = 256 - (data.length - i) * 8;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

261            bytes memory proofName = proof.name();

266            bytes memory keyrdata = proof.rdata();

345            bytes memory keyrdata = iter.rdata();
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [G-08] Public functions not callled by the contract should be declared external instead

### Details

There are **5** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

80    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {

90    function proveAndClaim(

101    function proveAndClaimWithResolver(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

64    function setAlgorithm(uint8 id, Algorithm algo) public owner_only {

75    function setDigest(uint8 id, Digest digest) public owner_only {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [G-09] Use assembly to check for address(0)

### Details

There are **7** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol

102            if (dnsresolver != address(0)) {

197        if (resolver == address(0)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

115        if (addr != address(0)) {

116            if (resolver == address(0)) {

187        if (owner == address(0) || owner == previousRegistrar) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

317        if (address(algorithm) == address(0)) {

420        if (address(digests[digesttype]) == address(0)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [G-10] The result of function calls should be cached rather than re-calling the function

### Details

There are **22** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/DNSClaimChecker.sol

34            if (iter.name().compareNames(buf.buf) != 0) continue;

72        if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false); // 0x613d3078 == 'a=0x'
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol

87                !rrname.equals(name) ||

104                    IERC165(dnsresolver).supportsInterface(

115                    IERC165(dnsresolver).supportsInterface(

144        if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

152        if (!RRUtils.serialNumberGte(inception, inceptions[node])) {

168        if (!suffixes.isPublicSuffix(domain)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol

279        if (self.equals(other)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

169        if (isZeroCurve(x0, y0)) {

221        if (isZeroCurve(x0, y0)) {

396        if (!isOnCurve(Q[0], Q[1])) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

149        if (name.labelCount(0) != rrset.labels) {

160        if (!RRUtils.serialNumberGte(rrset.expiration, uint32(now))) {

166        if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {

233        if (!name.isSubdomainOf(rrset.signerName)) {

262            if (!proofName.equals(rrset.signerName)) {

271            if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {

350            if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {

353                    verifyKeyWithDS(rrset.signerName, proof, dnskey, keyrdata)

382            if (!proofName.equals(keyname)) {

401            if (verifyDSHash(ds.digestType, buf.buf, ds.digest)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

## [G-11] State variables only set in the constructor should be declared `immutalbe`

### Summary

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

### Details

There are **2** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol

/// @audit immutable
39    string public gatewayURL;

46        gatewayURL = _gatewayURL;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L39

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

/// @audit  immutable
28     PublicSuffixList public suffixes;

65        suffixes = _suffixes;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L65

## [G-12] Using storage instead of memory for structs/arrays saves gas

### Summary

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from
storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array.

If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to
be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read.

The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

### Details

There are **1** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

/// @audit  memory      -->  storage
119            RRUtils.SignedSet memory rrset = validateSignedSet(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L119

## [G-13] Duplicated if() checks should be refactored to a modifier or function

### Details

There are **3** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol

28            if (labelLen == 0) {
64            if (labelLen == 0) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L28

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

169        if (isZeroCurve(x0, y0)) {
221        if (isZeroCurve(x0, y0)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L169

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

271            if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {
350            if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L271

## [G-14] Use constants instead of `type(uintx).max`

### Summary

type(uint120).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

### Details

There are **4** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/RecordParser.sol

24        if (separator == type(uint256).max) {

33        if (terminator == type(uint256).max) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol

88                    mask = type(uint256).max;

398        return type(uint256).max;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

## [G-15] Use nested if and, avoid multiple check combinations

### Summary

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

### Details

There are **3** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol

178        if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178

```solidity
File: /contracts/dnssec-oracle/RRUtils.sol

304        while (counts > 0 && !self.equals(off, other, otheroff)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

128        if (x0 == 0 && y0 == 0) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128

### Mitigation

```solidity
File: /contracts/dnssec-oracle/algorithms/EllipticCurve.sol

-128        if (x0 == 0 && y0 == 0) {

+           if (x0 == 0) {
+               if ( y0 == 0) {...}
+        }
```

## [G-16] Use `assembly` to write address storage values

### Details

There are **2** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

62        previousRegistrar = _previousRegistrar;

63        resolver = _resolver;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

### Mitigation

```solidity
File: /contracts/dnsregistrar/DNSRegistrar.sol

   constructor(
        address _previousRegistrar,,,,,){

            assembly {
                sstore(previousRegistrar.slot, _previousRegistrar)
            }
        }
```

## [G-17] Multiple accesses of a `mapping/array` should use a local variable cache

### Summary

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory/calldata.

### Details

There are **3** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/dnsregistrar/OffchainDNSResolver.sol

/// @audit  namorOrAddress[idx] ,  nameOrAddress[idx + 1]
178        if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L178

```solidity
File: /contracts/dnssec-oracle/BytesUtils.sol

/// @audit  self[idx]
394            if (self[idx] == needle) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L394

```solidity
File: /contracts/dnssec-oracle/DNSSECImpl.sol

/// @audit digests[digesttype]
420        if (address(digests[digesttype]) == address(0)) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420
