## Inefficient memory storage use
Consider declaring variables outside of loops and updating their values within the loop instead of repeatedly re-declaring them on each loop iteration for gas efficiency reason where possible.

For example, the specific instance below may be refactored as follows:

[File: BytesUtils.sol#L77-L97](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L77-L97)

```diff
+        uint256 a;
+        uint256 b;
+        uint256 mask;
+        int256 diff;

        for (uint256 idx = 0; idx < shortest; idx += 32) {
-            uint256 a;
-            uint256 b;
            assembly {
                a := mload(selfptr)
                b := mload(otherptr)
            }
            if (a != b) {
                // Mask out irrelevant bytes and check again
-                uint256 mask;
                if (shortest - idx >= 32) {
                    mask = type(uint256).max;
                } else {
                    mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
                }
-                int256 diff = int256(a & mask) - int256(b & mask);
+                diff = int256(a & mask) - int256(b & mask);
                if (diff != 0) return diff;
            }
            selfptr += 32;
            otherptr += 32;
        }
```
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: DNSSECImpl.sol#L415-L424](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L415-L424)

```diff
    function verifyDSHash(
        uint8 digesttype,
        bytes memory data,
        bytes memory digest
-    ) internal view returns (bool) {
+    ) internal view returns (bool _status) {
        if (address(digests[digesttype]) == address(0)) {
            return false;
        }
-        return digests[digesttype].verify(data, digest);
+        _status = digests[digesttype].verify(data, digest);
    }
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: RRUtils.sol#L304](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L304)

```diff
-        while (counts > 0 && !self.equals(off, other, otheroff)) {
+        while (!(counts == 0 || self.equals(off, other, otheroff))) {
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

[File: RRUtils.sol#L160](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L160)

```diff
-        if (iter.offset >= iter.data.length) {
        // Rationale for subtracting 1 on the right side of the inequality:
        // x >= 10; [10, 11, 12, ...]
        // x > 10 - 1 is the same as x > 9; [10, 11, 12 ...]
+        if (iter.offset > iter.data.length - 1) {
```
Similarly, the `<=` instance below may be replaced with `<` as follows:

[File: RRUtils.sol#L381](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L381)

```diff
-            require(data.length <= 8192, "Long keys not permitted");
            // Rationale for adding 1 on the right side of the inequality:
            // x <= 10; [10, 9, 8, ...]
            // x < 10 + 1 is the same as x < 11; [10, 9, 8 ...]
+            require(data.length < 8193, "Long keys not permitted");
```
## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A `private` visibility that saves more gas on function calls than the `internal` visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

```diff
+    function _onlyOwner() private view {
+        Root root = Root(ens.owner(bytes32(0)));
+        address owner = root.owner();
+        require(msg.sender == owner);
+    }

    modifier onlyOwner() {
-        Root root = Root(ens.owner(bytes32(0)));
-        address owner = root.owner();
-        require(msg.sender == owner);
+        _onlyOwner();
        _;
    }
```
## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.4",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Private over internal visibility 
Contracts not intended to be inherited could have their internal functions resort to private visibility to save gas on calls.

For instance, `parseSignature()` and `parseKey()` solely called from within P256SHA256Algorithm.sol by `verify()` may be refactored as follows:

[File: P256SHA256Algorithm.sol#L17-L42](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L17-L42) 

```diff
    function verify(
        bytes calldata key,
        bytes calldata data,
        bytes calldata signature
    ) external view override returns (bool) {
        return
            validateSignature(
                sha256(data),
                parseSignature(signature),
                parseKey(key)
            );
    }

    function parseSignature(
        bytes memory data
-    ) internal pure returns (uint256[2] memory) {
+    ) private pure returns (uint256[2] memory) {
        require(data.length == 64, "Invalid p256 signature length");
        return [uint256(data.readBytes32(0)), uint256(data.readBytes32(32))];
    }

    function parseKey(
        bytes memory data
-    ) internal pure returns (uint256[2] memory) {
+    ) private pure returns (uint256[2] memory) {
        require(data.length == 68, "Invalid p256 key length");
        return [uint256(data.readBytes32(4)), uint256(data.readBytes32(36))];
    }
```
## Unused imports
Consider removing unused imports to help save gas on contract deployment.

Here is a specific instance entailed:

[File: DNSClaimChecker.sol#L4](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L4)

```diff
-import "../dnssec-oracle/DNSSEC.sol";
```
## Exponent length handling
The if else code logic of `RSASHA256Algorithm.verify()` assumes that if the exponent length is `0`, it should read the next two bytes as the exponent length. However, it does not validate if the given key is long enough to actually contain the exponent and modulus after reading the exponent length. 

Consider adding a check to ensure the key's length is sufficient to help circumvent underflow issue on line 33 while preventing unneeded gas waste as follows:

[File: RSASHA256Algorithm.sol#L13-L44](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L13-L44)

```diff
    function verify(
        bytes calldata key,
        bytes calldata data,
        bytes calldata sig
    ) external view override returns (bool) {
        bytes memory exponent;
        bytes memory modulus;

        uint16 exponentLen = uint16(key.readUint8(4));
        if (exponentLen != 0) {
            exponent = key.substring(5, exponentLen);
            modulus = key.substring(
                exponentLen + 5,
                key.length - exponentLen - 5
            );
        } else {
+            require(key.length >= 7); // or use a number greater than 7 where deemed fit
            exponentLen = key.readUint16(5);
            exponent = key.substring(7, exponentLen);
            modulus = key.substring(
                exponentLen + 7,
33:                key.length - exponentLen - 7
            );
        }

        // Recover the message from the signature
        bool ok;
        bytes memory result;
        (ok, result) = RSAVerify.rsarecover(modulus, exponent, sig);

        // Verify it ends with the hash of our data
        return ok && sha256(data) == result.readBytes32(result.length - 32);
    }
```
## State variables repeatedly read should be cached
SLOADs cost 100 gas each after the 1st one whereas MLOADs/MSTOREs only incur 3 gas each. As such, storage values read multiple times should be cached in the stack memory the first time (costing only 1 SLOAD) and then re-read from this cache to avoid multiple SLOADs.

For instance, `ens` in `DNSRegistrar._enableNode()` may be cached as follows:

[File: DNSRegistrar.sol#L186-L200](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L186-L200)

```diff
+        ENS _ens ens; // SLOAD1

-        address owner = ens.owner(node);
+        address owner = _ens.owner(node); // MLOAD1
        if (owner == address(0) || owner == previousRegistrar) {
            if (parentNode == bytes32(0)) {
-                Root root = Root(ens.owner(bytes32(0)));
+                Root root = Root(_ens.owner(bytes32(0))); // MLOAD2
                root.setSubnodeOwner(label, address(this));
-                ens.setResolver(node, resolver);
+                _ens.setResolver(node, resolver); // MLOAD3
            } else {
-                ens.setSubnodeRecord(
+                _ens.setSubnodeRecord( // MLOAD4
                    parentNode,
                    label,
                    address(this),
                    resolver,
                    0
                );
            }
```
## Unused event 
The [Debug event](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4) in SHA1.sol is declared but never used in the library. Neither is the library inherited to have the event used elsewhere of the system codebase. It's a good practice to either remove unused code (save gas on contract size) or utilize events to emit relevant information for external callers and tracking.