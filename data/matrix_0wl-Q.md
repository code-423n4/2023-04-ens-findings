## Non Critical Issues

|       | Issue                                                                   |
| ----- | :---------------------------------------------------------------------- |
| NC-1  | ADD TO INDEXED PARAMETER FOR COUNTABLE EVENTS                           |
| NC-2  | GENERATE PERFECT CODE HEADERS EVERY TIME                                |
| NC-2  | SOLIDITY COMPILER VERSIONS MISMATCH                                     |
| NC-4  | FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION           |
| NC-5  | MISSING EVENT FOR CRITICAL PARAMETER CHANGE                             |
| NC-6  | NO SAME VALUE INPUT CONTROL                                             |
| NC-7  | OMISSIONS IN EVENTS                                                     |
| NC-8  | SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC                      |
| NC-9  | FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE     |
| NC-10 | BETTER TO HAVE CONFIG FACET                                             |
| NC-11 | USE A MORE RECENT VERSION OF SOLIDITY                                   |
| NC-12 | FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS |
| NC-13 | LINES ARE TOO LONG                                                      |

### [NC-1] ADD TO INDEXED PARAMETER FOR COUNTABLE EVENTS

#### Description:

Add to indexed parameter for countable Events

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

66:         emit AlgorithmUpdated(id, address(algo));

77:         emit DigestUpdated(id, address(digest));

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

4:     event Debug(bytes32 x);

```

### [NC-2] GENERATE PERFECT CODE HEADERS EVERY TIME

#### Description:

I recommend using header for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [NC-3] SOLIDITY COMPILER VERSIONS MISMATCH

#### Description:

The project is compiled with different versions of Solidity, which is not recommended because it can lead to undefined behaviors.

It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

3: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

2: pragma solidity ^0.8.4;

3: pragma experimental ABIEncoderV2;

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/digests/SHA1Digest.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/digests/SHA256Digest.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/utils/HexUtils.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/utils/NameEncoder.sol

2: pragma solidity ^0.8.13;

```

### [NC-4] FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION

#### Description:

https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

Use solidity pragma version min. 0.8.13

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

3: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

2: pragma solidity ^0.8.4;

3: pragma experimental ABIEncoderV2;

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/digests/SHA1Digest.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/digests/SHA256Digest.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/utils/HexUtils.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/utils/NameEncoder.sol

2: pragma solidity ^0.8.13;

```

### [NC-5] MISSING EVENT FOR CRITICAL PARAMETER CHANGE

#### Description:

Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

80:     function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {

82:         emit NewPublicSuffixList(address(suffixes));

98:         ens.setSubnodeOwner(rootNode, labelHash, addr);

114:         ens.setSubnodeRecord(rootNode, labelHash, owner, resolver, 0);

121:             AddrResolver(resolver).setAddr(node, addr);

190:                 root.setSubnodeOwner(label, address(this));

191:                 ens.setResolver(node, resolver);

193:                 ens.setSubnodeRecord(

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

64:     function setAlgorithm(uint8 id, Algorithm algo) public owner_only {

66:         emit AlgorithmUpdated(id, address(algo));

75:     function setDigest(uint8 id, Digest digest) public owner_only {

77:         emit DigestUpdated(id, address(digest));

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

4:     event Debug(bytes32 x);

```

### [NC-6] NO SAME VALUE INPUT CONTROL

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

62:         previousRegistrar = _previousRegistrar;

63:         resolver = _resolver;

65:         suffixes = _suffixes;

67:         ens = _ens;

81:         suffixes = _suffixes;

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

44:         ens = _ens;

45:         oracle = _oracle;

46:         gatewayURL = _gatewayURL;

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

55:         anchors = _anchors;

```

### [NC-7] OMISSIONS IN EVENTS

#### Description:

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

The events should include the new value and old value where possible.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

66:         emit NewPublicSuffixList(address(suffixes));

82:         emit NewPublicSuffixList(address(suffixes));

```

### [NC-8] SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC

#### Description:

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

#### Exploit Scenario:

A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

#### **Proof Of Concept**

```solidity
File: hardhat.config.ts

  solidity: {
    compilers: [
      {
        version: '0.8.17',
        settings: {
          optimizer: {
            enabled: true,
            runs: 1300,
          },
        },
      },
```

