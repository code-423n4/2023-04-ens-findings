# Low Risk Issues

## 1. Low Level Calls With Solidity Version Before ```0.8.14``` Can Result In Optimiser Bug

### Summary

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug. 

https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference: https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L276


## 2. Contracts are not using their OZ Upgradeable counterparts

### Summary

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts. It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L5
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L7

## 3. Pragma Experimental ```ABIEncoderV2``` is Deprecated

So use pragma abicoder v2 instead.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L3

## 4. Remove unused code

### Summary

This code is not used in the main project contract files, remove it or add event-emit Code that is not in use, suggests that they should not be present and could potentially contain insecure functionalities.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L316

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L377

## 5. ```require()``` Should Be Used Instead Of ```assert()```

### Summary

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L169

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L25

## 6. Admin privilege - A single point of failure can allow a hacked or malicious owner use critical functions in the project

### Summary

The owner role has a single point of failure and onlyOwner can use critical a few functions.

owner role in the project: Owner is not behind a multisig and changes are not behind a timelock.

Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.

Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Allow only multi-signature wallets to call the function to reduce the likelihood of an attack.

https://twitter.com/danielvf/status/1572963475101556738?s=20&t=V1kvzfJlsx-D2hfnG0OmuQ

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L80

## 7. Upgrade ```OpenZeppelin``` Contract Dependency

### Summary

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.8.1 via >=4.8.1

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/package.json#L59

# Non Critical Issues

## 2. Avoid Floating Pragmas: The Version Should Be Locked

### Summary

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L3

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/RecordParser.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/HexUtils.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L2

## 3. Variable Names That Consist Of All Capital Letters Should Be Reserved For Const/immutable Variables

### Summary

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L142
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L143

## 4. Constants in comparisons should appear on the left side

### Summary

Doing so will prevent typo bugs

More about typo bugs: https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L179
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L28
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L64
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L312
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L315
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L42
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L340
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L17
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L39

## 5. Critical Changes Should Use Two-step Procedure

### Summary

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference: https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L80

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L64

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L75

## 6. Declare interfaces on separate files

### Summary

Interfaces should be declared on a separate file and not on the same file where the contract resides in.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L22

## 7. Duplicated require()/revert() Checks Should Be Refactored To A Modifier Or Function

### Summary

Saves deployment costs

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L18

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L305

## 8. Function writing that does not comply with the Solidity Style Guide

### Summary

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

-constructor
-receive function (if exists)
-fallback function (if exists)
-external
-public
-internal
-private
-within a grouping, place the view and pure functions last

### Proof of Concept
Several in-scope contracts

See OffchainDNSResolver.sol for example

## 9. Use delete to Clear Variables

### Summary

delete a assigns the initial value for the type to a. i.e. for integers it is equivalent to a = 0, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If a is a mapping, then delete a[x] will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with delete statements.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L16

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L18

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L12

## 10. Imports can be grouped together

### Summary

Consider importing OZ first, then ENS related files, then all interfaces, then all utils.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L4-L8

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L5-L15

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L4-L12

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L5-L11

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L3-L6

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L3-L5

## 11. ```NatSpec``` return parameters should be included in contracts

### Summary

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

### Proof of Concept

Several in-scope contracts.

See OffchainDNSResolver.sol for example.

###Recommended Mitigation Steps
Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)
```
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

## 12. No need to initialize uints to zero

### Summary

There is no need to initialize uint variables to zero as their default value is 0

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L339
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L59
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L72
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L12
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L263
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L236
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L210
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L263
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L12

## 13. Initial value check is missing in Set Functions

### Summary

A check regarding whether the current value and the new value are the same should be added

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L80

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L64

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L75

## 14. Contracts should have full test coverage

### Summary

The test coverage rate of the project is 97%. Testing all functions is best practice in terms of security criteria. While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

```
- How many contracts are in scope?:   21
- Total SLoC for these contracts?:  2103
...
- What is the overall line coverage percentage provided by your tests?:  90
```

Due to its capacity, test coverage is expected to be 100%.

## 15. Implementation contract may not be initialized

### Summary

OpenZeppelin recommends that the initializer modifier be applied to constructors. Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits. https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L55

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L43

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L52

## 16. NatSpec comments should be increased in contracts

### Summary

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

### Proof of Concept
Several in-scope contracts.

See OffchainDNSResolver.sol for example.

## 17. Non-usage of specific imports

### Summary

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly. https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:

```
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

### Proof of Concept

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L4-L7

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L7-L15

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L4

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L5

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L6

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L8

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L9

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L10

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L11

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L12

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/RecordParser.sol#L4

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L5-L10

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L3

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L3-L5

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L3-L5

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L3-L5

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L3-L4

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L3-L4

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L3-L4

## 18. Use a more recent version of Solidity

### Summary

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

0.8.12: string.concat() instead of abi.encodePacked(,)

0.8.13: Ability to use using for with a list of free functions

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases. Override Checker: Allow changing data location for parameters only when overriding external functions.

0.8.15:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays. Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

0.8.16:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

0.8.17:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

0.8.19: 

SMTChecker: New trusted mode that assumes that any compile-time available code is the actual used code, even in external calls. Bug Fixes:

Assembler: Avoid duplicating subassembly bytecode where possible.
Code Generator: Avoid including references to the deployed label of referenced functions if they are called right away.
ContractLevelChecker: Properly distinguish the case of missing base constructor arguments from having an unimplemented base function.
SMTChecker: Fix internal error caused by unhandled z3 expressions that come from the solver when bitwise operators are used.
SMTChecker: Fix internal error when using the custom NatSpec annotation to abstract free functions.
TypeChecker: Also allow external library functions in using for.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L3

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/RecordParser.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/SHA1.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/digests/SHA256Digest.sol#L1

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/HexUtils.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L2

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L2

## 19. ```NatSpec``` comments should be increased in contracts

### Summary

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters The following events should also add the previous original value in addition to the new value.

### Proof of Concept

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L53

## 20. ```NatSpec``` comments should be increased in contracts

### Summary

An open TODO is present. It is recommended to avoid open TODOs as they may indicate programming errors that still need to be fixed.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L71

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L167

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L291

## 21. Using >/>= without specifying an upper bound is unsafe

### Summary

There will be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/SHA1.sol#L1

## 22. Public Functions Not Called By The Contract Should Be Declared External Instead

### Summary

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L80

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L101

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L64

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L75

## 23. ```require() / revert()``` Statements Should Have Descriptive Reason Strings

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L18

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L196

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L212

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L228

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L244

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L265

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L266

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L305

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L337

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L343

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L345

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L373

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L17

## 24. Use ```bytes.concat()```

### Summary

Solidity version 0.8.4 introduces bytes.concat() (vs abi.encodePacked(<bytes>,<bytes>))

### Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L119

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L151

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L185

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L223

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L12

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L29

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L46

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L39