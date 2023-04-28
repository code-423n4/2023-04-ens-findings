| ID    | Title |
| -------- | ------- |
| 01 | Lack of address(0) checks
| 02 | Large or complicated contracts should implement fuzzing tests
| 03 | Validation for `inception` in `DNSRegistrar._claim` can be done sooner
| 04 | `inception`, `expiration` and `now` can be the same value
| 05 | `EllipticCurve.inverseMod()` is susceptible to arithmetic issues
| 06 | Unbounded recursive calls in `DNSRegistrar._enableNode()`
| 07 | Lack of validation for `self` and `len` in `BytesUtils.keccak()`
| 08 | Lack of validation for `offset`, `otheroffset`, `len` and `otherlen` in `BytesUtils.compare()`
| 09 | Lack of validation for `len` in `BytesUtils.memcpy()`
| 10 | State should be modified before external calls when possible
| 11 | Emit events before external calls
| 12 | Check for stale values in setter functions what emit events
| 13 | Lack of old and new value for events related to parameter updates
| 14 | Add a setter function for `anchors`
| 15 | Prefer OZ ownable rather than custom owner contract
| 16 | Unused struct
| 17 | Consistency for line break in conditionals
| 18 | `DNSSECImpl.validateRRs()` could receive rrset as a single argument
| 19 | Lack of spacing in SPDX license
| 20 | Constant redefined elsewhere
| 21 | Variable declared for interfaces should the prefix I
| 22 | Can simplify two statements into one in `DNSRegistrar.onlyOwner` modifier
| 23 | Reference read-only type function argument can be calldata
| 24 | Add comments for the contracts explaining it's usage and intent
| 25 | Variable shadow 
| 26 | Give a more descriptive name for variables
| 27 | Add NATSPEC on the interfaces
| 28 | Add variables on custom erros
| 29 | Contracts with the same name
| 30 | Unused return named variable
| 31 | Use return named variables or explicit returns consistently
| 32 | Use custom errors or require statements consistently
| 33 | Order of functions
| 34 | Refactor comments to have 120 chars or less per line
| 35 | Prefer camel case
| 36 | Replace magic numbers with constants
| 37 | Declare `offset` consistently
| 38 | Loop can be improved
| 39 | Proper casing for computedkeytag
| 40 | Replace "o" with the RFC in the comments
| 41 | Similar fields can be grouped in a struct
| 42 | Type can be declared within the definition
| 43 | Use constants for reusable numeric values

# [01] Lack of address(0) checks

If a variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

Consider adding address(0) checks for `_previousRegistrar` and `_resolver` in `DNSRegistrar.sol` constructor.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L56-L57

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62-L63

# [02] Large or complicated contracts should implement fuzzing tests

Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement fuzzing tests.

Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. 

Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

# [03] Validation for `inception` in `DNSRegistrar._claim` can be done sooner

The `inception` validation could be done right after `inception` is extracted from `oracle.verifyRRSet(input)`. 

If the function succeeds, it won't make a difference. However if the inception validation reverts the Tx, having the validation sooner will make the caller pay less gas. Currently, since `enableNode` will be called first, it might involve expensive recursive function calls only to revert in the end (if the inception is incorrect).

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L152-L154

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L137

# [04] `inception`, `expiration` and `now` can be the same value

`DNSSECImpl.validateSignedSet()` is using the serial number math and it will make the checks outlined in the RFC which includes a case where `inception` can be equal to the current time and `expiration` can also be equal to the current time. Having exactly the same `inception` and `expiration` seems counter-intuitive and doesn't seem to have any practical usage since it just expires immediately.

Consider adding a check to validate if the `inception` is not equal to the `expiration`, e.g.

```
if (rrset.inception == rrset.expiration) {
    revert CustomError();
}
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L158-L168

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L332-L340

# [05] `EllipticCurve.inverseMod()` is susceptible to arithmetic issues

Since there's a division being applied before a multiplication for the following calculations:

```
while (r2 != 0) {
    q = r1 / r2;
    (t1, t2, r1, r2) = (t2, t1 - int256(q) * t2, r2, r1 - q * r2);
}

