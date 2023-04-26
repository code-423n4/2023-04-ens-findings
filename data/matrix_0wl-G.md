## Gas Optimizations

|        | Issue                                                                                                                   |
| ------ | :---------------------------------------------------------------------------------------------------------------------- |
| GAS-1  | Use assembly to check for `address(0)`                                                                                  |
| GAS-2  | <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP                                                      |
| GAS-3  | Use Custom Errors                                                                                                       |
| GAS-4  | DIV BY 0                                                                                                                |
| GAS-5  | DOS WITH BLOCK GAS LIMIT                                                                                                |
| GAS-6  | USE FUNCTION INSTEAD OF MODIFIERS                                                                                       |
| GAS-7  | IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED |
| GAS-8  | INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS                                  |
| GAS-9  | MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL                                                            |
| GAS-10 | THE INCREMENT IN FOR LOOP POSTCONDITION CAN BE MADE UNCHECKED                                                           |
| GAS-11 | STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE                                                |
| GAS-12 | USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS                                                            |
| GAS-13 | TERNARY OPERATION IS CHEAPER THAN IF-ELSE STATEMENT                                                                     |
| GAS-14 | Use != 0 instead of > 0 for unsigned integer comparison                                                                 |
| GAS-15 | USE BYTES32 INSTEAD OF STRING                                                                                           |
| GAS-16 | `internal` functions not called by the contract should be removed                                                       |

### [GAS-1] Use assembly to check for `address(0)`

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

