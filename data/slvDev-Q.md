## Low Findings

|  | Issue | Instances |
| --- | --- | --- |
| [L-01] | ABIEncoderV2 is Deprecated | 1 |
| [L-02] | Vulnerable OpenZeppelin Dependency | 1 |
| [L-03] | Two-Step Ownership Transfer Pattern Offers Enhanced Security | 1 |
| [L-04] | Prefer Using OpenZeppelin's Ownable | 1 |
| [L-05] | Admin Privilege - Single Point of Failure | 1 |
| [L-06] | Low Level Calls with Solidity Version Before 0.8.14 Can Result in Optimiser Bug | 15 |
| [L-07] | Insufficient Validation for Assigning Zero Values to Crucial State Variables | 3 |
| [L-08] | Eliminate Unused Code | 2 |
| [L-09] | Use require() instead of assert() | 3 |
| [L-10] | Prepare for Project Stop and Upgrade Scenarios | 1 |

## Non-critical Findings

|  | Issue | Instances |
| --- | --- | --- |
| [NC-01] | Consolidate System-Wide Constants in a Single File | 2 |
| [NC-02] | Separate Interface Declarations into Individual Files | 1 |
| [NC-03] | Inconsistent Use of Named Return Variables Causes Confusion | 6 |
| [NC-04] | Function Calls in Loop Risk Denial of Service Due to Unchecked Array Length | 1 |
| [NC-05] | Use Descriptive Names for Contracts and Libraries | 3 |
| [NC-06] | Event is missing indexed fields | 3 |
| [NC-07] | Omissions in Events | 3 |
| [NC-08] | Add a timelock to critical functions | 3 |
| [NC-09] | Missing Initial Value Check-in Set Functions | 3 |
| [NC-10] | require()/revert() statements without descriptive reason strings | 14 |
| [NC-11] | Custom Errors without return value properties | 5 |
| [NC-12] | Use delete to clear variables instead of zero assignment | 1 |
| [NC-13] | Public functions not called by the contract should be declared external instead | 5 |
| [NC-14] | Outdated Solidity Version | 18 |
| [NC-15] | Open TODOs | 3 |
| [NC-16] | Long lines are not suitable for the Solidity Style Guide | 3 |
| [NC-17] | Order of functions not following the Style Guide | 1 |
| [NC-18] | Function Naming suggestions | 10 |
| [NC-19] | Duplicated require()/revert() Checks | 2 |
| [NC-20] | Non-specific Imports | 52 |
| [NC-21] | Use bytes.concat() | 7 |
| [NC-22] | Add NatSpec Mapping comment | 3 |
| [NC-23] | Constant/Immutable variables not using all capital letters | 11 |
| [NC-24] | Constants Should Be Defined Rather Than Using Magic Numbers | 4 |
| [NC-25] | Large or Complicated Codebase needs Fuzz Tests | 4 |
| [NC-26] | Floating Pragmas | 17 |
| [NC-27] | Inconsistent Pragma Directives |  |
| [NC-28] | Missing SPDX-License-Identifier | 13 |
| [NC-29] | NatSpec comments should be increased in contracts | 1 |
| [NC-30] | Utilize SMTChecker | 1 |

## [L-01] ABIEncoderV2 is Deprecated

The **`pragma experimental ABIEncoderV2`** directive has been deprecated due to the introduction of a more stable and efficient ABI encoder in Solidity version 0.8.0, known as **`ABI coder v2`**.

```solidity
File: /tree/main/contracts/dnssec-oracle/DNSSECImpl.sol

pragma experimental ABIEncoderV2
```

## [L-02] Vulnerable OpenZeppelin Dependency

The **`package.json`** configuration file indicates that the project is utilizing version **`4.1.0`**
 of OZ, which is not the most recent update. It is necessary to have a minimum dependency version of **`4.8.1`** in order to incorporate essential security vulnerability fixes.

```solidity
File: ./package.json
"@openzeppelin/contracts": "^4.1.0"
```