```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L51-L54

It's possible for r2 to be bigger than r1 which would cause q to round down. This would cause the resulting variables specially t1 to have an incorrect value. In turn, this can generate math errors when calling `EllipticCurve.verifySignature()`, which will be called by `P256SHA256Algorithm.verify()`. Thus, calling this algorithm in `DNSSECImpl.verify` would validate signatures incorrectly.

## Recommendation

Perform the multiplications before the divisions when computing q, t1, t2, r1 and r2 in `EllipticCurve.inverseMod()`.

# [06] Unbounded recursive calls in `DNSRegistrar._enableNode()`

`DNSRegistrar._enableNode()` will be called N times recursively depending on the bytes `domain` and the first byte extracted from the domain (by using `domain.readUint8(offset))`. It might be beneficial to add a validation of the `domain` length before making this call, to prevent large domains consuming all of the gas. Adding a validation would revert earlier and the caller would have to pay less gas (for failed Txs in this case). One upper bound that could be used for this validation is that the original domain should not have more than 63 characters (before converting to bytes).

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L183

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L174-L177

# [07] Lack of validation for `self` and `len` in `BytesUtils.keccak()`

Consider validating that `len` is not zero and `self` is not empty in the keccak utility. This can help reverting when receiving bad inputs.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L13-L22

# [08] Lack of validation for `offset`, `otheroffset`, `len` and `otherlen` in `BytesUtils.compare()`

Consider adding a input validation on `BytesUtils.compare` to check if `offset`, `otheroffset`, `len` and `otherlen` are multiples of 32. Otherwise, the function might not compare the correct number of bytes.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L52-L100

# [09] Lack of validation for `len` in `BytesUtils.memcpy()`

Consider adding a check in `BytesUtils.memcpy()` to ensure `len` is smaller than 32, otherwise it will read and write memory from the same address.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L273-L292

# [10] State should be modified before external calls when possible

The logic for including an inception in the inceptions mapping in `DNSRegistrar._claim()` should be done before calling `DNSRegistrar.enableNode()` since `enableNode()` will makes calls to the `ENS.sol` contract. Modify the state before external calls is important to follow the checks-effects-interactions pattern which is recommended to prevent reentrancy attacks.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L149

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L152-L155

# [11] Emit events before external calls

Wherever possible, consider emitting events before external calls. In case of reentrancy, funds are not at risk (for external call + event ordering), however emitting events after external calls can damage frontends and monitoring tools in case of reentrancy attacks.

On the example bellow, consider moving the `emit Claim` statement before calling `enableNode()` in `DNSRegistrar._claim()`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L163

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L149

# [12] Check for stale values in setter functions what emit events

Consider adding checks on `DNSSECImpl.setAlgorithm()` and `DNSSECImpl.setDigest()` to prevent stale values, e.g.

```
function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
    if (algorithms[id] == algo) {
        revert CustomError();
    }
    algorithms[id] = algo;
    emit AlgorithmUpdated(id, address(algo));
}
```

This can prevent emitting unecessary events.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L64-L67

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L75-L78

# [13] Lack of old and new value for events related to parameter updates

Events that mark critical parameter changes should contain both the old and the new value

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83

# [14] Add a setter function for `anchors`

Currently `anchors` in `DNSSECImpl.sol` is not immutable but it's only set in the constructor. It might be beneficial to create a setter function for this data structure since. Otherwise, in case it's deployed incorrectly, the protocol will need to do a redeployment.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L52-L56

# [15] Prefer OZ ownable rather than custom owner contract

Since OpenZeppelin `Ownable.sol` is extensively tested and has the same functionality as `Owned.sol`, it could be beneficial to use the OpenZeppelin version instead of `Owned.sol` in `DNSSECImpl.sol`. Also, `Ownable.sol` is already being used in `Root.sol`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L22

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/Owned.sol

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/root/Root.sol#L7

# [16] Unused struct

The struct `OwnerRecord` is not being used on the codebase.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L40-L45

# [17] Consistency for line break in conditionals

For conditionals with only one statement, consider using only one approach throughout the codebase, e.g. using it on the same line or breaking a line.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L59

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L149-L151

# [18] `DNSSECImpl.validateRRs()` could receive rrset as a single argument

Since typeCovered will be a field on rrset, it could be deconstructed inside `DNSSECImpl.validateRRs()`. This way the only argument needed would be `rrset`. Decreasing the number of function arguments can be beneficial in reducing the function complexity and testing.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L148

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L181-L214

# [19] Lack of spacing in SPDX license

Consider convering `//SPDX-License-Identifier: MIT` into `// SPDX-License-Identifier: MIT`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L1

# [20] Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated.

A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L29-L30

# [21] Variable declared for interfaces should the prefix I

Consider adding the prefix "I" or "_I" to highlight variable being declared from interfaces to facilitate differentiating interface variable to regular variables.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L60

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L67

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENS.sol

# [22] Can simplify two statements into one in `DNSRegistrar.onlyOwner` modifier

