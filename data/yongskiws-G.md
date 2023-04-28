### Gas Optimizations Issues List
| Number |Issues Details|Instance|
|:--:|:-------|:--:|
|[G-01]| Using unchecked blocks to save gas | 2 |
|[G-02]| Use calldata instead of memory for function parameters | 3 |
|[G-03]| Use hardcode address instead address(this) | 8 |
|[G-04]| Sort Solidity operations using short-circuit mode | 3 |
|[G-05]| Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration) | 3 |
|[G-06]| Using storage instead of memory for structs/arrays saves gas | 4 |
|[G-07]| Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate | 3 |
|[G-08]| Using private rather than public for constants, saves gas | - |
|[G-09]| Using immutable for uint & address for save gas | - |
|[G-10]| internal functions not called by the contract should be removed to save deployment gas | 10 |
|[G-11]| x = x + y is cheaper than x += y; | 2 |
|[G-12]| Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | - |
|[G-13]| Using > 0 costs more gas than != 0 when used on a uint in a require() statement | 9 |
|[G-14]| Use custom errors rather than revert()/require() strings to save gas | 14 |

## [G-01] Using unchecked blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
https://github.com/ethereum/solidity/issues/10695

Gas Saved For Using unchecked blocks to save gas

The above should be modified to:

2 results - 1 files:
before
TestBytesUtils  || 2254402 || 7.5 % 

after
TestBytesUtils  || 2192177 || 7.3 %

``` diff
ens\BytesUtils.sol
+unchecked{
90:           mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
+}

352:  uint256 bitlen = len * 5;
353:         if (len % 8 == 0) {
354:             // Multiple of 8 characters, no padding
355:             ret = (ret << 5) | decoded;
356:         } else if (len % 8 == 2) {
357:             // Two extra characters - 1 byte
358:             ret = (ret << 3) | (decoded >> 2);
359:             bitlen -= 2;
360:         } else if (len % 8 == 4) {
361:             // Four extra characters - 2 bytes
362:             ret = (ret << 1) | (decoded >> 4);
363:             bitlen -= 4;
364:         } else if (len % 8 == 5) {
365:             // Five extra characters - 3 bytes
366:             ret = (ret << 4) | (decoded >> 1);
367:             bitlen -= 1;
368:         } else if (len % 8 == 7) {
369:             // Seven extra characters - 4 bytes
370:             ret = (ret << 2) | (decoded >> 3);
371:             bitlen -= 3;
372:         } else {
373:             revert();
374:         }
375:
+unchecked{ 
376:         return bytes32(ret << (256 - bitlen));
+}
```


## [G-02] Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of memory is more optimal.

Consider the following generic example:

``` solidity
contract C {
  function add(uint[] memory arr) external returns (uint sum) {
      uint length = arr.length;
      for (uint i = 0; i < arr.length; i++) {
          sum += arr[i];
      }
  }
}
```

In the above example, the dynamic array arr has the storage location memory. When the function gets called externally, the array values are kept in calldata and copied to memory during ABI decoding (using the opcode calldataload and mstore). And during the for loop, arr[i] accesses the value in memory using a mload. However, for the above example this is inefficient. Consider the following snippet instead:

``` solidity
contract C {
  function add(uint[] calldata arr) external returns (uint sum) {
      uint length = arr.length;
      for (uint i = 0; i < arr.length; i++) {
          sum += arr[i];
      }
  }
}
```

In the above snippet, instead of going via memory, the value is directly read from calldata using calldataload. That is, there are no intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with copying value from calldata to memory in a for loop. Each iteration would cost at least 60 gas. In the latter example, this can be completely avoided. This will also reduce the number of instructions and therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument is only read.

Note that in older Solidity versions, changing some function arguments from memory to calldata may cause "unimplemented feature error". This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples Note: The following pattern is prevalent in the codebase:

``` solidity
function f(bytes memory data) external {
  (...) = abi.decode(data, (..., types, ...));
}
```

Here, changing to bytes calldata will decrease the gas. The total savings for this change across all such uses would be quite significant.

3 results - 3 files:


``` solidity
ens\BytesUtils.sol
341:         for (uint256 i = 0; i < len; i++) {
342:             bytes1 char = self[off + i];
343:             require(char >= 0x30 && char <= 0x7A);
344:             decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);
345:             require(decoded <= 0x20);
346:             if (i == len - 1) {
347:                 break;
348:             }
349:             ret = (ret << 5) | decoded;
350:         }


ens\DNSSECImpl.sol
118:         for (uint256 i = 0; i < input.length; i++) {
119:             RRUtils.SignedSet memory rrset = validateSignedSet(
120:                 input[i],
121:                 proof,
122:                 now
123:             );
124:             proof = rrset.data;
125:             inception = rrset.inception;
126:         }


ens\EllipticCurve.sol
325:         for (uint256 i = 0; i < exp; i++) {
326:             (base2X, base2Y, base2Z) = twiceProj(base2X, base2Y, base2Z);
327:         }
```