### [NC-9] FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE

#### Description:

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private, within a grouping, place the view and pure functions last

### [NC-10] BETTER TO HAVE CONFIG FACET

Better to have config facet, in case some update is needed in the config.sol. Therefore, it is not necessary to redeploy the facets that imported config.

### [NC-11] USE A MORE RECENT VERSION OF SOLIDITY

#### Description:

For security, it is best practice to use the latest Solidity version. For the security fix list in the versions; https://github.com/ethereum/solidity/blob/develop/Changelog.md

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

3: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

2: pragma solidity ^0.8.4;

3: pragma experimental ABIEncoderV2;

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/digests/SHA1Digest.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/dnssec-oracle/digests/SHA256Digest.sol

1: pragma solidity ^0.8.4;

```

```solidity
File: contracts/utils/HexUtils.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: contracts/utils/NameEncoder.sol

2: pragma solidity ^0.8.13;

```

### [NC-12] FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

#### Description:

Solidity’s standard naming convention for internal and private functions and variables (apart from constants): the mixedCase format starting with an underscore (\_mixedCase starting with an underscore).

Solidity’s standard naming convention for internal and private constants variables: the snake_case format starting with an underscore (\_mixedCase starting with an underscore) and use ALL_CAPS for naming them.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

19:         function getOwnerAddress(

46:         function parseRR(

66:         function parseString(

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

136:        function parseRR(

162:         function readTXT(

173:         function parseAndResolve(

190:         function resolveName(

209:         function textNamehash(

```

```solidity
File: contracts/dnsregistrar/RecordParser.sol

14:         function readKeyValue(

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

13:        function keccak(

32:         function compare(

52:         function compare(

111:        function equals(

129:         function equals(

148:         function equals(

164:         function equals(

179:         function readUint8(

192:         function readUint16(

208:         function readUint32(

224:         function readBytes32(

240:         function readBytes20(

260:         function readBytesN(

273:     function memcpy(uint256 dest, uint256 src, uint256 len) private pure {

300:     function substring(

332:         function base32HexDecodeWord(

387:         function find(

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

140:         function validateSignedSet(

181:        function validateRRs(

225:         function verifySignature(

254:         function verifyWithKnownKey(

285:        function verifySignatureWithKey(

330:         function verifyWithDS(

373:         function verifyKeyWithDS(

415:        function verifyDSHash(

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

19:         function nameLength(

41:         function readName(

55:         function labelCount(

94:         function readSignedSet(

111:         function rrs(

136:         function iterateRRs(

150:     function done(RRIterator memory iter) internal pure returns (bool) {

158:     function next(RRIterator memory iter) internal pure {

187:     function name(RRIterator memory iter) internal pure returns (bytes memory) {

200:         function rdata(

222:         function readDNSKEY(

248:         function readDS(

259:         function isSubdomainOf(

275:         function compareNames(

332:        function serialNumberGte(

341:         function progress(

353:     function computeKeytag(bytes memory data) internal pure returns (uint16) {

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

6:     function sha1(bytes memory data) internal pure returns (bytes20 ret) {

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

40:     function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {

65:         function toProjectivePoint(

77:         function addAndReturnProjectivePoint(

92:         function toAffinePoint(

107:            function zeroProj()

117:     function zeroAffine() internal pure returns (uint256 x, uint256 y) {

124:         function isZeroCurve(

137:     function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {

159:         function twiceProj(

208:         function addProj(

247:         function addProj2(

286:         function add(

302:        function twice(

316:         function multiplyPowerBase2(

335:         function multiplyScalar(

377:         function multipleGeneratorByScalar(

386:         function validateSignature(

```

```solidity
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

7:        function modexp(

```

```solidity
File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

30:         function parseSignature(

37:        function parseKey(

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSAVerify.sol

14:         function rsarecover(

```

```solidity
File: contracts/utils/HexUtils.sol

11:         function hexStringToBytes32(

68:         function hexToAddress(

```

```solidity
File: contracts/utils/NameEncoder.sol

9:         function dnsEncodeName(

```

### [NC-13] LINES ARE TOO LONG