The current `DNSRegistrar.onlyOwner` could be refactored to the following to improve the simplicity of the code.

```
modifier onlyOwner() {
    Root root = Root(ens.owner(bytes32(0)));
    require(msg.sender == root.owner());
    _;
}
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78

# [23] Reference read-only type function argument can be calldata

Consider replacing memory with calldata for read only function inputs, e.g. name and input can be calldata for `DNSRegistrar.proveAndClaim()` and `DNSRegistrar._claim()`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L91-L92

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L134-L135

# [24] Add comments for the contracts explaining it's usage and intent

It could be beneficial to add comments for each contract to explain it's usage and context. This would help auditors and developer reading the source code that are not familiar with the project.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L32

# [25] Variable shadow 

Consider renaming the argument `resolver` in `DNSRegistrar.proveAndClaimWithResolver()`. Currently it's being shadow by the `resolver` state variable in the same contract.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L104

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L30

# [26] Give a more descriptive name for variables

It will be useful to auditors and developers reading the code for variables to have a descriptive name. For example, since solidity is a typed language, declaring an address variable as `addr` is implicit with regards to the meaning of the variable and it's intention.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L105

# [27] Add NATSPEC on the interfaces

Adding NATSPEC on the interfaces will help understand the intended functionalities for interfaces written by ENS currently being called within the project.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L193

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/registry/ENS.sol#L30-L36

# [28] Add variables on custom erros

Avoid leaving custom error empty and add variables when possible. This can while debugging.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L201-L203

# [29] Contracts with the same name

`./contracts/dnssec-oracle/BytesUtils.sol` (in scope) and `./contracts/wrapper/BytesUtils` (not in scope) have the same. Even if the wrapper is used for testing or some other reason, consider giving another name, since having multiple contracts with the same name can cause problems on the compilation artifacts.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/wrapper/BytesUtils.sol

# [30] Unused return named variable

Consider either removing the variable in the function header or declaring and returning in the function scope.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L182-L184

# [31] Use return named variables or explicit returns consistently

Some functions return named variables, others return explicit values.

Following function returns an explicit value.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L300-L304

Following function return returns a named variable.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L260-L264

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

# [32] Use custom errors or require statements consistently

Consider using only one approach throughout the codebase to improve the consistency of the codebase.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L196

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L168-L170

# [33] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows internal above public.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L133-L136

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L166

# [34] Refactor comments to have 120 chars or less per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

Please refer to this [example](https://github.com/ProjectOpenSea/seaport/blob/main/contracts/conduit/Conduit.sol#L28-L35) for comments wrapping to avoid having large lines.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L81

# [35] Prefer camel case

Consider refactoring camel cased definitions to pascal case, since camel case is the recommended format for solidity.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/Owned.sol#L9

# [36] Replace magic numbers with constants

Using constants instead of magic numbers will improve code readability.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L22

# [37] Declare `offset` consistently

Consider declaring the variable offset consistently, e.g. using only `offset` or `off`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L15

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L389

# [38] Loop can be improved

The loop in `DNSSECImplLoop.verifyRRSet()` can be refactored to:
- Don't initialize i with zero (since it's the default value for uint256)
- Cache array.length outside the loop
- Increment can be prefixed with saves gas, e.g. replace i++ with ++i

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L118

# [39] Proper casing for computedkeytag

Consider refactoring `uint16 computedkeytag` to `uint16 computedKeytag` to achieve improved consistency.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L304

# [40] Replace "o" with the RFC in the comments

It could be beneficial to quote the RFC instead of "o" in the comments mentioning RFCs, e.g. the examples bellow could contain [RFC ID], where ID is the RFC with the specification. This could facilitate research for auditors/developers reading the code.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L209

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L231

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L298

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L309

# [41] Similar fields can be grouped in a struct

Consider grouping fields in a struct by type, e.g. consider refactoring `SignedSet` to the following.

```
struct SignedSet {
    uint8 algorithm;
    uint8 labels;
    uint16 typeCovered;
    uint16 keytag;
    uint32 ttl;
    uint32 expiration;
    uint32 inception;
    bytes signerName;
    bytes data;
    bytes name;
}
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L81-L92

# [42] Type can be declared within the definition

`RSASHA256Algorithm.verify` can be refactored to the following:

```
(bool ok, bytes memory result) = RSAVerify.rsarecover(modulus, exponent, sig);
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L37-L40

# [43] Use constants for reusable numeric values

On the examples bellow, 5 and 7 could be constants to improve code readability.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L23

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L25-L26

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L29

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L30-L33