## [L-03] ****Two-Step Ownership Transfer Pattern Offers Enhanced Security****

The **`DNSSECImpl.sol`** contract inherits from **`Owned.sol`**, which implements a **`setOwner`** function to modify ownership.

Using a two-step transfer ownership pattern provides better safety and prevents accidental ownership changes.

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

import "./Owned.sol";
contract DNSSECImpl is DNSSEC, Owned {
```

```solidity
File: contracts\dnssec-oracle\Owned.sol

function setOwner(address newOwner) public owner_only {
    owner = newOwner;
}
```

## [L-04] ****Prefer Using OpenZeppelin's Ownable****

The **`DNSSECImpl.sol`** contract inherits from a custom **`Owned.sol`** implementation. It is more advisable to utilize a community-tested and reliable solution rather than creating one from scratch.

OpenZeppelin's Ownable: **[https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol)**

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

import "./Owned.sol";
contract DNSSECImpl is DNSSEC, Owned {
```

## [L-05] Admin Privilege - Single Point of Failure

A single point of failure can allow a hacked or malicious owner to use critical functions in the project.
The owner role has a single point of failure, and `onlyOwner` and `owner_only` can use a few critical functions.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
```

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
function setDigest(uint8 id, Digest digest) public owner_only {
```

## [L-06] Low Level Calls with Solidity Version Before 0.8.14 Can Result in Optimiser Bug

The project contracts in scope are using low-level calls with Solidity version before 0.8.14 which can result in optimizer bug.
[https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d](https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d)

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

19: assembly {
            ret := keccak256(add(add(self, 32), offset), len)
        }
73: assembly {
            selfptr := add(self, add(offset, 32))
            otherptr := add(other, add(otheroffset, 32))
        }
80: assembly {
                a := mload(selfptr)
                b := mload(otherptr)
            }
197: assembly {
            ret := and(mload(add(add(self, 2), idx)), 0xFFFF)
        }
213: assembly {
            ret := and(mload(add(add(self, 4), idx)), 0xFFFFFFFF)
        }
229: assembly {
            ret := mload(add(add(self, 32), idx))
        }
245: assembly {
            ret := and(
                mload(add(add(self, 32), idx)),
                0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF000000000000000000000000
            )
        }
267: assembly {
            let mask := not(sub(exp(256, sub(32, len)), 1))
            ret := and(mload(add(add(self, 32), idx)), mask)
        }
276: assembly {
                mstore(dest, mload(src))
            }
286: assembly {
                let srcpart := and(mload(src), not(mask))
                let destpart := and(mload(dest), mask)
                mstore(dest, or(destpart, srcpart))
            }
311: assembly {
            dest := add(ret, 32)
            src := add(add(self, 32), offset)
        }

```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

386: assembly {
                    word := mload(add(add(data, 32), i))
                }

```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

23: assembly {
            success := staticcall(
                gas(),
                5,
                add(input, 32),
                mload(input),
                add(output, 32),
                mload(modulus)
            )
        }

```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/SHA1.sol

7: assembly {
            // Get a safe scratch location
            let scratch := mload(0x40)

            // Get the data length, and point data at the first byte
            let len := mload(data)
            data := add(data, 32)

            // Find the length after padding
            let totallen := add(and(add(len, 1), 0xFFFFFFFFFFFFFFC0), 64)
            switch lt(sub(totallen, len), 9)
            case 1 {
                totallen := add(totallen, 64)
            }

```

```solidity
File: /app/2023-04-ens/contracts/utils/HexUtils.sol

17: assembly {
            // check that the index to read to is not past the end of the string
            if gt(lastIdx, mload(str)) {
                revert(0, 0)
            }

```

## [L-07] ****Insufficient Validation for Assigning Zero Values to Crucial State Variables****

The variables **`_suffixes`**, **`Algorithm`**, and **`Digest`** are important **`address`** variables in the system.

It is essential to verify that they are not assigned to **`address(0)`** to ensure proper functionality.

```solidity
File: contracts\dnsregistrar\DNSRegistrar.sol

function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
```

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
function setDigest(uint8 id, Digest digest) public owner_only {
```

## [L-08] ****Eliminate Unused Code****

The presence of unused code within the main project contract files is unnecessary and may introduce potential security vulnerabilities.
It is advisable to remove such code or incorporate event-emitting functionality to ensure a more secure and streamlined implementation.

```solidity
File: contracts\dnssec-oracle\algorithms\EllipticCurve.sol

function multiplyPowerBase2(uint256 x0,uint256 y0,uint256 exp) internal pure returns (uint256, uint256) {
function multipleGeneratorByScalar(uint256 scalar) internal pure returns (uint256, uint256) {
```

## [L-09] Use `require()` instead of `assert()`

Assert should not be used except for tests, require should be used.

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process’s available gas instead of returning it, as request()/revert() did.

Consider replacing `assert()` with `require()` to avoid consuming the remainder of the transaction's available gas.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

169: assert(startIdx + fieldLength < lastIdx);
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

25: assert(idx < self.length);
61: assert(offset < self.length);
```

## [L-10] Prepare for Project Stop and Upgrade Scenarios

In the early stages of the project, there might be situations where the system requires stopping or upgrading. It is recommended to prepare a script in advance and include it in the project documentation. This is often referred to as the "Emergency Stop (Circuit Breaker) Pattern".

[https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol](https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol)

The project hasn’t Stop and Upgrade scenario

## [NC-01] ****Consolidate System-Wide Constants in a Single File****

Numerous addresses and constants are used throughout the system. It is recommended to gather the most frequently used ones in a single file (e.g., **`constants.sol`**) and utilize inheritance to access these values.

Consolidating constants in this manner will improve code readability and simplify future maintenance. It will also help address potential issues arising from hard-coded values, such as admin addresses.

Create a **`constants.sol`** file and import it into contracts that require access to these values. While this approach is generally beneficial, be mindful that in certain use cases, it may lead to higher gas consumption during deployment.

```solidity
File: contracts\dnsregistrar\DNSClaimChecker.sol

uint16 constant CLASS_INET = 1;
uint16 constant TYPE_TXT = 16;
```

```solidity
File: contracts\dnsregistrar\OffchainDNSResolver.sol

uint16 constant CLASS_INET = 1;
uint16 constant TYPE_TXT = 16;
```

## [NC-02] Separate Interface Declarations into Individual Files

To improve code organization and maintainability, it is recommended to declare interfaces in separate files rather than combining them with other contract code.

```solidity
File: contracts\dnsregistrar\OffchainDNSResolver.sol

22: interface IDNSGateway {
32: contract OffchainDNSResolver is IExtendedResolver {
```

## [NC-03] ****Inconsistent Use of Named Return Variables Causes Confusion****

To enhance code clarity and maintainability, consider adopting a uniform approach to handling return values across the codebase. Remove named return variables, declare them explicitly as local variables, and add the required return statements where needed. This practice will not only improve code readability but also minimize potential regressions during future code refactoring.

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

function verifyRRSet(RRSetWithSignature[] memory input) external view virtual override
    returns (bytes memory rrs, uint32 inception)
{
    return verifyRRSet(input, block.timestamp);
}

function verifyRRSet(RRSetWithSignature[] memory input,uint256 now) public view virtualoverride
    returns (bytes memory rrs, uint32 inception)
{
    ...
    return (proof, inception);
}

function validateSignedSet(RRSetWithSignature memory input,bytes memory proof,uint256 now) internal view 
		returns (RRUtils.SignedSet memory rrset) {
   ...
    return rrset;
}
```

```solidity
File: contracts\dnssec-oracle\RRUtils.sol
function readName(bytes memory self, uint256 offset) internal pure 
	returns (bytes memory ret) {
    uint256 len = nameLength(self, offset);
    return self.substring(offset, len);
}
```

```solidity
File: contracts\dnssec-oracle\algorithms\EllipticCurve.sol

function zeroAffine() internal pure returns (uint256 x, uint256 y) {
    return (0, 0);
}

function multiplyScalar(uint256 x0,uint256 y0,uint256 scalar) internal pure 
		returns (uint256 x1, uint256 y1) {
        ...
        return toAffinePoint(x1, y1, z1);
    }
```

## [NC-04] Function Calls in Loop Risk Denial of Service Due to Unchecked Array Length

Unbounded loop function calls are susceptible to errors, resource exhaustion, and potential contract trapping due to gas limitations or failed transactions. To prevent unnecessary gas consumption and denial of service, consider limiting the loop length, especially if the array is expected to grow or handle a large number of elements. Implementing such a measure will enhance the contract's reliability and efficiency.

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

function verifyRRSet(RRSetWithSignature[] memory input,uint256 now) public view virtual overridereturns (bytes memory rrs, uint32 inception) {
+   require(input.length< maxInputLength, "max length");
    bytes memory proof = anchors;
    for (uint256 i = 0; i < input.length; i++) {
        RRUtils.SignedSet memory rrset = validateSignedSet(
            input[i],
            proof,
            now
        );
        proof = rrset.data;
        inception = rrset.inception;
    }
    return (proof, inception);
}
```

## [NC-05] Use Descriptive Names for Contracts and Libraries

Navigating the codebase can be challenging due to the absence of descriptive naming conventions that indicate which files contain significant logic.

It is recommended to add prefixes to files as follows:

- Interfaces: **`I_`**
- Abstract contracts: **`Abs_`**
- Libraries: **`Lib_`**

Adopting this or a similar convention will improve code readability and maintainability.

```solidity
File: contracts\dnsregistrar\PublicSuffixList.sol

interface PublicSuffixList {
```

```solidity
File: contracts\dnssec-oracle\algorithms\Algorithm.sol

interface Algorithm {
```

```solidity
File: contracts\dnssec-oracle\digests\Digest.sol

interface Digest {
```

## [NC-06] Event is missing indexed fields

Index event fields to make them more quickly accessible to off-chain tools that parse events. However, be aware that each indexed field costs extra gas during emission.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

47: event Claim(
        bytes32 indexed node,
        address indexed owner,
        bytes dnsname,
        uint32 inception
    );
53: event NewPublicSuffixList(address suffixes);
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/SHA1.sol

4: event Debug(bytes32 x);
```

## [NC-07] **Omissions in Events**

In the codebase, events are generally emitted when significant modifications are made to the contracts. However, some events lack crucial parameters.

It is recommended to include both the `new` and `old` values in events, where applicable, for a more comprehensive audit trail and improved transparency.

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
    algorithms[id] = algo;
    emit AlgorithmUpdated(id, address(algo));
}
function setDigest(uint8 id, Digest digest) public owner_only {
    digests[id] = digest;
    emit DigestUpdated(id, address(digest));
}
```

```solidity
File: contracts\dnsregistrar\DNSRegistrar.sol

function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
    suffixes = _suffixes;
    emit NewPublicSuffixList(address(suffixes));
}
```

## [NC-08] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

64: function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
75: function setDigest(uint8 id, Digest digest) public owner_only {
```

## [NC-09] Missing Initial Value Check-in Set Functions

A check regarding whether the current value and the new value are the same should be added.
This helps prevent unnecessary state changes and events in case the new value is the same as the current value.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

64: function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }
75: function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }
```

## [NC-10] require()/revert() statements without descriptive reason strings

Consider adding descriptive reason strings to require() and revert() statements for better error handling and debugging.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

76: require(msg.sender == owner);
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

18: require(offset + len <= self.length);
196: require(idx + 2 <= self.length);
212: require(idx + 4 <= self.length);
228: require(idx + 32 <= self.length);
244: require(idx + 20 <= self.length);
265: require(len <= 32);
266: require(idx + len <= self.length);
305: require(offset + len <= self.length);
337: require(len <= 52);
343: require(char >= 0x30 && char <= 0x7A);
345: require(decoded <= 0x20);
373: revert();
```

```solidity
File: /app/2023-04-ens/contracts/utils/HexUtils.sol

20: revert(0, 0)
```

## [NC-11] Custom Errors without return value properties

Take advantage of custom error's return value property.
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, providing a serious advantage in debugging and examining the revert details of dapps such as Tenderly.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

117: revert PreconditionNotMet();
153: revert StaleProof();
160: revert NoOwnerRecordFound();
202: revert PreconditionNotMet();
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

205: revert InvalidRRSet();
```

## [NC-12] Use delete to clear variables instead of zero assignment

You can use the `delete` keyword instead of setting the variable as zero. Using delete can save gas and improve code readability.

```solidity
File: /app/2023-04-ens/contracts/utils/NameEncoder.sol

18: dnsName[0] = 0;
```

## [NC-13] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public. If a public function is not called internally within the contract, it should be declared as external to save gas.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
90: function proveAndClaim(
        bytes memory name,
        DNSSEC.RRSetWithSignature[] memory input
    ) public override {
101: function proveAndClaimWithResolver(
        bytes memory name,
        DNSSEC.RRSetWithSignature[] memory input,
        address resolver,
        address addr
    ) public override {
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

64: function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
75: function setDigest(uint8 id, Digest digest) public owner_only {
```

## [NC-14] Outdated Solidity Version

The current Solidity version used in the contract is outdated.
Consider using a more recent version for improved features and security.

0.8.4: bytes.concat() instead of abi.encodePacked(,)
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

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

3: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA1Digest.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA256Digest.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

2: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/SHA1.sol

1: pragma solidity >=0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/utils/NameEncoder.sol

2: pragma solidity ^0.8.13;
```

```solidity
File: /app/2023-04-ens/contracts/utils/HexUtils.sol

2: pragma solidity ^0.8.4;
```

## [NC-15] Open TODOs

An open TODO is present in the code. It is recommended to avoid open TODOs
as they may indicate programming errors that still need to be fixed.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

71: // TODO: More robust parsing that handles whitespace and multiple key/value pairs
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

167: // TODO: Concatenate multiple text fields
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

291: // TODO: Check key isn't expired, unless updating key itself
```

## [NC-16] Long lines are not suitable for the `Solidity Style Guide`

It is generally recommended that lines in the source code should not exceed 80-120 characters.
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

```
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

323: hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F";

```

```
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

81: * @dev Takes a chain of signed DNS records, verifies them, and returns the data from the last record set in the chain.
100: * @dev Takes a chain of signed DNS records, verifies them, and returns the data from the last record set in the chain.

```

## [NC-17] Order of functions not following the Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.
But there are contracts in the project that do not comply with this.
Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

43: constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
```

## [NC-18] Function Naming suggestions

Proper use of _ as a function name prefix and a common pattern is to prefix internal and private function names with _. This pattern is correctly applied in the Party contracts, however there are some inconsistencies in the libraries.

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

273: function memcpy(uint256 dest, uint256 src, uint256 len) private pure {
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

150: function done(RRIterator memory iter) internal pure returns (bool) {
158: function next(RRIterator memory iter) internal pure {
187: function name(RRIterator memory iter) internal pure returns (bytes memory) {
353: function computeKeytag(bytes memory data) internal pure returns (uint16) {
358: *     function computeKeytag(bytes memory data) internal pure returns (uint16) {
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

40: function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {
117: function zeroAffine() internal pure returns (uint256 x, uint256 y) {
137: function isOnCurve(uint256 x, uint256 y) internal pure returns (bool) {
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/SHA1.sol

6: function sha1(bytes memory data) internal pure returns (bytes20 ret) {
```

## [NC-19] Duplicated require()/revert() Checks

Duplicated require() or revert() checks should be refactored to a modifier or function.
This helps in maintaining a clean and organized codebase and saves deployment costs.

```
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

18: require(offset + len <= self.length);
305: require(offset + len <= self.length);

```

## [NC-20] Non-specific Imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
[https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files](https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files)

Example:
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

4: import "../dnssec-oracle/DNSSEC.sol";
5: import "../dnssec-oracle/BytesUtils.sol";
6: import "../dnssec-oracle/RRUtils.sol";
7: import "../utils/HexUtils.sol";
8: import "@ensdomains/buffer/contracts/Buffer.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

4: import "../../contracts/resolvers/profiles/IAddrResolver.sol";
5: import "../../contracts/resolvers/profiles/IExtendedResolver.sol";
6: import "../../contracts/resolvers/profiles/IExtendedDNSResolver.sol";
7: import "@openzeppelin/contracts/utils/introspection/ERC165.sol";
8: import "../dnssec-oracle/BytesUtils.sol";
9: import "../dnssec-oracle/DNSSEC.sol";
10: import "../dnssec-oracle/RRUtils.sol";
11: import "../registry/ENSRegistry.sol";
12: import "../utils/HexUtils.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/RecordParser.sol

4: import "../dnssec-oracle/BytesUtils.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

5: import "@openzeppelin/contracts/utils/introspection/IERC165.sol";
6: import "@ensdomains/buffer/contracts/Buffer.sol";
7: import "../dnssec-oracle/BytesUtils.sol";
8: import "../dnssec-oracle/DNSSEC.sol";
9: import "../dnssec-oracle/RRUtils.sol";
10: import "../registry/ENSRegistry.sol";
11: import "../root/Root.sol";
12: import "../resolvers/profiles/AddrResolver.sol";
13: import "./DNSClaimChecker.sol";
14: import "./PublicSuffixList.sol";
15: import "./IDNSRegistrar.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

3: import "./BytesUtils.sol";
4: import "@ensdomains/buffer/contracts/Buffer.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

3: import "./Algorithm.sol";
4: import "../BytesUtils.sol";
5: import "./RSAVerify.sol";
6: import "@ensdomains/solsha1/contracts/SHA1.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

3: import "./Algorithm.sol";
4: import "./EllipticCurve.sol";
5: import "../BytesUtils.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

3: import "./Algorithm.sol";
4: import "../BytesUtils.sol";
5: import "./RSAVerify.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSAVerify.sol

3: import "../BytesUtils.sol";
4: import "./ModexpPrecompile.sol";File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA1Digest.sol

3: import "./Digest.sol";
4: import "../BytesUtils.sol";
5: import "@ensdomains/solsha1/contracts/SHA1.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA256Digest.sol

3: import "./Digest.sol";
4: import "../BytesUtils.sol";
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

5: import "./Owned.sol";
6: import "./BytesUtils.sol";
7: import "./RRUtils.sol";
8: import "./DNSSEC.sol";
9: import "./algorithms/Algorithm.sol";
10: import "./digests/Digest.sol";
11: import "@ensdomains/buffer/contracts/Buffer.sol";
```

## [NC-21] Use bytes.concat()

Solidity version 0.8.4 introduces bytes.concat() for concatenating byte arrays,
which is more efficient than using abi.encodePacked().
It is recommended to use bytes.concat() instead of abi.encodePacked() and upgrade to at least Solidity version 0.8.4 if required.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

223: abi.encodePacked(parentNode, name.keccak(idx, separator - idx))
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

119: bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));
151: bytes32 node = keccak256(abi.encodePacked(parentNode, labelHash));
185: node = keccak256(abi.encodePacked(parentNode, label));
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

12: bytes memory input = abi.encodePacked(
```

```solidity
File: /app/2023-04-ens/contracts/utils/NameEncoder.sol

29: abi.encodePacked(
46: abi.encodePacked(node, bytesName.keccak(0, labelLength))
```

## [NC-22] Add NatSpec Mapping comment

Add NatSpec comments describing mapping keys and values

```solidity
File: contracts\dnsregistrar\DNSRegistrar.sol

32: mapping(bytes32 => uint32) public inceptions;
```

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

45: mapping(uint8 => Algorithm) public algorithms;
46: mapping(uint8 => Digest) public digests;
```

## [NC-23] Constant/Immutable variables not using all capital letters

Variable names for constant/immutable variables should consist of all capital letters.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

26: ENS public immutable ens;
27: DNSSEC public immutable oracle;
29: address public immutable previousRegistrar;
30: address public immutable resolver;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

21: uint256 constant a =
23: uint256 constant b =
25: uint256 constant gx =
27: uint256 constant gy =
29: uint256 constant p =
31: uint256 constant n =
34: uint256 constant lowSmax =
```

## [NC-24] Constants Should Be Defined Rather Than Using Magic Numbers

Magic numbers are hardcoded numerical values used directly in the code, making it harder to read and maintain. Consider using constants instead of magic numbers.

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

228: require(idx + 32 <= self.length);
244: require(idx + 20 <= self.length);
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

44: return ok && SHA1.sha1(data) == result.readBytes20(result.length - 20);
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

43: return ok && sha256(data) == result.readBytes32(result.length - 32);
```

## [NC-25] Large or Complicated Codebase needs Fuzz Tests

Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement fuzzing tests.
Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold.
Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

0: Complex contract with 400 lines of code
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

0: Complex contract with 433 lines of code
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

0: Complex contract with 419 lines of code
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

0: Complex contract with 425 lines of code
```

## [NC-26] Floating Pragmas

Floating pragmas may lead to unintended vulnerabilities due to different compiler versions.
It is recommended to lock the Solidity version in pragma statements.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

2: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

2: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/RecordParser.sol

2: pragma solidity ^0.8.11;
```

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

3: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSAVerify.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA1Digest.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA256Digest.sol

1: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

2: pragma solidity ^0.8.4;
```

```solidity
File: /app/2023-04-ens/contracts/utils/NameEncoder.sol

2: pragma solidity ^0.8.13;
```

```solidity
File: /app/2023-04-ens/contracts/utils/HexUtils.sol

2: pragma solidity ^0.8.4;
```

## [NC-27] Inconsistent Pragma Directives

Inconsistent pragma directives across contract files may lead to potential compatibility issues and unintended behavior. It is essential to maintain a uniform version specification throughout the codebase to ensure seamless integration and prevent issues arising from version mismatches.

```solidity
All Contracts
2: pragma solidity ^0.8.4;

File: /app/2023-04-ens/contracts/utils/NameEncoder.sol
2: pragma solidity ^0.8.13;

File: /app/2023-04-ens/contracts/dnsregistrar/RecordParser.sol
2: pragma solidity ^0.8.11;
```

## [NC-28] Missing SPDX-License-Identifier

Add an SPDX-License-Identifier to the beginning of each source file to indicate its license and improve trust in your smart contract.

```solidity
File: /app/2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol
File: /app/2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/RRUtils.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/algorithms/RSAVerify.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA1Digest.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/digests/SHA256Digest.sol
File: /app/2023-04-ens/contracts/dnssec-oracle/SHA1.sol
```

## [NC-29] NatSpec comments should be increased in contracts

**Context:** All Contracts

**Description:** For Solidity contracts, it is highly recommended to use NatSpec annotations for all public interfaces, which are part of the ABI. As stated in the official Solidity documentation, proper documentation is essential for complex projects such as DeFi to ensure code readability and auditability. **[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)**

**Recommendation:** Enhance NatSpec comments throughout the contracts for improved clarity and understanding.

## [NC-30] Utilize SMTChecker

The pinnacle of smart contract behavior assurance lies in formal mathematical verification. By ensuring that all assertions made are true across all inputs, the reliability of your verification depends on the quality of your assertions.

Employing SMTChecker can provide a higher level of confidence in the correctness and security of your smart contracts.