#### Description:

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

[Reference](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length)

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

72:         if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false); // 0x613d3078 == 'a=0x'

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

64:             revert OffsetOutOfBoundsError(otheroffset + otherlen, other.length);

248:                 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF000000000000000000000000

323:         hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F";

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

187:     function name(RRIterator memory iter) internal pure returns (bytes memory) {

395:                         0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>

398:                     0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);

402:                     0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +

404:                     0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>

408:                     0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +

410:                     0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>

415:                     0x00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF) +

417:                     0xFFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000) >>

421:                     0x0000000000000000FFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF) +

423:                     0xFFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF0000000000000000) >>

427:                     0x00000000000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF) +

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

78:                             0xFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFE

82:                             0x0000000100000001000000010000000100000001000000010000000100000001

105:                             0xFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFC

109:                             0x0000000300000003000000030000000300000003000000030000000300000003

189:                             0x100000000000000000000000000000000000000000000000000000000

205:                                 and(div(x, 0x400000000000000000000), 0x3FFFFFFF)

```

## Low Issues

|      | Issue                                                                    |
| ---- | :----------------------------------------------------------------------- |
| L-1  | Arbitrary Jump with Function Type Variable                               |
| L-2  | DOS WITH BLOCK GAS LIMIT                                                 |
| L-3  | DECODING AN IPFS HASH USING A FIXED HASH FUNCTION AND LENGTH OF THE HASH |
| L-4  | FUNCTIONS CREATE DIRTY BITS                                              |
| L-5  | DON’T USE OWNER AND TIMELOCK                                             |
| L-6  | Initializers could be front-run                                          |
| L-7  | UNIFY RETURN CRITERIA                                                    |
| L-8  | REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR                               |
| L-9  | Timestamp Dependence                                                     |
| L-10 | ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS                              |
| L-11 | UNSAFE CAST                                                              |
| L-12 | UNSPECIFIC COMPILER VERSION PRAGMA                                       |
| L-13 | Void constructor                                                         |
| L-14 | NOT USING THE LATEST VERSION OF OPENZEPPELIN FROM DEPENDENCIES           |

### [L-1] Arbitrary Jump with Function Type Variable

#### Description:

Function types are supported in Solidity. This means that a variable of type function can be assigned to a function with a matching signature. The function can then be called from the variable just like any other function. Users should not be able to change the function variable, but in some cases this is possible.

If the smart contract uses certain assembly instructions, mstore for example, an attacker may be able to point the function variable to any other function. This may give the attacker the ability to break the functionality of the contract, and perhaps even drain the contract funds.

Since inline assembly is a way to access the EVM at a low level, it bypasses many important safety features. So it's important to only use assembly if it is necessary and properly understood.

[Source](https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/arbitrary-jump-function-type.md)

[SWC Registry](https://swcregistry.io/docs/SWC-127)

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

276:             assembly {
                  mstore(dest, mload(src))
            }

286:             assembly {
                let srcpart := and(mload(src), not(mask))
                let destpart := and(mload(dest), mask)
                mstore(dest, or(destpart, srcpart))
            }

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

41:                        mstore(scratch, readword(data, i, len))

42:                mstore(add(scratch, 32), readword(data, add(i, 32), len))

53:                    mstore(

85:               mstore(add(scratch, j), temp)

112:                    mstore(add(scratch, j), temp)

```

### [L-2] DOS WITH BLOCK GAS LIMIT

#### Description:

Programming patterns such as looping over arrays of unknown size may lead to DoS when the gas cost of execution exceeds the block gas limit.

Reference: https://swcregistry.io/docs/SWC-128

This loops could drain all user gas and revert.

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

