## Summary
### Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|L-01|SHA-1 is no longer considered secure, as it is vulnerable to collision attacks| 1 |
|L-02|Function Calls in Loop Could Lead to Denial of Service due to Array length not being checked | 1 |
|L-03|Missing Event for  initialize | 3 |
|L-04|Project Upgrade and Stop Scenario should be|All Contracts|
|L-05|Tests cannot be run on the `Ropsten` network and must be removed| 1 |
|N-06|Assembly Codes Specific – Should Have Comments | 15|
|N-07|For functions, follow Solidity standard naming conventions (internal function style rule)| 73 |
|N-08|Use SMTChecker | 1 |
|N-09|Repeated validation logic can be converted into a function modifier| 2 |
|N-10|Use a single file for all system-wide constants| 32 |
|N-11|Avoid _shadowing_ `inherited state variables|1|
|N-12|Use Fuzzing Test for math code bases | 1|
|N-13|I recommend that you choose it according to its purpose when determining the function names| All Contracts|

Total 13 issues

### [L-01] SHA-1 is no longer considered secure, as it is vulnerable to collision attacks.

SHA-1 is no longer considered secure, as it is vulnerable to collision attacks. It has been deprecated in favor of stronger hash functions, such as SHA-256 and SHA-3.


https://security.stackexchange.com/questions/162779/does-still-make-sense-to-use-sha1
https://www.computerworld.com/article/3173616/the-sha1-hash-function-is-now-completely-unsafe.html
https://security.googleblog.com/2017/02/announcing-first-sha1-collision.html?m=1


SHA-1 uses a 160-bit hash value to ensure the integrity of data by producing a unique message digest. However, as computing power increased, researchers found that the hash function could be exploited using a technique known as a "collision attack." A collision attack occurs when two different input messages produce the same hash value. Attackers can use this technique to generate fake or malicious data that has the same hash value as the original data.

In 2017, the National Institute of Standards and Technology (NIST) declared that SHA-1 should no longer be used for cryptographic purposes due to these vulnerabilities. Instead, the institute recommended the use of more secure algorithms such as SHA-256 or SHA-3.




```solidity
contracts/dnssec-oracle/SHA1.sol:
  2  
  3: library SHA1 {
  4      event Debug(bytes32 x);
  5  
  6:     function sha1(bytes memory data) internal pure returns (bytes20 ret) {
  7          assembly {

contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol:
   5  import "./RSAVerify.sol";
   6: import "@ensdomains/solsha1/contracts/SHA1.sol";
   7  
   8  /**
   9:  * @dev Implements the DNSSEC RSASHA1 algorithm.
  10   */
  11: contract RSASHA1Algorithm is Algorithm {
  12      using BytesUtils for *;

  43          // Verify it ends with the hash of our data
  44:         return ok && SHA1.sha1(data) == result.readBytes20(result.length - 20);
  45      }

contracts/dnssec-oracle/digests/SHA1Digest.sol:
   4  import "../BytesUtils.sol";
   5: import "@ensdomains/solsha1/contracts/SHA1.sol";
   6  
   7  /**
   8:  * @dev Implements the DNSSEC SHA1 digest.
   9   */
  10: contract SHA1Digest is Digest {


```


### [L-02] Function Calls in Loop Could Lead to Denial of Service due to Array length not being checked


```diff

contracts/dnsregistrar/DNSRegistrar.sol:

+ maxArrayLength = 50;

   89       */
   90:     function proveAndClaim(
   91:         bytes memory name,
   92:         DNSSEC.RRSetWithSignature[] memory input
   93:     ) public override {
+      if input.length > maxArrayLength) {
+          revert maxArrayLengt();
+       } 
   94:         (bytes32 rootNode, bytes32 labelHash, address addr) = _claim(
   95:             name,
   96:             input
   97:         );
   98:         ens.setSubnodeOwner(rootNode, labelHash, addr);
   99:     }


```

**Recommendation:**
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to gas limitations or failed transactions. Consider bounding the loop length if the array is expected to be growing and/or handling a huge list of elements to avoid unnecessary gas wastage and denial of service.


### [L-03] Missing Event for  initialize

**Context:**
```solidity


contracts/dnsregistrar/DNSRegistrar.sol:
  54  
  55:     constructor(
  56:         address _previousRegistrar,
  57:         address _resolver,
  58:         DNSSEC _dnssec,
  59:         PublicSuffixList _suffixes,
  60:         ENS _ens
  61:     ) {
  62:         previousRegistrar = _previousRegistrar;
  63:         resolver = _resolver;
  64:         oracle = _dnssec;
  65:         suffixes = _suffixes;
  66:         emit NewPublicSuffixList(address(suffixes));
  67:         ens = _ens;
  68:     }


contracts/dnsregistrar/OffchainDNSResolver.sol:
  42  
  43:     constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
  44:         ens = _ens;
  45:         oracle = _oracle;
  46:         gatewayURL = _gatewayURL;
  47:     }


contracts/dnssec-oracle/DNSSECImpl.sol:
  51       */
  52:     constructor(bytes memory _anchors) {
  53:         // Insert the 'trust anchors' - the key hashes that start the chain
  54:         // of trust for all other records.
  55:         anchors = _anchors;
  56:     }

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
Issuing event-emit during initialization is a detail that many projects skip

**Recommendation:**
Add Event-Emit

### [L-04] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

This point of view is a top professional point of view, not for amateur projects

Even if the project is not upgradably, the architecture of the upgradably versions should be planned (For example, from Uniswap v2 to Uniswap v3, etc.)


### [L-05] Tests cannot be run on the `Ropsten` network and must be removed


https://github.com/code-423n4/2023-04-ens/tree/main/deployments/ropsten

The Ropsten testnet has not been used after the Ethereum Merge. This is because the Ethereum Merge involves transitioning from the Proof of Work (PoW) consensus algorithm to the Proof of Stake (PoS) consensus algorithm, and the Ropsten testnet was built on the PoW protocol.

As a result, the Ropsten testnet can no longer be used for testing smart contracts on the Ethereum network


### [N-06] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

```solidity

15 results - 5 files

contracts/dnssec-oracle/BytesUtils.sol:
   18          require(offset + len <= self.length);
   19:         assembly {
   20              ret := keccak256(add(add(self, 32), offset), len)

   74  
   75:         assembly {
   76              selfptr := add(self, add(offset, 32))

   81              uint256 b;
   82:             assembly {
   83                  a := mload(selfptr)

  198          require(idx + 2 <= self.length);
  199:         assembly {
  200              ret := and(mload(add(add(self, 2), idx)), 0xFFFF)

  214          require(idx + 4 <= self.length);
  215:         assembly {
  216              ret := and(mload(add(add(self, 4), idx)), 0xFFFFFFFF)

  230          require(idx + 32 <= self.length);
  231:         assembly {
  232              ret := mload(add(add(self, 32), idx))

  246          require(idx + 20 <= self.length);
  247:         assembly {
  248              ret := and(

  268          require(idx + len <= self.length);
  269:         assembly {
  270              let mask := not(sub(exp(256, sub(32, len)), 1))

  277          for (; len >= 32; len -= 32) {
  278:             assembly {
  279                  mstore(dest, mload(src))

  287              uint256 mask = (256 ** (32 - len)) - 1;
  288:             assembly {
  289                  let srcpart := and(mload(src), not(mask))

  312  
  313:         assembly {
  314              dest := add(ret, 32)

contracts/dnssec-oracle/RRUtils.sol:
  385                  uint256 word;
  386:                 assembly {
  387                      word := mload(add(add(data, 32), i))

contracts/dnssec-oracle/SHA1.sol:
  6      function sha1(bytes memory data) internal pure returns (bytes20 ret) {
  7:         assembly {

contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol:
  22  
  23:         assembly {
  24              success := staticcall(

contracts/utils/HexUtils.sol:
  16          valid = true;
  17:         assembly {


```

### [N-07] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity


73 results - 12 files

contracts/dnsregistrar/DNSClaimChecker.sol:
  22:     ) internal pure returns (address, bool) {
  50:     ) internal pure returns (address, bool) {
  70:     ) internal pure returns (address, bool) {

contracts/dnsregistrar/OffchainDNSResolver.sol:
  140:     ) internal view returns (address, bytes memory) {
  166:     ) internal pure returns (bytes memory) {
  177:     ) internal view returns (address) {
  194:     ) internal view returns (address) {
  213:     ) internal view returns (bytes32) {

contracts/dnssec-oracle/BytesUtils.sol:
   17:     ) internal pure returns (bytes32 ret) {
   37:     ) internal pure returns (int256) {
   61:     ) internal pure returns (int256) {
  119:     ) internal pure returns (bool) {
  136:     ) internal pure returns (bool) {
  154:     ) internal pure returns (bool) {
  169:     ) internal pure returns (bool) {
  184:     ) internal pure returns (uint8 ret) {
  197:     ) internal pure returns (uint16 ret) {
  213:     ) internal pure returns (uint32 ret) {
  229:     ) internal pure returns (bytes32 ret) {
  245:     ) internal pure returns (bytes20 ret) {
  266:     ) internal pure returns (bytes32 ret) {
  306:     ) internal pure returns (bytes memory) {
  338:     ) internal pure returns (bytes32) {
  394:     ) internal pure returns (uint256) {

contracts/dnssec-oracle/DNSSECImpl.sol:
  144:     ) internal view returns (RRUtils.SignedSet memory rrset) {
  184:     ) internal pure returns (bytes memory name) {
  230:     ) internal view {
  258:     ) internal view {
  290:     ) internal view returns (bool) {
  334:     ) internal view {
  378:     ) internal view returns (bool) {
  419:     ) internal view returns (bool) {

contracts/dnssec-oracle/RRUtils.sol:
   22:     ) internal pure returns (uint256) {
   44:     ) internal pure returns (bytes memory ret) {
   58:     ) internal pure returns (uint256) {
   96:     ) internal pure returns (SignedSet memory self) {
  113:     ) internal pure returns (RRIterator memory) {
  139:     ) internal pure returns (RRIterator memory ret) {
  150:     function done(RRIterator memory iter) internal pure returns (bool) {
  158:     function next(RRIterator memory iter) internal pure {
  187:     function name(RRIterator memory iter) internal pure returns (bytes memory) {
  202:     ) internal pure returns (bytes memory) {
  226:     ) internal pure returns (DNSKEY memory self) {
  252:     ) internal pure returns (DS memory self) {
  262:     ) internal pure returns (bool) {
  278:     ) internal pure returns (int256) {
  335:     ) internal pure returns (bool) {
  344:     ) internal pure returns (uint256) {
  353:     function computeKeytag(bytes memory data) internal pure returns (uint16) {
  358:          *     function computeKeytag(bytes memory data) internal pure returns (uint16) {

contracts/dnssec-oracle/SHA1.sol:
  6:     function sha1(bytes memory data) internal pure returns (bytes20 ret) {

contracts/dnssec-oracle/algorithms/EllipticCurve.sol:
   40:     function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {
   68:     ) internal pure returns (uint256[3] memory P) {
   82:     ) internal pure returns (uint256[3] memory P) {
   96:     ) internal pure returns (uint256 x1, uint256 y1) {
  117:     function zeroAffine() internal pure returns (uint256 x, uint256 y) {
  127:     ) internal pure returns (bool isZero) {
  137:     function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {
  163:     ) internal pure returns (uint256 x1, uint256 y1, uint256 z1) {
  215:     ) internal pure returns (uint256 x2, uint256 y2, uint256 z2) {
  291:     ) internal pure returns (uint256, uint256) {
  305:     ) internal pure returns (uint256, uint256) {
  320:     ) internal pure returns (uint256, uint256) {
  339:     ) internal pure returns (uint256 x1, uint256 y1) {
  379:     ) internal pure returns (uint256, uint256) {
  390:     ) internal pure returns (bool) {

contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol:
  11:     ) internal view returns (bool success, bytes memory output) {

contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol:
  32:     ) internal pure returns (uint256[2] memory) {
  39:     ) internal pure returns (uint256[2] memory) {

contracts/dnssec-oracle/algorithms/RSAVerify.sol:
  18:     ) internal view returns (bool, bytes memory) {

contracts/utils/HexUtils.sol:
  15:     ) internal pure returns (bytes32 r, bool valid) {
  72:     ) internal pure returns (address, bool) {

contracts/utils/NameEncoder.sol:
  11:     ) internal pure returns (bytes memory dnsName, bytes32 node) {



```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [N-08] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

### [N-09] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code
(!= address(0))

```solidity

2 results - 2 files

contracts/dnsregistrar/DNSRegistrar.sol:
  114          ens.setSubnodeRecord(rootNode, labelHash, owner, resolver, 0);
  115:         if (addr != address(0)) {

contracts/dnsregistrar/OffchainDNSResolver.sol:
  101              // If we found a valid record, try to resolve it
  102:             if (dnsresolver != address(0)) {


```



### [N-10] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity
32 results - 6 files

contracts/dnsregistrar/DNSClaimChecker.sol:
  15  
  16:     uint16 constant CLASS_INET = 1;
  17:     uint16 constant TYPE_TXT = 16;
  18  

contracts/dnsregistrar/OffchainDNSResolver.sol:
  28  
  29: uint16 constant CLASS_INET = 1;
  30: uint16 constant TYPE_TXT = 16;
  31  

contracts/dnssec-oracle/BytesUtils.sol:
  323      // 0xFF represents invalid characters in that range.
  324:     bytes constant base32HexTable =
  325          hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F";

contracts/dnssec-oracle/DNSSECImpl.sol:
  26  
  27:     uint16 constant DNSCLASS_IN = 1;
  28  
  29:     uint16 constant DNSTYPE_DS = 43;
  30:     uint16 constant DNSTYPE_DNSKEY = 48;
  31  
  32:     uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
  33  

contracts/dnssec-oracle/RRUtils.sol:
   71  
   72:     uint256 constant RRSIG_TYPE = 0;
   73:     uint256 constant RRSIG_ALGORITHM = 2;
   74:     uint256 constant RRSIG_LABELS = 3;
   75:     uint256 constant RRSIG_TTL = 4;
   76:     uint256 constant RRSIG_EXPIRATION = 8;
   77:     uint256 constant RRSIG_INCEPTION = 12;
   78:     uint256 constant RRSIG_KEY_TAG = 16;
   79:     uint256 constant RRSIG_SIGNER_NAME = 18;
   80  

  209  
  210:     uint256 constant DNSKEY_FLAGS = 0;
  211:     uint256 constant DNSKEY_PROTOCOL = 2;
  212:     uint256 constant DNSKEY_ALGORITHM = 3;
  213:     uint256 constant DNSKEY_PUBKEY = 4;
  214  

  235  
  236:     uint256 constant DS_KEY_TAG = 0;
  237:     uint256 constant DS_ALGORITHM = 2;
  238:     uint256 constant DS_DIGEST_TYPE = 3;
  239:     uint256 constant DS_DIGEST = 4;
  240  

contracts/dnssec-oracle/algorithms/EllipticCurve.sol:
  20      // Set parameters for curve.
  21:     uint256 constant a =
  22          0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;
  23:     uint256 constant b =
  24          0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;
  25:     uint256 constant gx =
  26          0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;
  27:     uint256 constant gy =
  28          0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;
  29:     uint256 constant p =
  30          0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;
  31:     uint256 constant n =
  32          0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;
  33  
  34:     uint256 constant lowSmax =
  35          0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0;



```


## [N-11] Avoid _shadowing_ `inherited state variables`

**Context:**
```solidity

contracts/dnsregistrar/DNSRegistrar.sol:
  21: contract DNSRegistrar is IDNSRegistrar, IERC165 {
  30:     address public immutable resolver; // @audit `resolver` is state variable




contracts/dnsregistrar/DNSRegistrar.sol:
  101:     function proveAndClaimWithResolver(
  102:         bytes memory name,
  103:         DNSSEC.RRSetWithSignature[] memory input,
  104:         address resolver, // @audit `resolver` is same name function argument
  105:         address addr
  106:     ) public override {
  107:         (bytes32 rootNode, bytes32 labelHash, address owner) = _claim(
  108:             name,
  109:             input
  110:         );
  111:         if (msg.sender != owner) {
  112:             revert PermissionDenied(msg.sender, owner);
  113:         }
  114:         ens.setSubnodeRecord(rootNode, labelHash, owner, resolver, 0);
  115:         if (addr != address(0)) {
  116:             if (resolver == address(0)) {
  117:                 revert PreconditionNotMet();
  118:             }
  119:             bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
  120:             // Set the resolver record
  121:             AddrResolver(resolver).setAddr(node, addr);
  122:         }
  123:     }


```
`resolver` is shadowed, 

The local variable name ` resolver ` in the `DNSRegistrar.sol` hase the same name and create shadowing

The shadowed here does not break any model of the project (if it were, this finding would be High) but using the same name would be confusing and prone to error.

**Recommendation:**
Avoid using variables with the same name, including inherited in the same contract, if used, it must be specified in the NatSpec comments.

### [N-12]  Use Fuzzing Test for math code bases 


I recommend fuzzing testing in math code structures,

**Recommendation:**

Use should fuzzing test like Echidna.


Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now.



https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05


### [N-13] I recommend that you choose it according to its purpose when determining the function names.

In the function names used in the project, general words were used instead of specifying the specific purpose of use.

In projects written with Solidity, there is no standard for naming functions, but writing the names in accordance with the purpose of the function makes it easier to read and control. It also makes it easy for automated tools, etc. to analyze the code.

Since this is a QA report, I would like to specify it through one function of the project instead of detailing it.


**Context:**
```diff
contracts/dnssec-oracle/BytesUtils.sol:
- 13:     function keccak(
+           function calculateHashInRange(
 14:         bytes memory self,
  15:         uint256 offset,
  16:         uint256 len
  17:     ) internal pure returns (bytes32 ret) {
  18:         require(offset + len <= self.length);
  19:         assembly {
  20:             ret := keccak256(add(add(self, 32), offset), len)
  21:         }
  22:     }


```