## [G-03] Use hardcode address instead address(this)


Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry's script.sol and solmate's LibRlp.sol contracts can help achieve this.


References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

https://twitter.com/transmissions11/status/1518507047943245824

8 results - 6 files:

``` solidity
File: c:\Users\user\Desktop\security\2023-04-ens\contracts\dnsregistrar\DNSRegistrar.sol
190:     root.setSubnodeOwner(label, address(this));

193:                 ens.setSubnodeRecord(
194:                     parentNode,
195:                     label,
196:                     address(this),
197:                     resolver,
198:                     0
199:                 );

201:       } else if (owner != address(this)) {

File: c:\Users\user\Desktop\security\2023-04-ens\contracts\dnsregistrar\OffchainDNSResolver.sol

56:  revert OffchainLookup(
57:             address(this),
58:             urls,
59:             abi.encodeCall(IDNSGateway.resolve, (name, TYPE_TXT)),
60:             OffchainDNSResolver.resolveCallback.selector,
61:             abi.encode(name, data)
62:         );

File: c:\Users\user\Desktop\security\2023-04-ens\contracts\registry\ENSRegistry.sol
147:        if (addr == address(this)) {
  
File: c:\Users\user\Desktop\security\2023-04-ens\contracts\registry\ENSRegistryWithFallback.sol
61:       addr = address(this);

File: c:\Users\user\Desktop\security\2023-04-ens\contracts\resolvers\Multicallable.sol
21:             (bool success, bytes memory result) = address(this).delegatecall(
22:                 data[i]
23:             );

File: c:\Users\user\Desktop\security\2023-04-ens\contracts\resolvers\profiles\ExtendedResolver.sol
9:         (bool success, bytes memory result) = address(this).staticcall(data);

File: c:\Users\user\Desktop\security\2023-04-ens\contracts\utils\UniversalResolver.sol
577:  revert OffchainLookup(
578:             address(this),
579:             multicallData.gateways,
580:             abi.encodeWithSelector(BatchGateway.query.selector, callDatas),
581:             multicallData.callbackFunction,
582:             abi.encode(
583:                 multicallData.isWildcard,
584:                 multicallData.resolver,
585:                 multicallData.gateways,
586:                 multicallData.metaData,
587:                 extraDatas
588:             )
589:         );
```

## [G-04] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

3 results - 2 files:


``` solidity
ens\DNSRegistrar.sol
128:   return
129:             interfaceID == type(IERC165).interfaceId ||
130:             interfaceID == type(IDNSRegistrar).interfaceId;

187:         if (owner == address(0) || owner == previousRegistrar) {

ens\DNSSECImpl.sol
201:                 if (
202:                     name.length != iter.data.nameLength(iter.offset) ||
203:                     !name.equals(0, iter.data, iter.offset, name.length)
204:                 )
```

## [G-05] Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration)

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.
``` solidity
for(uint256 i; i < 10; i++){
//doSomething
}
```
can be written as shown below.
``` diff
for(uint256 i; i < 10;) {
// loop logic
+  unchecked { i++; }
}
```
We can also write it as an inlined function like below.
``` solidity
function inc(i) internal pure returns (uint256) {
unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
// doSomething
}
```

The above should be modified to:

3 results - 3 files:

``` diff

ens\BytesUtils.sol
-341:         for (uint256 i = 0; i < len; i++) {
+341:         for (uint256 i = 0; i < len;) {  
342:             bytes1 char = self[off + i];
343:             require(char >= 0x30 && char <= 0x7A);
344:             decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);
345:             require(decoded <= 0x20);
346:             if (i == len - 1) {
347:                 break;
348:             }
349:             ret = (ret << 5) | decoded;
+             unchecked {
+                ++i;
+             }
350:         }


ens\DNSSECImpl.sol
-118:         for (uint256 i = 0; i < input.length; i++) {
+118:         for (uint256 i = 0; i < input.length;) {
119:             RRUtils.SignedSet memory rrset = validateSignedSet(
120:                 input[i],
121:                 proof,
122:                 now
123:             );
124:             proof = rrset.data;
125:             inception = rrset.inception;
+             unchecked {
+                ++i;
+             }
126:         }


ens\EllipticCurve.sol
-325:         for (uint256 i = 0; i < exp; i++) {
+325:         for (uint256 i = 0; i < exp;) {
326:             (base2X, base2Y, base2Z) = twiceProj(base2X, base2Y, base2Z);
+             unchecked {
+                ++i;
+             }
327:         }

```

## [G-06] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

4 results - 4 files:

``` solidity

ens\OffchainDNSResolver.sol
53:         string[] memory urls = new string[](1);
54:         urls[0] = gatewayURL;
55: 

ens\DNSClaimChecker.sol
24:   Buffer.buffer memory buf;

ens\DNSClaimChecker.sol
30:             RRUtils.RRIterator memory iter = data.iterateRRs(0);

ens\OffchainDNSResolver.sol
53:         string[] memory urls = new string[](1);
54:         urls[0] = gatewayURL;

```