118:                 for (uint256 i = 0; i < input.length; i++) {

```

### [L-3] DECODING AN IPFS HASH USING A FIXED HASH FUNCTION AND LENGTH OF THE HASH

#### Description:

An IPFS hash specifies the hash function and length of the hash in the first two bytes of the hash. The first two bytes are `0x1220`, where 12 denotes that this is the SHA256 hash function and 20 is the length of the hash in bytes (32 bytes).

Although SHA256 is 32 bytes and is currently the most common IPFS hash function, other content could use a hash function that is larger than 32 bytes. The current implementation limits the usage to the SHA256 hash function and a hash length of 32 bytes.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

43:         return (address(0x0), false);

63:         return (address(0x0), false);

72:         if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false); // 0x613d3078 == 'a=0x'

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

198:             ret := and(mload(add(add(self, 2), idx)), 0xFFFF)

214:             ret := and(mload(add(add(self, 4), idx)), 0xFFFFFFFF)

248:                 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF000000000000000000000000

343:             require(char >= 0x30 && char <= 0x7A);

343:             require(char >= 0x30 && char <= 0x7A);

344:             decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);

345:             require(decoded <= 0x20);

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

32:     uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

395:                         0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>

398:                     0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);

402:                     0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +

404:                     0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>

408:                     0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +

410:                     0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>

415:                     0x00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF) +

417:                     0xFFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000) >>

421:                     0x0000000000000000FFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF) +

423:                     0xFFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF0000000000000000) >>

427:                     0x00000000000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF) +

429:             ac1 += (ac1 >> 16) & 0xFFFF;

```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

9:             let scratch := mload(0x40)

16:             let totallen := add(and(add(len, 1), 0xFFFFFFFFFFFFFFC0), 64)

22:             let h := 0x6745230100EFCDAB890098BADCFE001032547600C3D2E1F0

47:                     mstore8(add(scratch, sub(len, i)), 0x80)

78:                             0xFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFEFFFFFFFE

81:                             div(temp, 0x80000000),

82:                             0x0000000100000001000000010000000100000001000000010000000100000001

105:                             0xFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFCFFFFFFFC

108:                             div(temp, 0x40000000),

109:                             0x0000000300000003000000030000000300000003000000030000000300000003

127:                             div(x, 0x100000000000000000000),

128:                             div(x, 0x10000000000)

130:                         f := and(div(x, 0x1000000000000000000000000000000), f)

131:                         f := xor(div(x, 0x10000000000), f)

132:                         k := 0x5A827999

137:                             div(x, 0x1000000000000000000000000000000),

138:                             div(x, 0x100000000000000000000)

140:                         f := xor(div(x, 0x10000000000), f)

141:                         k := 0x6ED9EBA1

146:                             div(x, 0x1000000000000000000000000000000),

147:                             div(x, 0x100000000000000000000)

149:                         f := and(div(x, 0x10000000000), f)

152:                                 div(x, 0x1000000000000000000000000000000),

153:                                 div(x, 0x100000000000000000000)

157:                         k := 0x8F1BBCDC

162:                             div(x, 0x1000000000000000000000000000000),

163:                             div(x, 0x100000000000000000000)

165:                         f := xor(div(x, 0x10000000000), f)

166:                         k := 0xCA62C1D6

172:                             0x80000000000000000000000000000000000000000000000

174:                         0x1F

178:                             div(x, 0x800000000000000000000000000000000000000),

179:                             0xFFFFFFE0

184:                     temp := add(and(x, 0xFFFFFFFF), temp)

189:                             0x100000000000000000000000000000000000000000000000000000000

194:                         div(x, 0x10000000000),

195:                         mul(temp, 0x10000000000000000000000000000000000000000)

200:                             0xFFFFFFFF00FFFFFFFF000000000000FFFFFFFF00FFFFFFFF

204:                                 and(div(x, 0x4000000000000), 0xC0000000),

204:                                 and(div(x, 0x4000000000000), 0xC0000000),

205:                                 and(div(x, 0x400000000000000000000), 0x3FFFFFFF)

205:                                 and(div(x, 0x400000000000000000000), 0x3FFFFFFF)

207:                             0x100000000000000000000

214:                     0xFFFFFFFF00FFFFFFFF00FFFFFFFF00FFFFFFFF00FFFFFFFF

223:                                     div(h, 0x100000000),

224:                                     0xFFFFFFFF00000000000000000000000000000000

227:                                     div(h, 0x1000000),

228:                                     0xFFFFFFFF000000000000000000000000

231:                             and(div(h, 0x10000), 0xFFFFFFFF0000000000000000)

231:                             and(div(h, 0x10000), 0xFFFFFFFF0000000000000000)

233:                         and(div(h, 0x100), 0xFFFFFFFF00000000)

233:                         and(div(h, 0x100), 0xFFFFFFFF00000000)

235:                     and(h, 0xFFFFFFFF)

237:                 0x1000000000000000000000000

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

22:         0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;

24:         0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;

26:         0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;

28:         0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;

30:         0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;

32:         0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;

35:         0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0;

```

```solidity
File: contracts/utils/HexUtils.sol

40:                 ascii := 0xff

52:                 if or(eq(byte1, 0xff), eq(byte2, 0xff)) {

52:                 if or(eq(byte1, 0xff), eq(byte2, 0xff)) {

73:         if (lastIdx - idx < 40) return (address(0x0), false);

```

#### Recommended Mitigation Steps:

Consider using a more generic implementation that can handle different hash functions and lengths and allow the user to choose.

### [L-4] FUNCTIONS CREATE DIRTY BITS

#### Description:

This explanation should be added in the NatSpec comments of this function that sends ether with call;

Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed. Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer.

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/SHA1.sol

7:         assembly {

```

```solidity
File: contracts/utils/HexUtils.sol

17:         assembly {

```

#### Recommended Mitigation Steps:

Add this comment to \_returnDust function:

`/// @dev Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer. Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.`

### [L-5] DON’T USE OWNER AND TIMELOCK

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

76:         require(msg.sender == owner);

```

### [L-6] Initializers could be front-run

#### Description:

Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

25:         buf.init(name.length + 5);

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

398:             buf.init(keyname.length + keyrdata.length);

```

### [L-7] UNIFY RETURN CRITERIA

In contracts, sometimes the name of the return variable is not defined and sometimes is, unifying the way of writing the code makes the code more uniform and readable.

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

127:     ) external pure override returns (bool) {

136:     ) internal returns (bytes32 parentNode, bytes32 labelHash, address addr) {

166:     function enableNode(bytes memory domain) public returns (bytes32 node) {

177:     ) internal returns (bytes32 node) {

```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

17:     ) internal pure returns (bytes32 ret) {

35:     ) internal pure returns (int256) {

59:     ) internal pure returns (int256) {

117:     ) internal pure returns (bool) {

134:     ) internal pure returns (bool) {

152:     ) internal pure returns (bool) {

167:     ) internal pure returns (bool) {

182:     ) internal pure returns (uint8 ret) {

195:     ) internal pure returns (uint16 ret) {

211:     ) internal pure returns (uint32 ret) {

227:     ) internal pure returns (bytes32 ret) {

243:     ) internal pure returns (bytes20 ret) {

264:     ) internal pure returns (bytes32 ret) {

304:     ) internal pure returns (bytes memory) {

336:     ) internal pure returns (bytes32) {

392:     ) internal pure returns (uint256) {

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

94:         returns (bytes memory rrs, uint32 inception)

115:         returns (bytes memory rrs, uint32 inception)

144:     ) internal view returns (RRUtils.SignedSet memory rrset) {

184:     ) internal pure returns (bytes memory name) {

290:     ) internal view returns (bool) {

378:     ) internal view returns (bool) {

419:     ) internal view returns (bool) {

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

22:     ) internal pure returns (uint256) {

44:     ) internal pure returns (bytes memory ret) {

58:     ) internal pure returns (uint256) {

96:     ) internal pure returns (SignedSet memory self) {

113:     ) internal pure returns (RRIterator memory) {

139:     ) internal pure returns (RRIterator memory ret) {

150:     function done(RRIterator memory iter) internal pure returns (bool) {

187:     function name(RRIterator memory iter) internal pure returns (bytes memory) {

202:     ) internal pure returns (bytes memory) {

226:     ) internal pure returns (DNSKEY memory self) {

252:     ) internal pure returns (DS memory self) {

262:     ) internal pure returns (bool) {

278:     ) internal pure returns (int256) {

335:     ) internal pure returns (bool) {

344:     ) internal pure returns (uint256) {

353:     function computeKeytag(bytes memory data) internal pure returns (uint16) {

```

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

40:     function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {

68:     ) internal pure returns (uint256[3] memory P) {

82:     ) internal pure returns (uint256[3] memory P) {

96:     ) internal pure returns (uint256 x1, uint256 y1) {

109:         returns (uint256 x, uint256 y, uint256 z)

117:     function zeroAffine() internal pure returns (uint256 x, uint256 y) {

127:     ) internal pure returns (bool isZero) {

137:     function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {

163:     ) internal pure returns (uint256 x1, uint256 y1, uint256 z1) {

215:     ) internal pure returns (uint256 x2, uint256 y2, uint256 z2) {

253:     ) private pure returns (uint256 x2, uint256 y2, uint256 z2) {

291:     ) internal pure returns (uint256, uint256) {

305:     ) internal pure returns (uint256, uint256) {

320:     ) internal pure returns (uint256, uint256) {

339:     ) internal pure returns (uint256 x1, uint256 y1) {

379:     ) internal pure returns (uint256, uint256) {

390:     ) internal pure returns (bool) {

```

#### Recommended Mitigation Steps:

add `{return x}` if you want to return the updated value or else remove `returns(uint)` from the `function(){}` if no value you wanted to return

### [L-8] REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR

The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory.

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

Error definitions should be added to the require block, not exceeding 32 bytes or we should use custom errors

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
File: contracts/utils/HexUtils.sol

20:                 revert(0, 0)

```

### [L-9] Timestamp Dependence

#### Description:

Contracts often need access to time values to perform certain types of functionality. Values such as block.timestamp can give you a sense of the current time or a time delta, however, they are not safe to use for most purposes.

In the case of block.timestamp, developers often attempt to use it to trigger time-dependent events. As Ethereum is decentralized, nodes can synchronize time only to some degree. Moreover, malicious miners can alter the timestamp of their blocks, especially if they can gain advantages by doing so. However, miners cant set a timestamp smaller than the previous one (otherwise the block will be rejected), nor can they set the timestamp too far ahead in the future. Taking all of the above into consideration, developers cant rely on the preciseness of the provided timestamp.

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

96:         return verifyRRSet(input, block.timestamp);

```

### [L-10] ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS

#### Description:

Low-level calls `call`/`delegatecall`/`staticcall` return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.

https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-callsn

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

122:                         .staticcall(query);

```

```solidity
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

24:             success := staticcall(

```

#### Recommended Mitigation Steps:

In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

### [L-11] UNSAFE CAST

#### Description:

Keep in mind that the version of solidity used, despite being greater than 0.8, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

183:         return uint8(self[idx]);

344:             decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);

```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

160:         if (!RRUtils.serialNumberGte(rrset.expiration, uint32(now))) {

161:             revert SignatureExpired(rrset.expiration, uint32(now));

166:         if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {

167:             revert SignatureNotValidYet(rrset.inception, uint32(now));

```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

430:             return uint16(ac1);

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

22:         uint16 exponentLen = uint16(key.readUint8(4));

```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

21:         uint16 exponentLen = uint16(key.readUint8(4));

```

#### Recommended Mitigation Steps:

Use a safeCast from Open Zeppelin or increase the type length.

### [L-12] UNSPECIFIC COMPILER VERSION PRAGMA

#### Description:

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.
https://swcregistry.io/docs/SWC-103

#### **Proof Of Concept**

```solidity
File: contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;

```

### [L-13] Void constructor

#### Description:

Calls to base contract constructors that are unimplemented leads to misplaced assumptions. Check if the constructor is implemented or remove call if not.

Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#void-constructor

#### **Proof Of Concept**

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

55:     constructor(

```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

43:     constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {

```

### [L-14] NOT USING THE LATEST VERSION OF OPENZEPPELIN FROM DEPENDENCIES

#### Description:

The package.json configuration file says that the project is using 4.4.2/4.3.1 etc. of OpenZeppelin which has a not last update version.

Patched versions for @openzeppelin/contracts and @openzeppelin/contracts-upgradeable is 4.4.1.

That is why @openzeppelin/contracts version 4.3.1 is vulnerable.

https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-9c22-pwxw-p6hx

#### **Proof Of Concept**

```solidity
File: package.json

59:    "@openzeppelin/contracts": "^4.1.0",
  },

```

#### Recommended Mitigation Steps:

Use patched versions. Latest non vulnerable version 4.4.1