115:         if (addr != address(0)) {

116:             if (resolver == address(0)) {

187:         if (owner == address(0) || owner == previousRegistrar) {

188:             if (parentNode == bytes32(0)) {

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

102:             if (dnsresolver != address(0)) {

197:         if (resolver == address(0)) {

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

317:         if (address(algorithm) == address(0)) {

420:         if (address(digests[digesttype]) == address(0)) {

```

### [GAS-2] <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

#### Description:

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

[Source](https://code4rena.com/reports/2022-12-backed#g14--arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop)

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

118:         for (uint256 i = 0; i < input.length; i++) {

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

384:             for (uint256 i = 0; i < data.length + 31; i += 32) {

```

### [GAS-3] Use Custom Errors

#### Description:

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.
[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

76:         require(msg.sender == owner);

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

18:         require(offset + len <= self.length);

196:         require(idx + 2 <= self.length);

212:         require(idx + 4 <= self.length);

228:         require(idx + 32 <= self.length);

244:         require(idx + 20 <= self.length);

265:         require(len <= 32);

266:         require(idx + len <= self.length);

305:         require(offset + len <= self.length);

337:         require(len <= 52);

343:             require(char >= 0x30 && char <= 0x7A);

345:             require(decoded <= 0x20);

373:             revert();

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

381:             require(data.length <= 8192, "Long keys not permitted");

```

```solidity
File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

33:         require(data.length == 64, "Invalid p256 signature length");

40:         require(data.length == 68, "Invalid p256 key length");

```

```solidity
File: contracts/dnssec-oracle/digests/SHA1Digest.sol

17:         require(hash.length == 20, "Invalid sha1 hash length");

```

```solidity
File: contracts/dnssec-oracle/digests/SHA256Digest.sol

16:         require(hash.length == 32, "Invalid sha256 hash length");

```

```solidity
File: contracts/utils/HexUtils.sol

20:                 revert(0, 0)

```

### [GAS-4] DIV BY 0

#### Description:

Division by 0 can lead to accidentally revert.

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

52:                 q = r1 / r2;

```

### [GAS-5] DOS WITH BLOCK GAS LIMIT

#### Description:

When smart contracts are deployed or functions inside them are called, the execution of these actions always requires a certain amount of gas, based of how much computation is needed to complete them. The Ethereum network specifies a block gas limit and the sum of all transactions included in a block can not exceed the threshold.

Programming patterns that are harmless in centralized applications can lead to Denial of Service conditions in smart contracts when the cost of executing a function exceeds the block gas limit. Modifying an array of unknown size, that increases in size over time, can lead to such a Denial of Service condition.

[Code4arena example](https://swcregistry.io/docs/SWC-128)

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

118:                 for (uint256 i = 0; i < input.length; i++) {

```

#### Recommended Mitigation Steps:

Caution is advised when you expect to have large arrays that grow over time. Actions that require looping across the entire data structure should be avoided.

If you absolutely must loop over an array of unknown size, then you should plan for it to potentially take multiple blocks, and therefore require multiple transactions.

### [GAS-6] USE FUNCTION INSTEAD OF MODIFIERS

#### Description:

[Code4arena example](https://code4rena.com/reports/2022-10-inverse#02-use-function-instead-of-modifiers-4-instances)

[Code4arena example](https://code4rena.com/reports/2022-09-frax#g-02-use-function-instead-of-modifiers-4-instances)

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

73:     modifier onlyOwner() {

```

### [GAS-7] IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

#### Description:

If a variable is not set/initialized, the default value is assumed (0, false, 0x0 … depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

77:         for (uint256 idx = 0; idx < shortest; idx += 32) {

339:         uint256 ret = 0;

341:         for (uint256 i = 0; i < len; i++) {

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

118:         for (uint256 i = 0; i < input.length; i++) {

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

59:         uint256 count = 0;

72:     uint256 constant RRSIG_TYPE = 0;

210:     uint256 constant DNSKEY_FLAGS = 0;

236:     uint256 constant DS_KEY_TAG = 0;

263:         uint256 off = 0;

384:             for (uint256 i = 0; i < data.length + 31; i += 32) {

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

325:         for (uint256 i = 0; i < exp; i++) {

```

```solidity
File: contracts/utils/NameEncoder.sol

12:         uint8 labelLength = 0;

```

### [GAS-8] INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS

#### Description:

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

150:     function done(RRIterator memory iter) internal pure returns (bool) {

187:     function name(RRIterator memory iter) internal pure returns (bytes memory) {

353:     function computeKeytag(bytes memory data) internal pure returns (uint16) {

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

6:     function sha1(bytes memory data) internal pure returns (bytes20 ret) {

```

### [GAS-9] MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

73:     modifier onlyOwner() {

```

### [GAS-10] THE INCREMENT IN FOR LOOP POSTCONDITION CAN BE MADE UNCHECKED

#### Description:

This is only relevant if you are using the default solidity checked arithmetic.

The for loop postcondition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks.One can manually do this.

[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

341:         for (uint256 i = 0; i < len; i++) {

393:         for (uint256 idx = off; idx < off + len; idx++) {

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

118:         for (uint256 i = 0; i < input.length; i++) {

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

325:         for (uint256 i = 0; i < exp; i++) {

```

### [GAS-11] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

#### Description:

Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD. Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

65:             suffixes = _suffixes;

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

43:     gatewayURL = _gatewayURL;

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

55:     anchors = _anchors;

```

### [GAS-12] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

#### Description:

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read.

The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

92:         DNSSEC.RRSetWithSignature[] memory input

103:         DNSSEC.RRSetWithSignature[] memory input,

135:         DNSSEC.RRSetWithSignature[] memory input

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

26:     ) external returns (DNSSEC.RRSetWithSignature[] memory);

53:         string[] memory urls = new string[](1);

73:         DNSSEC.RRSetWithSignature[] memory rrsets = abi.decode(

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

88:         RRSetWithSignature[] memory input

108:         RRSetWithSignature[] memory input,

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

68:     ) internal pure returns (uint256[3] memory P) {

82:     ) internal pure returns (uint256[3] memory P) {

388:         uint256[2] memory rs,

389:         uint256[2] memory Q

408:         uint256[3] memory P = addAndReturnProjectivePoint(x1, y1, x2, y2);

```

```solidity
File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

32:     ) internal pure returns (uint256[2] memory) {

39:     ) internal pure returns (uint256[2] memory) {

```

### [GAS-13] TERNARY OPERATION IS CHEAPER THAN IF-ELSE STATEMENT

#### Description:

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.
Note that this optimization seems to be dependent on usage of a more recent Solidity version. The following gas savings are on version 0.8.17.
[Code4arena example](https://code4rena.com/reports/2022-10-traderjoe/#g-03-ternary-operation-is-cheaper-than-if-else-statement)

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

123:                if (ok) {
                        return ret;
                    } else {
                        revert CouldNotResolve(name);
                    }

216:    if (separator < lastIdx) {
            parentNode = textNamehash(name, separator + 1, lastIdx);
        } else {
            separator = lastIdx;
        }

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

87:             if (shortest - idx >= 32) {
                    mask = type(uint256).max;
                } else {
                    mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
                }

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol


234:        if (t0 == t1) {
                return twiceProj(x0, y0, z0);
            } else {
                return zeroProj();
            }
```

### [GAS-14] Use != 0 instead of > 0 for unsigned integer comparison

#### Description:

To update this with using at least 0.8.6 there is no difference in gas usage with!= 0 or > 0.
[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

304:         while (counts > 0 && !self.equals(off, other, otheroff)) {

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

361:         while (scalar > 0) {

```

### [GAS-15] USE BYTES32 INSTEAD OF STRING

#### Description:

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

16:     string[] urls,

39:     string public gatewayURL;

43:     constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {

53:         string[] memory urls = new string[](1);

```

```solidity
File: contracts/utils/NameEncoder.sol

10:         string memory name

```

### [GAS-16] `internal` functions not called by the contract should be removed

#### Description:

If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

19:     function getOwnerAddress(

```

```solidity
File: contracts/dnsregistrar/RecordParser.sol

14:     function readKeyValue(

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

179:     function readUint8(

192:     function readUint16(

208:     function readUint32(

224:     function readBytes32(

240:     function readBytes20(

260:     function readBytesN(

300:     function substring(

332:     function base32HexDecodeWord(

387:     function find(

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

94:     function readSignedSet(

111:     function rrs(

150:     function done(RRIterator memory iter) internal pure returns (bool) {

187:     function name(RRIterator memory iter) internal pure returns (bytes memory) {

200:     function rdata(

222:     function readDNSKEY(

248:     function readDS(

259:     function isSubdomainOf(

275:     function compareNames(

332:     function serialNumberGte(

353:     function computeKeytag(bytes memory data) internal pure returns (uint16) {

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

6:     function sha1(bytes memory data) internal pure returns (bytes20 ret) {

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

316:     function multiplyPowerBase2(

377:     function multipleGeneratorByScalar(

```

```solidity
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

7:     function modexp(

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSAVerify.sol

14:     function rsarecover(

```

```solidity
File: contracts/utils/HexUtils.sol

68:     function hexToAddress(

```

```solidity
File: contracts/utils/NameEncoder.sol

9:     function dnsEncodeName(

```
