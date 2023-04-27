# Summary
A majority of the optimizations were benchmarked via the protocol's tests, i.e. using the following config: `solc version 0.8.17`, `optimizer on`, and `1300 runs`. Optimizations that were not benchmarked are explained via EVM gas costs and opcodes.

Below are the overall average gas savings for the following tested functions, with all the optimizations applied:
| Function |    Before   |    After   | Avg Gas Savings |
| ------ | -------- | -------- | ------- |
| DNSRegistrar.proveAndClaim |  269704  |  240737  | 28967 | 
| DNSRegistrar.proveAndClaimWithResolver |  320432  |  291386  | 29046 |  

**Total gas saved across all listed functions: 58013**

*Notes*: 

- The [Gas report](#gasreport-output-with-all-optimizations-applied) output, after all optimizations have been applied, can be found at the end of the report.
- The final diffs for each contract, with all the optimizations applied, can be found [here](https://gist.github.com/0xJCN/e7523b1e87e79e6eea8e865906e5fb17).

## Gas Optimizations
| Number |Issue|Instances|
|-|:-|:-:|
| [G-01](#refactor-code-to-avoid-unnecessary-memory-expansion-and-data-check-within-loops) | Refactor code to avoid unnecessary memory expansion and data check within loops | 2 | 
| [G-02](#state-variables-can-be-cached-instead-of-re-reading-them-from-storage) | State variables can be cached instead of re-reading them from storage | 1 | 
| [G-03](#create-immutable-variable-to-avoid-an-external-call) | Create immutable variable to avoid an external call | 1 | 
| [G-04](#avoid-emitting-storage-values) | Avoid emitting storage values | 1 | 
| [G-05](#refactor-code-with-assembly-to-check-zero-address-hash-values-and-perform-external-call) | Refactor code with assembly to check zero address, hash values, and perform external call | 1 |
| [G-06](#use-assembly-to-hash-values-more-efficiently) | Use assembly to hash values more efficiently | 2 |
| [G-07](#use-assembly-to-make-more-efficient-back-to-back-calls) | Use assembly to make more efficient back-to-back calls | 1 |
| [G-08](#use-assembly-for-loops) | Use assembly for loops | 3 | 
| [G-09](#use-assembly-to-grab-and-cast-value-in-byte-array) | Use assembly to grab and cast value in byte array | 1 | 
| [G-10](#include-check-in-assembly-block) | Include check in assembly block | 3 | 
| [G-11](#write-a-more-gas-efficient-assembly-loop) | Write a more gas efficient assembly loop | 1 | 
| [G-12](#use-a-do-while-loop-instead-of-a-for-loop) | Use a `do while` loop instead of a `for` loop | 8 | 
| [G-13](#dont-cache-value-if-it-is-only-used-once) | Don't cache value if it is only used once | 1 | 
| [G-14](#refactor-code-to-avoid-instantiating-memory-struct-within-loop) | Refactor code to avoid instantiating memory struct within loop | 1 | 
| [G-15](#if-statements-that-use--can-be-refactored-into-nested-if-statements) | `If` statements that use `&` can be refactored into nested `if` statements | 1 | 
| [G-16](#refactor-modifier-to-avoid-two-external-calls-when-calling-setpublicsuffixlist) | Refactor modifier to avoid two external calls when calling `setPublicSuffixList` | 1 |

## Refactor code to avoid unnecessary memory expansion and data check within loops
In the instances below, the proof name is being read from memory and then stored into a new section of memory. After this is done an `if` statement is used to check a condition. Both the proof name and condition in the `if` statement stay the same for each iteration, and therefore those lines of code can be moved outside of the loop to avoid doing those computations on each iteration.

Total Instances: `2`

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L254-L264

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 3270 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  298194  |  336129  |  317162 |    2     |

```solidity
File: contracts/dnsssec-oracle/DNSSECImpl.sol
254:    function verifyWithKnownKey(
255:        RRUtils.SignedSet memory rrset,
256:        RRSetWithSignature memory data,
257:        RRUtils.RRIterator memory proof
258:    ) internal view {
259:        // Check the DNSKEY's owner name matches the signer name on the RRSIG
260:        for (; !proof.done(); proof.next()) {
261:            bytes memory proofName = proof.name();
262:            if (!proofName.equals(rrset.signerName)) {
263:                revert ProofNameMismatch(rrset.signerName, proofName);
264:            }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..f8adb3c 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -257,11 +257,11 @@ contract DNSSECImpl is DNSSEC, Owned {
         RRUtils.RRIterator memory proof
     ) internal view {
         // Check the DNSKEY's owner name matches the signer name on the RRSIG
+        bytes memory proofName = proof.name();
+        if (!proofName.equals(rrset.signerName)) {
+            revert ProofNameMismatch(rrset.signerName, proofName);
+        }
         for (; !proof.done(); proof.next()) {
-            bytes memory proofName = proof.name();
-            if (!proofName.equals(rrset.signerName)) {
-                revert ProofNameMismatch(rrset.signerName, proofName);
-            }

             bytes memory keyrdata = proof.rdata();
             RRUtils.DNSKEY memory dnskey = keyrdata.readDNSKEY(
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L373-L384

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 3223 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  298241  |  336176  |  317209 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
373:    function verifyKeyWithDS(
374:        bytes memory keyname,
375:        RRUtils.RRIterator memory dsrrs,
376:        RRUtils.DNSKEY memory dnskey,
377:        bytes memory keyrdata
378:    ) internal view returns (bool) {
379:        uint16 keytag = keyrdata.computeKeytag();
380:        for (; !dsrrs.done(); dsrrs.next()) {
381:            bytes memory proofName = dsrrs.name();
382:            if (!proofName.equals(keyname)) {
383:                revert ProofNameMismatch(keyname, proofName);
384:            }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..ae9ba6a 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -377,11 +377,11 @@ contract DNSSECImpl is DNSSEC, Owned {
         bytes memory keyrdata
     ) internal view returns (bool) {
         uint16 keytag = keyrdata.computeKeytag();
+        bytes memory proofName = dsrrs.name();
+        if (!proofName.equals(keyname)) {
+            revert ProofNameMismatch(keyname, proofName);
+        }
         for (; !dsrrs.done(); dsrrs.next()) {
-            bytes memory proofName = dsrrs.name();
-            if (!proofName.equals(keyname)) {
-                revert ProofNameMismatch(keyname, proofName);
-            }

             RRUtils.DS memory ds = dsrrs.data.readDS(
                 dsrrs.rdataOffset,
```

## State variables can be cached instead of re-reading them from storage
Caching of a state variable replaces each `Gwarmaccess (100 gas)` with a much cheaper stack read.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L415-L425

*Gas Savings for `DNSRegistrar.proveAndClaim`, obtained via protocol's tests: Avg 182 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  198110  |  308834  |  269704 |    7     |
| After  |  197928  |  308652  |  269522 |    7     |

### Cache `digests[digesttype]` to save 1 SLOAD
```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
415:    function verifyDSHash(
416:        uint8 digesttype,
417:        bytes memory data,
418:        bytes memory digest
419:    ) internal view returns (bool) {
420:        if (address(digests[digesttype]) == address(0)) {
421:            return false;
422:        }
423:        return digests[digesttype].verify(data, digest);
424:    }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..df0bbf7 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -417,9 +417,11 @@ contract DNSSECImpl is DNSSEC, Owned {
         bytes memory data,
         bytes memory digest
     ) internal view returns (bool) {
-        if (address(digests[digesttype]) == address(0)) {
+        Digest _digest = digests[digesttype];
+        if (address(_digest) == address(0)) {
             return false;
         }
-        return digests[digesttype].verify(data, digest);
+        return _digest.verify(data, digest);
     }
 }
```

## Create immutable variable to avoid an external call
Instead of performing an external call to get the `root` address each time `_enableNode` is invoked, we can perform this external call once in the constructor and store the `root` as an immutable variable. Doing this will save 1 external call each time `_enableNode` is invoked.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L187-L192

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 1011 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  300453  |  338388  |  319421 |    2     |

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol
187:        if (owner == address(0) || owner == previousRegistrar) {
188:            if (parentNode == bytes32(0)) {
189:                Root root = Root(ens.owner(bytes32(0)));
190:                root.setSubnodeOwner(label, address(this));
191:                ens.setResolver(node, resolver);
192:            } else {
```
```diff
diff --git a/contracts/dnsregistrar/DNSRegistrar.sol b/contracts/dnsregistrar/DNSRegistrar.sol
index 953a9a3..fda3ebc 100644
--- a/contracts/dnsregistrar/DNSRegistrar.sol
+++ b/contracts/dnsregistrar/DNSRegistrar.sol
@@ -28,6 +28,7 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {
     PublicSuffixList public suffixes;
     address public immutable previousRegistrar;
     address public immutable resolver;
+    Root private immutable root;
     // A mapping of the most recent signatures seen for each claimed domain.
     mapping(bytes32 => uint32) public inceptions;

@@ -65,6 +66,7 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {
         suffixes = _suffixes;
         emit NewPublicSuffixList(address(suffixes));
         ens = _ens;
+        root = Root(_ens.owner(bytes32(0)));
     }

     /**
@@ -186,7 +188,6 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {
         address owner = ens.owner(node);
         if (owner == address(0) || owner == previousRegistrar) {
             if (parentNode == bytes32(0)) {
-                Root root = Root(ens.owner(bytes32(0)));
                 root.setSubnodeOwner(label, address(this));
                 ens.setResolver(node, resolver);
             } else {
```

## Avoid emitting storage values
In the instance below, we can emit the calldata value instead of emitting a storage value. This will result in using a cheap `CALLDATALOAD` instead of an expensive `SLOAD`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83

### Emit `_suffixes` instead of reading from storage
```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol
80:    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
81:        suffixes = _suffixes;
82:        emit NewPublicSuffixList(address(suffixes));
83:    }
```
```diff
diff --git a/contracts/dnsregistrar/DNSRegistrar.sol b/contracts/dnsregistrar/DNSRegistrar.sol
index 953a9a3..64e758f 100644
--- a/contracts/dnsregistrar/DNSRegistrar.sol
+++ b/contracts/dnsregistrar/DNSRegistrar.sol
@@ -79,7 +79,7 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {

     function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
         suffixes = _suffixes;
-        emit NewPublicSuffixList(address(suffixes));
+        emit NewPublicSuffixList(address(_suffixes));
     }
```

## Refactor code with assembly to check zero address, hash values, and perform external call
In the instance below, we can check for the zero address using assembly. In addition, we can reuse memory to hash values and perform an external call.

**Note: In order to do this optimization safely we will cache and restore the free memory pointer.**

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L115-L122

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 169 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301449  |  339077  |  320263 |    2     |

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol
115:        if (addr != address(0)) {
116:            if (resolver == address(0)) {
117:                revert PreconditionNotMet();
118:            }
119:            bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
120:            // Set the resolver record
121:            AddrResolver(resolver).setAddr(node, addr);
122:        }
```
```diff
diff --git a/contracts/dnsregistrar/DNSRegistrar.sol b/contracts/dnsregistrar/DNSRegistrar.sol
index 953a9a3..a2802fd 100644
--- a/contracts/dnsregistrar/DNSRegistrar.sol
+++ b/contracts/dnsregistrar/DNSRegistrar.sol
@@ -112,13 +112,25 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {
             revert PermissionDenied(msg.sender, owner);
         }
         ens.setSubnodeRecord(rootNode, labelHash, owner, resolver, 0);
-        if (addr != address(0)) {
-            if (resolver == address(0)) {
-                revert PreconditionNotMet();
+        assembly {
+            if iszero(iszero(calldataload(0x64))) {
+                if iszero(calldataload(0x44)) {
+                    mstore(0x00, 0xf1613c4c)
+                    revert(0x1c, 0x04)
+                }
+                let memptr := mload(0x40)
+                mstore(0x00, rootNode)
+                mstore(0x20, labelHash)
+                let node := keccak256(0x00, 0x40)
+                mstore(0x00, 0xd5fa2b00)
+                mstore(0x20, node)
+                mstore(0x40, calldataload(0x64))
+                let success := call(gas(), calldataload(0x44), 0x00, 0x1c, 0x44, 0x00, 0x00)
+                if iszero(success) {
+                    revert(0, 0)
+                }
+                mstore(0x40, memptr)
             }
-            bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
-            // Set the resolver record
-            AddrResolver(resolver).setAddr(node, addr);
         }
     }
```

## Use assembly to hash values more efficiently
In the instances below, we can hash values more efficiently by using the least amount of opcodes possible via assembly.

Total Instances: `2`

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L151

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 116 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301348  |  339283  |  320316 |    2     |

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol
151:        bytes32 node = keccak256(abi.encodePacked(parentNode, labelHash));
```
```diff
diff --git a/contracts/dnsregistrar/DNSRegistrar.sol b/contracts/dnsregistrar/DNSRegistrar.sol
index 953a9a3..8fc7456 100644
--- a/contracts/dnsregistrar/DNSRegistrar.sol
+++ b/contracts/dnsregistrar/DNSRegistrar.sol
@@ -147,8 +147,13 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {

         // Make sure the parent name is enabled
         parentNode = enableNode(parentName);
-
-        bytes32 node = keccak256(abi.encodePacked(parentNode, labelHash));
+
+        bytes32 node;
+        assembly {
+            mstore(0x00, parentNode)
+            mstore(0x20, labelHash)
+            node := keccak256(0x00, 0x40)
+        }
         if (!RRUtils.serialNumberGte(inception, inceptions[node])) {
             revert StaleProof();
         }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L185

*Gas Savings for `DNSRegistrar.proveAndClaim`, obtained via protocol's tests: Avg 97 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  198110  |  308834  |  269704 |    7     |
| After  |  198025  |  308665  |  269607 |    7     |

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol
185:        node = keccak256(abi.encodePacked(parentNode, label));
```
```diff
diff --git a/contracts/dnsregistrar/DNSRegistrar.sol b/contracts/dnsregistrar/DNSRegistrar.sol
index 953a9a3..8fc7456 100644
--- a/contracts/dnsregistrar/DNSRegistrar.sol
+++ b/contracts/dnsregistrar/DNSRegistrar.sol
@@ -182,7 +182,11 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {

         bytes32 parentNode = _enableNode(domain, offset + len + 1);
         bytes32 label = domain.keccak(offset + 1, len);
-        node = keccak256(abi.encodePacked(parentNode, label));
+        assembly {
+            mstore(0x00, parentNode)
+            mstore(0x20, label)
+            node := keccak256(0x00, 0x40)
+        }
         address owner = ens.owner(node);
         if (owner == address(0) || owner == previousRegistrar) {
             if (parentNode == bytes32(0)) {
```

## Use assembly to make more efficient back-to-back calls
In the instance below, two external calls, both of which take two function parameters, are performed. We can potentially avoid memory expansion costs by using assembly to utilize `scratch space` + `free memory pointer` memory offsets for the function calls. We will use the same memory space for both function calls.

**Note: In order to do this optimization safely we will cache the free memory pointer value and restore it once we are done with our function calls.**

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L190-L191

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 380 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301084  |  339019  |  320052 |    2     |

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol
190:                root.setSubnodeOwner(label, address(this));
191:                ens.setResolver(node, resolver);
```
```diff
diff --git a/contracts/dnsregistrar/DNSRegistrar.sol b/contracts/dnsregistrar/DNSRegistrar.sol
index 953a9a3..07f8d99 100644
--- a/contracts/dnsregistrar/DNSRegistrar.sol
+++ b/contracts/dnsregistrar/DNSRegistrar.sol
@@ -187,8 +187,26 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {
         if (owner == address(0) || owner == previousRegistrar) {
             if (parentNode == bytes32(0)) {
                 Root root = Root(ens.owner(bytes32(0)));
-                root.setSubnodeOwner(label, address(this));
-                ens.setResolver(node, resolver);
+                address _resolver = resolver;
+                ENS _ens = ens;
+                assembly {
+                    let memptr := mload(0x40)
+                    mstore(0x00, 0x8cb8ecec)
+                    mstore(0x20, label)
+                    mstore(0x40, address())
+                    let success1 := call(gas(), root, 0x00, 0x1c, 0x44, 0x00, 0x00)
+                    if iszero(success1) {
+                        revert(0, 0)
+                    }
+                    mstore(0x00, 0x1896f70a)
+                    mstore(0x20, node)
+                    mstore(0x40, _resolver)
+                    let success2 := call(gas(), _ens, 0x00, 0x1c, 0x44, 0x00, 0x00)
+                    if iszero(success2) {
+                        revert(0, 0)
+                    }
+                    mstore(0x40, memptr)
+                }
             } else {
                 ens.setSubnodeRecord(
                     parentNode,
```

## Use assembly for loops
We can use assembly to write a more gas efficient loop. See the [final diffs](https://gist.github.com/0xJCN/e7523b1e87e79e6eea8e865906e5fb17) for comments regarding the assembly code.

Total Instances: `3`

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L19-L31

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 9009 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  292455  |  330390  |  311423 |    2     |

```solidity
File: contracts/dnssec-oracle/RRUtils.sol
19:    function nameLength(
20:        bytes memory self,
21:        uint256 offset
22:    ) internal pure returns (uint256) {
23:        uint256 idx = offset;
24:        while (true) {
25:            assert(idx < self.length);
26:            uint256 labelLen = self.readUint8(idx);
27:            idx += labelLen + 1;
28:            if (labelLen == 0) {
29:                break;
30:            }
31:        }
```
```diff
diff --git a/contracts/dnssec-oracle/RRUtils.sol b/contracts/dnssec-oracle/RRUtils.sol
index 20fbf15..2579945 100644
--- a/contracts/dnssec-oracle/RRUtils.sol
+++ b/contracts/dnssec-oracle/RRUtils.sol
@@ -21,12 +21,14 @@ library RRUtils {
         uint256 offset
     ) internal pure returns (uint256) {
         uint256 idx = offset;
-        while (true) {
-            assert(idx < self.length);
-            uint256 labelLen = self.readUint8(idx);
-            idx += labelLen + 1;
-            if (labelLen == 0) {
-                break;
+        assembly {
+            for {} 1 {} {
+                if iszero(lt(idx, mload(self))) {
+                    revert(0, 0)
+                }
+                let labelLen := shr(248, mload(add(add(self, 0x20), idx)))
+                idx := add(idx, add(labelLen, 1))
+                if iszero(labelLen) { break }
             }
         }
         return idx - offset;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L55-L68

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 3216 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  298248  |  336183  |  317216 |    2     |

```solidity
File: contracts/dnssec-oracle/RRUtils.sol
55:    function labelCount(
56:        bytes memory self,
57:        uint256 offset
58:    ) internal pure returns (uint256) {
59:        uint256 count = 0;
60:        while (true) {
61:            assert(offset < self.length);
62:            uint256 labelLen = self.readUint8(offset);
63:            offset += labelLen + 1;
64:            if (labelLen == 0) {
65:                break;
66:            }
67:            count += 1;
68:        }
```
```diff
diff --git a/contracts/dnssec-oracle/RRUtils.sol b/contracts/dnssec-oracle/RRUtils.sol
index 20fbf15..0cfe57a 100644
--- a/contracts/dnssec-oracle/RRUtils.sol
+++ b/contracts/dnssec-oracle/RRUtils.sol
@@ -57,14 +57,16 @@ library RRUtils {
         uint256 offset
     ) internal pure returns (uint256) {
         uint256 count = 0;
-        while (true) {
-            assert(offset < self.length);
-            uint256 labelLen = self.readUint8(offset);
-            offset += labelLen + 1;
-            if (labelLen == 0) {
-                break;
+        assembly {
+            for {} 1 {} {
+                if iszero(lt(offset, mload(self))) {
+                    revert(0, 0)
+                }
+                let labelLen := shr(248, mload(add(add(self, 0x20), offset)))
+                offset := add(offset, add(labelLen, 1))
+                if iszero(labelLen) { break }
+                count := add(count, 1)
             }
-            count += 1;
         }
         return count;
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L259-L270

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 894 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  300570  |  338505  |  319538 |    2     |

```solidity
File: contracts/dnssec-oracle/RRUtils.sol
259:    function isSubdomainOf(
260:        bytes memory self,
261:        bytes memory other
262:    ) internal pure returns (bool) {
263:        uint256 off = 0;
264:        uint256 counts = labelCount(self, 0);
265:        uint256 othercounts = labelCount(other, 0);
266:
267:        while (counts > othercounts) {
268:            off = progress(self, off);
269:            counts--;
270:        }
```
```diff
diff --git a/contracts/dnssec-oracle/RRUtils.sol b/contracts/dnssec-oracle/RRUtils.sol
index 20fbf15..8f098f0 100644
--- a/contracts/dnssec-oracle/RRUtils.sol
+++ b/contracts/dnssec-oracle/RRUtils.sol
@@ -263,10 +263,18 @@ library RRUtils {
         uint256 off = 0;
         uint256 counts = labelCount(self, 0);
         uint256 othercounts = labelCount(other, 0);
-
-        while (counts > othercounts) {
-            off = progress(self, off);
-            counts--;
+
+        assembly {
+            for {} 1 {} {
+                if iszero(gt(counts, othercounts)) {
+                    break
+                }
+                if iszero(lt(off, mload(self))) {
+                    revert(0, 0)
+                }
+                off := add(add(off, 1), shr(248, mload(add(add(self, 0x20), off))))
+                counts := sub(counts, 1)
+            }
         }

         return self.equals(off, other, 0);
```

## Use assembly to grab and cast value in byte array
Like various similar functions, i.e. [readUint16](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L192) and [readUint32](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L208), assembly can be used to grab the value at a specified index of a byte array and cast that value to a specific uint type. In the diff below, a check is done before this operation to ensure that the offset is not out of bounds.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L179-L184

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 1280 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  300184  |  338119  |  319152 |    2     |

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol
179:    function readUint8(
180:        bytes memory self,
181:        uint256 idx
182:    ) internal pure returns (uint8 ret) {
183:        return uint8(self[idx]);
184:    }
```
```diff
diff --git a/contracts/dnssec-oracle/BytesUtils.sol b/contracts/dnssec-oracle/BytesUtils.sol
index 96344ce..8b6335d 100644
--- a/contracts/dnssec-oracle/BytesUtils.sol
+++ b/contracts/dnssec-oracle/BytesUtils.sol
@@ -180,7 +180,12 @@ library BytesUtils {
         bytes memory self,
         uint256 idx
     ) internal pure returns (uint8 ret) {
-        return uint8(self[idx]);
+        assembly {
+            if iszero(lt(idx, mload(self))) {
+                revert(0, 0)
+            }
+            ret := shr(248, mload(add(add(self, 0x20), idx)))
+        }
     }
```

## Include check in assembly block
In the instances below, we can include the checks in the `require` statements inside the following assembly blocks.

Total Instances: `3`

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L13-L22

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 1656 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  299808  |  337743  |  318776 |    2     |

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol
13:    function keccak(
14:        bytes memory self,
15:        uint256 offset,
16:        uint256 len
17:    ) internal pure returns (bytes32 ret) {
18:        require(offset + len <= self.length);
19:        assembly {
20:            ret := keccak256(add(add(self, 32), offset), len)
21:        }
22:    }
```
```diff
diff --git a/contracts/dnssec-oracle/BytesUtils.sol b/contracts/dnssec-oracle/BytesUtils.sol
index 96344ce..472c224 100644
--- a/contracts/dnssec-oracle/BytesUtils.sol
+++ b/contracts/dnssec-oracle/BytesUtils.sol
@@ -15,8 +15,10 @@ library BytesUtils {
         uint256 offset,
         uint256 len
     ) internal pure returns (bytes32 ret) {
-        require(offset + len <= self.length);
         assembly {
+            if gt(add(offset, len), mload(self)) {
+                revert(0, 0)
+            }
             ret := keccak256(add(add(self, 32), offset), len)
         }
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L192-L200

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 3795 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  297669  |  335604  |  316637 |    2     |

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol
192:    function readUint16(
193:        bytes memory self,
194:        uint256 idx
195:    ) internal pure returns (uint16 ret) {
196:        require(idx + 2 <= self.length);
197:        assembly {
198:            ret := and(mload(add(add(self, 2), idx)), 0xFFFF)
199:        }
200:    }
```
```diff
diff --git a/contracts/dnssec-oracle/BytesUtils.sol b/contracts/dnssec-oracle/BytesUtils.sol
index 96344ce..cd94ece 100644
--- a/contracts/dnssec-oracle/BytesUtils.sol
+++ b/contracts/dnssec-oracle/BytesUtils.sol
@@ -193,8 +193,10 @@ library BytesUtils {
         bytes memory self,
         uint256 idx
     ) internal pure returns (uint16 ret) {
-        require(idx + 2 <= self.length);
         assembly {
+            if gt(add(idx, 2), mload(self)) {
+                revert(0, 0)
+            }
             ret := and(mload(add(add(self, 2), idx)), 0xFFFF)
         }
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L208-L216

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 1449 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  300015  |  337950  |  318983 |    2     |

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol
208:    function readUint32(
209:        bytes memory self,
210:        uint256 idx
211:    ) internal pure returns (uint32 ret) {
212:        require(idx + 4 <= self.length);
213:        assembly {
214:            ret := and(mload(add(add(self, 4), idx)), 0xFFFFFFFF)
215:        }
216:    }
```
```diff
diff --git a/contracts/dnssec-oracle/BytesUtils.sol b/contracts/dnssec-oracle/BytesUtils.sol
index 96344ce..e5d3190 100644
--- a/contracts/dnssec-oracle/BytesUtils.sol
+++ b/contracts/dnssec-oracle/BytesUtils.sol
@@ -209,8 +209,10 @@ library BytesUtils {
         bytes memory self,
         uint256 idx
     ) internal pure returns (uint32 ret) {
-        require(idx + 4 <= self.length);
         assembly {
+            if gt(add(idx, 4), mload(self)) {
+                revert(0, 0)
+            }
             ret := and(mload(add(add(self, 4), idx)), 0xFFFFFFFF)
         }
     }
```

**This optimization can also be done for the instances below. However, they are not included in the final Diffs as they do not result in gas savings when the tests are run:**

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L224-L232

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L240-L251

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L260-L271

## Write a more gas efficient assembly loop
In the instance below, we can rewrite the assembly loop using a more gas efficient infinite loop, which performs the condition check at the end of the loop. See [this](https://www.youtube.com/watch?v=ew3pfnb2_V8&t=946s) for more information.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L44-L58

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 306 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301158  |  339093  |  320126 |    2     |

```solidity
File: contracts/utils/HexUtils.sol
44:            for {
45:                let i := idx
46:            } lt(i, lastIdx) {
47:                i := add(i, 2)
48:            } {
49:                let byte1 := getHex(byte(0, mload(add(ptr, i))))
50:                let byte2 := getHex(byte(0, mload(add(ptr, add(i, 1)))))
51:                // if either byte is invalid, set invalid and break loop
52:                if or(eq(byte1, 0xff), eq(byte2, 0xff)) {
53:                    valid := false
54:                    break
55:                }
56:                let combined := or(shl(4, byte1), byte2)
57:                r := or(shl(8, r), combined)
58:            }
```
```diff
diff --git a/contracts/utils/HexUtils.sol b/contracts/utils/HexUtils.sol
index 3508390..b8579b7 100644
--- a/contracts/utils/HexUtils.sol
+++ b/contracts/utils/HexUtils.sol
@@ -41,11 +41,8 @@ library HexUtils {
             }

             let ptr := add(str, 32)
-            for {
-                let i := idx
-            } lt(i, lastIdx) {
-                i := add(i, 2)
-            } {
+            let i := idx
+            for {} 1 {} {
                 let byte1 := getHex(byte(0, mload(add(ptr, i))))
                 let byte2 := getHex(byte(0, mload(add(ptr, add(i, 1)))))
                 // if either byte is invalid, set invalid and break loop
@@ -55,6 +52,8 @@ library HexUtils {
                 }
                 let combined := or(shl(4, byte1), byte2)
                 r := or(shl(8, r), combined)
+                i := add(i, 2)
+                if iszero(lt(i, lastIdx)) { break }
             }
         }
     }
```

## Use a `do while` loop instead of a `for` loop
A `do while` loop will cost less gas since the condition is not being checked for the first iteration.

**Note: Other optimizations, such as caching length, precrementing, and using unchecked blocks are not included in the Diff since they are included in the automated findings report.**

Total Instances: `8`

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L118-L126

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 60 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301404  |  339339  |  320372 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
118:        for (uint256 i = 0; i < input.length; i++) {
119:            RRUtils.SignedSet memory rrset = validateSignedSet(
120:                input[i],
121:                proof,
122:                now
123:            );
124:            proof = rrset.data;
125:            inception = rrset.inception;
126:        }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..c30d8ba 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -115,7 +115,8 @@ contract DNSSECImpl is DNSSEC, Owned {
         returns (bytes memory rrs, uint32 inception)
     {
         bytes memory proof = anchors;
-        for (uint256 i = 0; i < input.length; i++) {
+        uint256 i;
+        do {
             RRUtils.SignedSet memory rrset = validateSignedSet(
                 input[i],
                 proof,
@@ -123,7 +124,8 @@ contract DNSSECImpl is DNSSEC, Owned {
             );
             proof = rrset.data;
             inception = rrset.inception;
-        }
+            i++;
+        } while (i < input.length);
         return (proof, inception);
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L260-L274

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 62 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301402  |  339337  |  320370 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
260:        for (; !proof.done(); proof.next()) {
261:            bytes memory proofName = proof.name();
262:            if (!proofName.equals(rrset.signerName)) {
263:                revert ProofNameMismatch(rrset.signerName, proofName);
264:            }
265:
266:            bytes memory keyrdata = proof.rdata();
267:            RRUtils.DNSKEY memory dnskey = keyrdata.readDNSKEY(
268:                0,
269:                keyrdata.length
270:            );
271:            if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {
272:                return;
273:            }
274:        }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..a357e70 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -257,7 +257,7 @@ contract DNSSECImpl is DNSSEC, Owned {
         RRUtils.RRIterator memory proof
     ) internal view {
         // Check the DNSKEY's owner name matches the signer name on the RRSIG
-        for (; !proof.done(); proof.next()) {
+        do {
             bytes memory proofName = proof.name();
             if (!proofName.equals(rrset.signerName)) {
                 revert ProofNameMismatch(rrset.signerName, proofName);
@@ -271,7 +271,9 @@ contract DNSSECImpl is DNSSEC, Owned {
             if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {
                 return;
             }
-        }
+            proof.next();
+        } while (!proof.done());
+
         revert NoMatchingProof(rrset.signerName);
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L181-L213

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 140 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301324  |  339259  |  320292 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
181:    function validateRRs(
182:        RRUtils.SignedSet memory rrset,
183:        uint16 typecovered
184:    ) internal pure returns (bytes memory name) {
185:        // Iterate over all the RRs
186:        for (
187:            RRUtils.RRIterator memory iter = rrset.rrs();
188:            !iter.done();
189:            iter.next()
190:        ) {
191:            // We only support class IN (Internet)
192:            if (iter.class != DNSCLASS_IN) {
193:                revert InvalidClass(iter.class);
194:            }
195:
196:            if (name.length == 0) {
197:                name = iter.name();
198:            } else {
199:                // Name must be the same on all RRs. We do things this way to avoid copying the name
200:                // repeatedly.
201:                if (
202:                    name.length != iter.data.nameLength(iter.offset) ||
203:                    !name.equals(0, iter.data, iter.offset, name.length)
204:                ) {
205:                    revert InvalidRRSet();
206:                }
207:            }
208:
209:            // o  The RRSIG RR's Type Covered field MUST equal the RRset's type.
210:            if (iter.dnstype != typecovered) {
211:                revert SignatureTypeMismatch(iter.dnstype, typecovered);
212:            }
213:        }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..f09438a 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -183,11 +183,8 @@ contract DNSSECImpl is DNSSEC, Owned {
         uint16 typecovered
     ) internal pure returns (bytes memory name) {
         // Iterate over all the RRs
-        for (
-            RRUtils.RRIterator memory iter = rrset.rrs();
-            !iter.done();
-            iter.next()
-        ) {
+        RRUtils.RRIterator memory iter = rrset.rrs();
+        do {
             // We only support class IN (Internet)
             if (iter.class != DNSCLASS_IN) {
                 revert InvalidClass(iter.class);
@@ -210,7 +207,8 @@ contract DNSSECImpl is DNSSEC, Owned {
             if (iter.dnstype != typecovered) {
                 revert SignatureTypeMismatch(iter.dnstype, typecovered);
             }
-        }
+            iter.next();
+        } while (!iter.done());
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L330-L361

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 68 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301396  |  339331  |  320364 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
330:    function verifyWithDS(
331:        RRUtils.SignedSet memory rrset,
332:        RRSetWithSignature memory data,
333:        RRUtils.RRIterator memory proof
334:    ) internal view {
335:        uint256 proofOffset = proof.offset;
336:        for (
337:            RRUtils.RRIterator memory iter = rrset.rrs();
338:            !iter.done();
339:            iter.next()
340:        ) {
341:            if (iter.dnstype != DNSTYPE_DNSKEY) {
342:                revert InvalidProofType(iter.dnstype);
343:            }
344:
345:            bytes memory keyrdata = iter.rdata();
346:            RRUtils.DNSKEY memory dnskey = keyrdata.readDNSKEY(
347:                0,
348:                keyrdata.length
349:            );
350:            if (verifySignatureWithKey(dnskey, keyrdata, rrset, data)) {
351:                // It's self-signed - look for a DS record to verify it.
352:                if (
353:                    verifyKeyWithDS(rrset.signerName, proof, dnskey, keyrdata)
354:                ) {
355:                    return;
356:                }
357:                // Rewind proof iterator to the start for the next loop iteration.
358:                proof.nextOffset = proofOffset;
359:                proof.next();
360:            }
361:        }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..66cc74e 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -333,11 +333,8 @@ contract DNSSECImpl is DNSSEC, Owned {
         RRUtils.RRIterator memory proof
     ) internal view {
         uint256 proofOffset = proof.offset;
-        for (
-            RRUtils.RRIterator memory iter = rrset.rrs();
-            !iter.done();
-            iter.next()
-        ) {
+        RRUtils.RRIterator memory iter = rrset.rrs();
+        do {
             if (iter.dnstype != DNSTYPE_DNSKEY) {
                 revert InvalidProofType(iter.dnstype);
             }
@@ -358,7 +355,8 @@ contract DNSSECImpl is DNSSEC, Owned {
                 proof.nextOffset = proofOffset;
                 proof.next();
             }
-        }
+            iter.next();
+        } while (!iter.done());
         revert NoMatchingProof(rrset.signerName);
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L373-L404

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 68 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301396  |  339331  |  320364 |    2     |

```solidity
File: contracts/dnsssec-oracle/DNSSECImpl.sol
373:    function verifyKeyWithDS(
374:        bytes memory keyname,
375:        RRUtils.RRIterator memory dsrrs,
376:        RRUtils.DNSKEY memory dnskey,
377:        bytes memory keyrdata
378:    ) internal view returns (bool) {
379:        uint16 keytag = keyrdata.computeKeytag();
380:        for (; !dsrrs.done(); dsrrs.next()) {
381:            bytes memory proofName = dsrrs.name();
382:            if (!proofName.equals(keyname)) {
383:                revert ProofNameMismatch(keyname, proofName);
384:            }
385:
386:            RRUtils.DS memory ds = dsrrs.data.readDS(
387:                dsrrs.rdataOffset,
388:                dsrrs.nextOffset - dsrrs.rdataOffset
389:            );
390:            if (ds.keytag != keytag) {
391:                continue;
392:            }
393:            if (ds.algorithm != dnskey.algorithm) {
394:                continue;
395:            }
396:
397:            Buffer.buffer memory buf;
398:            buf.init(keyname.length + keyrdata.length);
399:            buf.append(keyname);
400:            buf.append(keyrdata);
401:            if (verifyDSHash(ds.digestType, buf.buf, ds.digest)) {
402:                return true;
403:            }
404:        }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..ed1c137 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -377,7 +377,7 @@ contract DNSSECImpl is DNSSEC, Owned {
         bytes memory keyrdata
     ) internal view returns (bool) {
         uint16 keytag = keyrdata.computeKeytag();
-        for (; !dsrrs.done(); dsrrs.next()) {
+        do {
             bytes memory proofName = dsrrs.name();
             if (!proofName.equals(keyname)) {
                 revert ProofNameMismatch(keyname, proofName);
@@ -388,9 +388,11 @@ contract DNSSECImpl is DNSSEC, Owned {
                 dsrrs.nextOffset - dsrrs.rdataOffset
             );
             if (ds.keytag != keytag) {
+                dsrrs.next();
                 continue;
             }
             if (ds.algorithm != dnskey.algorithm) {
+                dsrrs.next();
                 continue;
             }

@@ -401,7 +403,8 @@ contract DNSSECImpl is DNSSEC, Owned {
             if (verifyDSHash(ds.digestType, buf.buf, ds.digest)) {
                 return true;
             }
-        }
+            dsrrs.next();
+        } while (!dsrrs.done());
         return false;
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L29-L40

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 42 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301422  |  339357  |  320390 |    2     |

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol
29:        for (
30:            RRUtils.RRIterator memory iter = data.iterateRRs(0);
31:            !iter.done();
32:            iter.next()
33:        ) {
34:            if (iter.name().compareNames(buf.buf) != 0) continue;
35:            bool found;
36:            address addr;
37:            (addr, found) = parseRR(data, iter.rdataOffset, iter.nextOffset);
38:            if (found) {
39:                return (addr, true);
40:            }
```
```diff
diff --git a/contracts/dnsregistrar/DNSClaimChecker.sol b/contracts/dnsregistrar/DNSClaimChecker.sol
index 54950d1..7848671 100644
--- a/contracts/dnsregistrar/DNSClaimChecker.sol
+++ b/contracts/dnsregistrar/DNSClaimChecker.sol
@@ -26,19 +26,20 @@ library DNSClaimChecker {
         buf.append("\x04_ens");
         buf.append(name);

-        for (
-            RRUtils.RRIterator memory iter = data.iterateRRs(0);
-            !iter.done();
-            iter.next()
-        ) {
-            if (iter.name().compareNames(buf.buf) != 0) continue;
+        RRUtils.RRIterator memory iter = data.iterateRRs(0);
+        do {
+            if (iter.name().compareNames(buf.buf) != 0) {
+                iter.next();
+                continue;
+            }
             bool found;
             address addr;
             (addr, found) = parseRR(data, iter.rdataOffset, iter.nextOffset);
             if (found) {
                 return (addr, true);
             }
-        }
+            iter.next();
+        } while (!iter.done());

         return (address(0x0), false);
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L46-L61

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 36 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301428  |  339363  |  320396 |    2     |

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol
46:    function parseRR(
47:        bytes memory rdata,
48:        uint256 idx,
49:        uint256 endIdx
50:    ) internal pure returns (address, bool) {
51:        while (idx < endIdx) {
52:            uint256 len = rdata.readUint8(idx);
53:            idx += 1;
54:
55:            bool found;
56:            address addr;
57:            (addr, found) = parseString(rdata, idx, len);
58:
59:            if (found) return (addr, true);
60:            idx += len;
61:        }
```
```diff
diff --git a/contracts/dnsregistrar/DNSClaimChecker.sol b/contracts/dnsregistrar/DNSClaimChecker.sol
index 54950d1..55bb5d2 100644
--- a/contracts/dnsregistrar/DNSClaimChecker.sol
+++ b/contracts/dnsregistrar/DNSClaimChecker.sol
@@ -48,9 +48,9 @@ library DNSClaimChecker {
         uint256 idx,
         uint256 endIdx
     ) internal pure returns (address, bool) {
-        while (idx < endIdx) {
+        do {
             uint256 len = rdata.readUint8(idx);
-            idx += 1;
+            ++idx;

             bool found;
             address addr;
@@ -58,7 +58,7 @@ library DNSClaimChecker {

             if (found) return (addr, true);
             idx += len;
-        }
+        } while (idx < endIdx);

         return (address(0x0), false);
     }
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L384-L399

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 406 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301072  |  339007  |  320026 |    2     |

```solidity
File: contracts/dnssec-oracle/RRUtils.sol
384:            for (uint256 i = 0; i < data.length + 31; i += 32) {
385:                uint256 word;
386:                assembly {
387:                    word := mload(add(add(data, 32), i))
388:                }
389:                if (i + 32 > data.length) {
390:                    uint256 unused = 256 - (data.length - i) * 8;
391:                    word = (word >> unused) << unused;
392:                }
393:                ac1 +=
394:                    (word &
395:                        0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>
396:                    8;
397:                ac2 += (word &
398:                    0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);
399:            }
```
```diff
diff --git a/contracts/dnssec-oracle/RRUtils.sol b/contracts/dnssec-oracle/RRUtils.sol
index 20fbf15..e9309d6 100644
--- a/contracts/dnssec-oracle/RRUtils.sol
+++ b/contracts/dnssec-oracle/RRUtils.sol
@@ -381,13 +381,15 @@ library RRUtils {
             require(data.length <= 8192, "Long keys not permitted");
             uint256 ac1;
             uint256 ac2;
-            for (uint256 i = 0; i < data.length + 31; i += 32) {
+            uint256 length = data.length;
+            uint256 i;
+            do {
                 uint256 word;
                 assembly {
                     word := mload(add(add(data, 32), i))
                 }
-                if (i + 32 > data.length) {
-                    uint256 unused = 256 - (data.length - i) * 8;
+                if (i + 32 > length) {
+                    uint256 unused = 256 - (length - i) * 8;
                     word = (word >> unused) << unused;
                 }
                 ac1 +=
@@ -396,7 +398,8 @@ library RRUtils {
                     8;
                 ac2 += (word &
                     0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);
-            }
+                i += 32;
+            } while (i < data.length + 31);
             ac1 =
                 (ac1 &
                     0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +
```

## Don't cache value if it is only used once
If a value is only intended to be used once then it should not be cached. Caching the value will result in unnecessary stack manipulation.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L304-L307

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 90 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301374  |  339309  |  320342 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
304:        uint16 computedkeytag = keyrdata.computeKeytag();
305:        if (computedkeytag != rrset.keytag) {
306:            return false;
307:        }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..c6c03fc 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -301,8 +301,7 @@ contract DNSSECImpl is DNSSEC, Owned {
         if (dnskey.algorithm != rrset.algorithm) {
             return false;
         }
-        uint16 computedkeytag = keyrdata.computeKeytag();
-        if (computedkeytag != rrset.keytag) {
+        if (keyrdata.computeKeytag() != rrset.keytag) {
             return false;
         }
```

## Refactor code to avoid instantiating memory struct within loop
In the instance below, instead of instantiating the `SignedSets` struct within memory in the loop we can refactor the `validateSignedSet` function so that only the needed struct values are returned and used in the loop.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L118-L174

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 322 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301142  |  339077  |  320110 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
118:        for (uint256 i = 0; i < input.length; i++) {
119:            RRUtils.SignedSet memory rrset = validateSignedSet(
120:                input[i],
121:                proof,
122:                now
123:            );
124:            proof = rrset.data;
125:            inception = rrset.inception;
126:        }

140:    function validateSignedSet(
141:        RRSetWithSignature memory input,
142:        bytes memory proof,
143:        uint256 now
144:    ) internal view returns (RRUtils.SignedSet memory rrset) {
145:        rrset = input.rrset.readSignedSet();

173:        return rrset;
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..a6a184c 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -116,13 +116,13 @@ contract DNSSECImpl is DNSSEC, Owned {
     {
         bytes memory proof = anchors;
         for (uint256 i = 0; i < input.length; i++) {
-            RRUtils.SignedSet memory rrset = validateSignedSet(
+            (bytes memory rrsetData, uint32 rrsetInception) = validateSignedSet(
                 input[i],
                 proof,
                 now
             );
-            proof = rrset.data;
-            inception = rrset.inception;
+            proof = rrsetData;
+            inception = rrsetInception;
         }
         return (proof, inception);
     }
@@ -141,8 +141,8 @@ contract DNSSECImpl is DNSSEC, Owned {
         RRSetWithSignature memory input,
         bytes memory proof,
         uint256 now
-    ) internal view returns (RRUtils.SignedSet memory rrset) {
-        rrset = input.rrset.readSignedSet();
+    ) internal view returns (bytes memory, uint32) {
+        RRUtils.SignedSet memory rrset = input.rrset.readSignedSet();

         // Do some basic checks on the RRs and extract the name
         bytes memory name = validateRRs(rrset, rrset.typeCovered);
@@ -170,7 +170,7 @@ contract DNSSECImpl is DNSSEC, Owned {
         // Validate the signature
         verifySignature(name, rrset, input, proof);

-        return rrset;
+        return (rrset.data, rrset.inception);
     }
```

## `If` statements that use `&` can be refactored into nested `if` statements

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L312-L314

*Gas Savings for `DNSRegistrar.proveAndClaimWithResolver`, obtained via protocol's tests: Avg 64 gas* 

|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  301464  |  339399  |  320432 |    2     |
| After  |  301400  |  339335  |  320368 |    2     |

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol
312:        if (dnskey.flags & DNSKEY_FLAG_ZONEKEY == 0) {
313:            return false;
314:        }
```
```diff
diff --git a/contracts/dnssec-oracle/DNSSECImpl.sol b/contracts/dnssec-oracle/DNSSECImpl.sol
index a3e4e5f..e442944 100644
--- a/contracts/dnssec-oracle/DNSSECImpl.sol
+++ b/contracts/dnssec-oracle/DNSSECImpl.sol
@@ -309,8 +309,10 @@ contract DNSSECImpl is DNSSEC, Owned {
         // o The matching DNSKEY RR MUST be present in the zone's apex DNSKEY
         //   RRset, and MUST have the Zone Flag bit (DNSKEY RDATA Flag bit 7)
         //   set.
-        if (dnskey.flags & DNSKEY_FLAG_ZONEKEY == 0) {
-            return false;
+        if (dnskey.flags == 0) {
+            if (DNSKEY_FLAG_ZONEKEY == 0) {
+                return false;
+            }
         }
```

## Refactor modifier to avoid two external calls when calling `setPublicSuffixList`
The `onlyOwner` modifier performs two external calls. In order to avoid these two calls everytime `setPublicSufficList` is called, we can perform these two calls in the constructor and create immutable variables for the return values.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol
73:    modifier onlyOwner() {
74:        Root root = Root(ens.owner(bytes32(0)));
75:        address owner = root.owner();
76:        require(msg.sender == owner);
77:        _;
78:    }
```
```diff
diff --git a/contracts/dnsregistrar/DNSRegistrar.sol b/contracts/dnsregistrar/DNSRegistrar.sol
index 953a9a3..3779292 100644
--- a/contracts/dnsregistrar/DNSRegistrar.sol
+++ b/contracts/dnsregistrar/DNSRegistrar.sol
@@ -28,6 +28,7 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {
     PublicSuffixList public suffixes;
     address public immutable previousRegistrar;
     address public immutable resolver;
+    address private immutable rootOwner;
     // A mapping of the most recent signatures seen for each claimed domain.
     mapping(bytes32 => uint32) public inceptions;

@@ -65,15 +66,14 @@ contract DNSRegistrar is IDNSRegistrar, IERC165 {
         suffixes = _suffixes;
         emit NewPublicSuffixList(address(suffixes));
         ens = _ens;
+        rootOwner = Root(_ens.owner(bytes32(0))).owner();
     }

     /**
      * @dev This contract's owner-only functions can be invoked by the owner of the ENS root.
      */
     modifier onlyOwner() {
-        Root root = Root(ens.owner(bytes32(0)));
-        address owner = root.owner();
-        require(msg.sender == owner);
+        require(msg.sender == rootOwner);
         _;
     }
```

## `GasReport` output, with all optimizations applied
```js
·--------------------------------------------------------|---------------------------|--------------|-----------------------------·
|                  Solc version: 0.8.17                  ·  Optimizer enabled: true  ·  Runs: 1300  ·  Block limit: 30000000 gas  │
·························································|···························|··············|······························
|  Methods                                                                                                                        │
···························|·····························|·············|·············|··············|···············|··············
|  Contract                ·  Method                     ·  Min        ·  Max        ·  Avg         ·  # calls      ·  eur (avg)  │
···························|·····························|·············|·············|··············|···············|··············
|  DNSRegistrar            ·  proveAndClaim              ·     170611  ·     277955  ·      240737  ·            7  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  DNSRegistrar            ·  proveAndClaimWithResolver  ·     272572  ·     310200  ·      291386  ·            2  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  DNSSECImpl              ·  setAlgorithm               ·      47660  ·      47672  ·       47671  ·           96  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  DNSSECImpl              ·  setDigest                  ·      47703  ·      47727  ·       47726  ·           48  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  ENSRegistry             ·  setOwner                   ·      28697  ·      28721  ·       28719  ·           21  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  ENSRegistry             ·  setResolver                ·      48254  ·      48266  ·       48265  ·          112  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  ENSRegistry             ·  setSubnodeOwner            ·      48998  ·      49394  ·       49319  ·          286  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  ERC20Recoverable        ·  recoverFunds               ·          -  ·          -  ·       35298  ·            1  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  MockERC20               ·  transfer                   ·          -  ·          -  ·       51378  ·            2  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  OwnedResolver           ·  setAddr                    ·      53760  ·      53772  ·       53770  ·            7  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  PublicResolver          ·  setAddr                    ·          -  ·          -  ·       58510  ·           22  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  PublicResolver          ·  setApprovalForAll          ·          -  ·          -  ·       46189  ·            1  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  PublicResolver          ·  setName                    ·          -  ·          -  ·       55359  ·           22  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  PublicResolver          ·  setText                    ·          -  ·          -  ·       57629  ·           22  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  ReverseRegistrar        ·  claim                      ·      60960  ·      60972  ·       60966  ·           44  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  Root                    ·  setController              ·          -  ·          -  ·       47813  ·           30  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  Root                    ·  setSubnodeOwner            ·          -  ·          -  ·       58638  ·            1  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
|  SimplePublicSuffixList  ·  addPublicSuffixes          ·      47573  ·      71045  ·       68810  ·           21  ·          -  │
···························|·····························|·············|·············|··············|···············|··············
```