## [G-07] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas)

2 results - 2 files:

``` solidity

ens\DNSRegistrar.sol
32:     mapping(bytes32 => uint32) public inceptions;


ens\DNSSECImpl.sol
45:     mapping(uint8 => Algorithm) public algorithms;
46:     mapping(uint8 => Digest) public digests;
47: 

```

## [G-08] Using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

There are ALL CONTRACT instances of this issue eg. :

``` diff
- uint256 public override example;
+ uint256 private override example;
```

## [G-09] Using immutable for uint & address for save gas
Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

There are ALL CONTRACT instances of this issue eg. :

``` diff
- address public override example;
+ address public immutable override example;
```

## [G-10] internal functions not called by the contract should be removed to save deployment gas
If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

10 results - 4 files:

``` solidity

ens\DNSSECImpl.sol
140:     function validateSignedSet(
141:         RRSetWithSignature memory input,
142:         bytes memory proof,
143:         uint256 now
144:     ) internal view returns (RRUtils.SignedSet memory rrset) {

285:     function verifySignatureWithKey(
286:         RRUtils.DNSKEY memory dnskey,
287:         bytes memory keyrdata,
288:         RRUtils.SignedSet memory rrset,
289:         RRSetWithSignature memory data
290:     ) internal view returns (bool) {

373:     function verifyKeyWithDS(
374:         bytes memory keyname,
375:         RRUtils.RRIterator memory dsrrs,
376:         RRUtils.DNSKEY memory dnskey,
377:         bytes memory keyrdata
378:     ) internal view returns (bool) {

415:     function verifyDSHash(
416:         uint8 digesttype,
417:         bytes memory data,
418:         bytes memory digest
419:     ) internal view returns (bool) {


ens\ModexpPrecompile.sol
7:     function modexp(
8:         bytes memory base,
9:         bytes memory exponent,
10:         bytes memory modulus
11:     ) internal view returns (bool success, bytes memory output) {


ens\OffchainDNSResolver.sol
136:    function parseRR(
137:         bytes memory data,
138:         uint256 idx,
139:         uint256 lastIdx
140:     ) internal view returns (address, bytes memory) {

173:     function parseAndResolve(
174:         bytes memory nameOrAddress,
175:         uint256 idx,
176:         uint256 lastIdx
177:     ) internal view returns (address) {


190:     function resolveName(
191:         bytes memory name,
192:         uint256 idx,
193:         uint256 lastIdx
194:     ) internal view returns (address) {

209:     function textNamehash(
210:         bytes memory name,
211:         uint256 idx,
212:         uint256 lastIdx
213:     ) internal view returns (bytes32) {

ens\RSAVerify.sol
14:     function rsarecover(
15:         bytes memory N,
16:         bytes memory E,
17:         bytes memory S
18:     ) internal view returns (bool, bytes memory) {

```

## [G-11] x = x + y is cheaper than x += y;

2 results - 1 files:

``` solidity
ens\BytesUtils.sol
95:             selfptr += 32;
96:             otherptr += 32;


ens\BytesUtils.sol
279:             dest += 32;
280:             src += 32;

```

## [G-12] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

Use a larger size then downcast where needed


## [G-13] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

9 results - 5 files:

``` solidity

ens\DNSRegistrar.sol
179:     if (len == 0) {

ens\EllipticCurve.sol
410: if (P[2] == 0) {

ens\NameEncoder.sol
17:  if (length == 0) {

39:  if (i == 0) {

ens\DNSSECImpl.sol
196:          if (name.length == 0) {


ens\RRUtils.sol
28:    if (labelLen == 0) {

64:        if (labelLen == 0) {

312:         if (off == 0) {

315:         if (otheroff == 0) {
```


##  [G‑14] Use custom errors rather than revert()/require() strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas
https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth

There are 14 instances of this issue:

``` solidity
ens\BytesUtils.sol
18:         require(offset + len <= self.length);
196:    require(idx + 2 <= self.length);
212:       require(idx + 4 <= self.length);
228:         require(idx + 32 <= self.length);
244:         require(idx + 20 <= self.length);
265:         require(len <= 32);
266:         require(idx + len <= self.length);
337:      require(len <= 52);

ens\DNSRegistrar.sol
76:         require(msg.sender == owner);

ens\P256SHA256Algorithm.sol
33:   require(data.length == 64, "Invalid p256 signature length");
40:    require(data.length == 68, "Invalid p256 key length");

ens\RRUtils.sol
381:             require(data.length <= 8192, "Long keys not permitted");

ens\SHA1Digest.sol
17:   require(hash.length == 20, "Invalid sha1 hash length");

ens\SHA256Digest.sol
16:  require(hash.length == 32, "Invalid sha256 hash length");
```
