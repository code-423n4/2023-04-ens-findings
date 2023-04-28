## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
// place these constants right before functions since there is no contructor.
72:    uint256 constant RRSIG_TYPE = 0;
73:    uint256 constant RRSIG_ALGORITHM = 2;
74:    uint256 constant RRSIG_LABELS = 3;
75:    uint256 constant RRSIG_TTL = 4;
76:    uint256 constant RRSIG_EXPIRATION = 8;
77:    uint256 constant RRSIG_INCEPTION = 12;
78:    uint256 constant RRSIG_KEY_TAG = 16;
79:    uint256 constant RRSIG_SIGNER_NAME = 18;
210:    uint256 constant DNSKEY_FLAGS = 0;
211:    uint256 constant DNSKEY_PROTOCOL = 2;
212:    uint256 constant DNSKEY_ALGORITHM = 3;
213:    uint256 constant DNSKEY_PUBKEY = 4;
236:    uint256 constant DS_KEY_TAG = 0;
237:    uint256 constant DS_ALGORITHM = 2;
238:    uint256 constant DS_DIGEST_TYPE = 3;
239:    uint256 constant DS_DIGEST = 4;

// place these structs right before functions since there is no contructor.
81:    struct SignedSet {
120:    struct RRIterator {
215:    struct DNSKEY {
241:    struct DS {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
// place this constant with the state variables
322:    bytes constant base32HexTable =
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
// place this modifer before the constructor
73:    modifier onlyOwner() {
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

```solidity
// this public function is coming after one external function
107:    function verifyRRSet(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
// this private function should come after all the internal ones.
273:    function memcpy(uint256 dest, uint256 src, uint256 len) private pure {
```

---

### natSpec missing [3]

Some functions are missing @params or @returns. Specification Format.” These are written with a triple slash (///) or a double asterisk block(/** ... */) directly above function declarations or statements to generate documentation in JSON format for developers and end-users. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). These comments contain different types of tags:
- @title: A title that should describe the contract/interface @author: The name of the author (for contract, interface) 
- @notice: Explain to an end user what this does (for contract, interface, function, public state variable, event) 
- @dev: Explain to a developer any extra details (for contract, interface, function, state variable, event) 
- @param: Documents a parameter (just like in doxygen) and must be followed by parameter name (for function, event)
- @return: Documents the return variables of a contract’s function (function, public state variable)
- @inheritdoc: Copies all missing tags from the base function and must be followed by the contract name (for function, public state variable)
- @custom…: Custom tag, semantics is application-defined (for everywhere)

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

```solidity
4:  library HexUtils {

// @return missing
11:    function hexStringToBytes32(
68:    function hexToAddress(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol

```solidity
6:    library NameEncoder {

9:    function dnsEncodeName(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

```solidity
3:  library SHA1 {

6:    function sha1(bytes memory data) internal pure returns (bytes20 ret) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

```solidity
// @return missing
140:    function validateSignedSet(
181:    function validateRRs(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol

```solidity
12:    function verify(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol

```solidity
13:    function verify(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol

```solidity
6:    library RSAVerify {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

```solidity
13:    function verify(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

```solidity
3:  library ModexpPrecompile {

// @params and @return missing
7:    function modexp(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

```solidity
7:    contract P256SHA256Algorithm is Algorithm, EllipticCurve {

30:    function parseSignature(

37:    function parseKey(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
// @param and @return missing
40:    function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {
65:    function toProjectivePoint(
77:    function addAndReturnProjectivePoint(
92:    function toAffinePoint(
124:    function isZeroCurve(
137:    function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {
159:    function twiceProj(
208:    function addProj(
247:    function addProj2(
286:    function add(
302:    function twice(
316:    function multiplyPowerBase2(
335:    function multiplyScalar(
377:    function multipleGeneratorByScalar(
386:    function validateSignature(

// @return missing
106:    function zeroProj()
117:    function zeroAffine() internal pure returns (uint256 x, uint256 y) {

```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

```solidity
14:    function verify(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
81:    struct SignedSet {

94:    function readSignedSet(

111:    function rrs(

120:    struct RRIterator {

215:    struct DNSKEY {

222:    function readDNSKEY(

241:    struct DS {

248:    function readDS(

259:    function isSubdomainOf(

275:    function compareNames(

// @param and @return missing
332:    function serialNumberGte(

341:    function progress(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
3:    library BytesUtils {

273:    function memcpy(uint256 dest, uint256 src, uint256 len) private pure {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
40:    struct OwnerRecord {

47:    event Claim(

53:    event NewPublicSuffixList(address suffixes);

55:    constructor(

80:    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {

101:    function proveAndClaimWithResolver(

133:    function _claim(

166:    function enableNode(bytes memory domain) public returns (bytes32 node) {

174:    function _enableNode(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

```solidity
6:  library RecordParser {

// @return missing
14:    function readKeyValue(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
14:   error OffchainLookup(

22:   interface IDNSGateway {

23:    function resolve(

32:   contract OffchainDNSResolver is IExtendedResolver {

43:    constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {

49:    function resolve(

65:    function resolveCallback(

136:    function parseRR(

162:    function readTXT(

173:    function parseAndResolve(

190:    function resolveName(

// @return missing
209:    function textNamehash(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

```solidity
10:   library DNSClaimChecker {

19:    function getOwnerAddress(

46:    function parseRR(

66:    function parseString(
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE


https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

```solidity
//  private and internal `functions` should preppend with `underline`
11:    function hexStringToBytes32(
68:    function hexToAddress(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol

```solidity
//  private and internal `functions` should preppend with `underline`
9:    function dnsEncodeName(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

```solidity
// private and internal `functions` should preppend with `underline`
6:    function sha1(bytes memory data) internal pure returns (bytes20 ret) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

```solidity
// private and internal `functions` should preppend with `underline`
140:    function validateSignedSet(
181:    function validateRRs(
225:    function verifySignature(
254:    function verifyWithKnownKey(
285:    function verifySignatureWithKey(
330:    function verifyWithDS(
373:    function verifyKeyWithDS(
415:    function verifyDSHash(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

```solidity
// private and internal `functions` should preppend with `underline`
7:    function modexp(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

```solidity
// private and internal `functions` should preppend with `underline`
30:    function parseSignature(
37:    function parseKey(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
40:    function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {
65:    function toProjectivePoint(
77:    function addAndReturnProjectivePoint(
92:    function toAffinePoint(
106:    function zeroProj()
117:    function zeroAffine() internal pure returns (uint256 x, uint256 y) {
124:    function isZeroCurve(
137:    function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {
159:    function twiceProj(
208:    function addProj(
247:    function addProj2(
286:    function add(
302:    function twice(
316:    function multiplyPowerBase2(
335:    function multiplyScalar(
377:    function multipleGeneratorByScalar(
386:    function validateSignature(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
// private and internal `functions` should preppend with `underline`
19:    function nameLength(
41:    function readName(
55:    function labelCount(
94:    function readSignedSet(
111:    function rrs(
136:    function iterateRRs(
150:    function done(RRIterator memory iter) internal pure returns (bool) {
158:    function next(RRIterator memory iter) internal pure {
187:    function name(RRIterator memory iter) internal pure returns (bytes memory) {
200:    function rdata(
222:    function readDNSKEY(
248:    function readDS(
259:    function isSubdomainOf(
275:    function compareNames(
332:    function serialNumberGte(
341:    function progress(
253:    function computeKeytag(bytes memory data) internal pure returns (uint16) {
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
// private and internal `functions` should preppend with `underline`
13:    function keccak(
32:    function compare(
52:    function compare(
111:    function equals(
129:    function equals(
148:    function equals(
164:    function equals(
179:    function readUint8(
192:    function readUint16(
208:    function readUint32(
224:    function readBytes32(
240:    function readBytes20(
260:    function readBytesN(
273:    function memcpy(uint256 dest, uint256 src, uint256 len) private pure {
300:    function substring(
332:    function base32HexDecodeWord(
387:    function find(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

```solidity
// private and internal `functions` should preppend with `underline`
14:    function readKeyValue(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
// private and internal `functions` should preppend with `underline`
136:    function parseRR(
162:    function readTXT(
173:    function parseAndResolve(
190:    function resolveName(
290:    function textNamehash(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

```solidity
// private and internal `functions` should preppend with `underline`
19:    function getOwnerAddress(
46:    function parseRR(
66:    function parseString(
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol

```solidity
// version differs from the rest of the repo, also lock this pragma to a specific version for a safer code. remove the `^`
2:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol

```solidity
// version differs from the rest of the repo, also lock this pragma to a specific version for a safer code. remove the `^`
2:  pragma solidity ^0.8.13;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `>=`
1:  pragma solidity >=0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
2:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:    pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:   pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:    pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
1:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
3:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol

```solidity
// version differs from the rest of the repo, also lock this pragma to a specific version for a safer code. remove the `^`
2:  pragma solidity ^0.8.11;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
2:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

```solidity
// lock this pragma to a specific version for a safer code. remove the `^`
2:  pragma solidity ^0.8.4;
```

---

### Observations [6]

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

```solidity
// License not defined
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

```solidity
// change these constants to UPPERCASE
21:    uint256 constant a =
23:    uint256 constant b =
25:    uint256 constant gx =
27:    uint256 constant gy =
29:    uint256 constant p =
31:    uint256 constant n =
34:    uint256 constant lowSmax =
```