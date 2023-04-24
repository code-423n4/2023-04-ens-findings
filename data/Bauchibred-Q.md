# **ENS QA report**

## **Table of Contents**

1.  **Low Issues**

            - L-01 Immutable addresses lack zero-address check
            - L-02 NatSpec is incomplete
            - L-03 File is missing NatSpec
            - L-04 Undocumented assembly blocks
            - L-05 Missing checks for address(0x0) when assigning values to address state variables
            - L-06 `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as keccak256()
            - L-07 `now` is deprecated
            - L-08 Missing event emision in dnssec-oracle/Owned.sol
            - L-09 Can't remove old algorithms and digests
            - L-10 Missing sanity (equivalence) checks in setter functions
            - L-11 SPDX license identifiers missing

2.  **Non-Critical Issues**

            - NC-01 Adding a return statement when the function defines a named return variable, is redundant
            - NC-02 Event is missing indexed fields
            - NC-03 Lower case letters should not be used for constants
            - NC-04 uint256 Can be used instead of uint
            - NC-05 If possible, use calldata instead of memory in function parameters
            - NC-06 Fix typos
            - NC-07 Use a more recent version of solidity
            - NC-08 public functions not called by the contract should be declared external instead
            - NC-09 Visibility for constructor is ignored, remove public visibility
            - NC-10 Floating/unlocked pragma
            - NC-11 Finally, best practices of source file layout should be followed

# Low Issues

## L-01 Immutable addresses lack zero-address check

### Proof of Concept

Multiple instances in codes, which include [DNSRegistrar.sol#constructor():]

```
    constructor(
        address _previousRegistrar,
        address _resolver,
        DNSSEC _dnssec,
        PublicSuffixList _suffixes,
        ENS _ens
    ) {
        previousRegistrar = _previousRegistrar;
        resolver = _resolver;
        oracle = _dnssec;
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
        ens = _ens;
    }
```

Constructors should check the address written in an immutable address variable is not the zero address when the contract is being deployed by the team.

### Recommendation

Add a zero address check for the immutable variables/addresses aforementioned.

## L-02 NatSpec is incomplete

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:
[DNSSECImpl.sol#L131-L174:](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L131-L174)

```
     * @dev Validates an RRSet against the already trusted RR provided in `proof`.
     *
     * @param input The signed RR set. This is in the format described in section
     *        5.3.2 of RFC4035: The RRDATA section from the RRSIG without the signature
     *        data, followed by a series of canonicalised RR records that the signature
     *        applies to.
     * @param proof The DNSKEY or DS to validate the signature against.
     * @param now The current timestamp.
     */
    function validateSignedSet(
        RRSetWithSignature memory input,
        bytes memory proof,
        uint256 now
    ) internal view returns (RRUtils.SignedSet memory rrset) {
        rrset = input.rrset.readSignedSet();


        // Do some basic checks on the RRs and extract the name
        bytes memory name = validateRRs(rrset, rrset.typeCovered);
        if (name.labelCount(0) != rrset.labels) {
            revert InvalidLabelCount(name, rrset.labels);
        }
        rrset.name = name;


        // All comparisons involving the Signature Expiration and
        // Inception fields MUST use "serial number arithmetic", as
        // defined in RFC 1982


        // o  The validator's notion of the current time MUST be less than or
        //    equal to the time listed in the RRSIG RR's Expiration field.
        if (!RRUtils.serialNumberGte(rrset.expiration, uint32(now))) {
            revert SignatureExpired(rrset.expiration, uint32(now));
        }


        // o  The validator's notion of the current time MUST be greater than or
        //    equal to the time listed in the RRSIG RR's Inception field.
        if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {
            revert SignatureNotValidYet(rrset.inception, uint32(now));
        }


        // Validate the signature
        verifySignature(name, rrset, input, proof);


        return rrset;
    }
```

### Recommendation

Complete all Natspecs on the case above, provide an explanation regarding the missing `@return`

## L-03 File is missing NatSpec

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:
[BytesUtils.sol;](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L1)

```
pragma solidity ^0.8.4;

library BytesUtils {
    error OffsetOutOfBoundsError(uint256 offset, uint256 length);
***
}
```

### Recommendation

Include Natspec in all files

## L-04 Undocumented assembly blocks

### Proof of Concept

Though functionality is documented for most functions, througout codes, both in/out of scope, assembly was used, since we all know the assembly is a low-level language that is harder to parse by readers. Consider including extensive inline documentation clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code less safe and more error-prone. Hence, consider implementing thorough tests to cover all potential use cases of the createClone function to ensure it behaves as expected.

### Recommendation

Consider including extensive inline documentation clearly explaining what every single assembly instruction does.

## L-05 Missing checks for address(0x0) when assigning values to address state variables

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:
[SHA1.setOwner():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/Owned.sol#L18-L20)

```
    function setOwner(address newOwner) public owner_only {
        owner = newOwner;
    }

```

### Recommendation

Include zero address checks

## L-06 `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as keccak256()

### Proof of Concept

Multiple instances within code in/out of scope, below are a few instances:
[DNSRegistrar.sol#L119:](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L119)
`             bytes32 node = keccak256(abi.encodePacked(rootNode, labelHash));`
[DNSRegistrar.sol#L151:](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L151)
`        bytes32 node = keccak256(abi.encodePacked(parentNode, labelHash));`
[DNSRegistrar.sol#L185:](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L185)
`        node = keccak256(abi.encodePacked(parentNode, label));`
Use `abi.encode()` instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. `abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456`), but `abi.encode(0x123,0x456) => 0x0...1230...456)`. If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or`bytes32()`instead.

### Recommendation

Use abi.encode() instead

## L-07 `now` is deprecated

This may not cause problems now, but if a bug is discovered in this release, and you're forced to change to another version, now may be unavailable, causing unnecessary delays in porting and testing the new contracts.

### Proof of Concept

[DNSSECImpl.sol#L94:](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L94)

```
RRUtils.SignedSet memory rrset = validateSignedSet(input[i], proof, now);
```

[DNSSECImpl.sol#L160-L168:](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L160-L168)

```
        if (!RRUtils.serialNumberGte(rrset.expiration, uint32(now))) { //@audit `now` is deprecated
            revert SignatureExpired(rrset.expiration, uint32(now)); //@audit `now` is deprecated
        }


        // o  The validator's notion of the current time MUST be greater than or
        //    equal to the time listed in the RRSIG RR's Inception field.
        if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {//@audit `now` is deprecated
            revert SignatureNotValidYet(rrset.inception, uint32(now));//@audit `now` is deprecated
        }
```

This may not cause problems now, but if a bug is discovered in this release, and you're forced to change to another version, now may be unavailable, causing unnecessary delays in porting and testing the new contracts.

### Recommendation

Use block.timestamp instead.

## L-08 Missing event emision in dnssec-oracle/Owned.sol

### Proof of Concept

[DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L22)
[setOwner():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/Owned.sol#L18-L20)

```
    function setOwner(address newOwner) public owner_only {
        owner = newOwner;
    }
```

### Recommendation

Every time the contract changes the owner should emit an event to facilitate off-chain monitoring, like in OpenZeppelin Ownable contract

## L-09 Can't remove old algorithms and digests

### Proof of Concept

There are presently no provisions to remove old algorithms added by calls to `setAlgorithm()` and `setDigest()`.

[setAlgorithm():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L64-L67)

```
    function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }
```

[setDigest():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L75-L78)

```
    function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }
```

### Recommendation

Add the option to remove items in algorithms and digests.

## L-10 Missing sanity (equivalence) checks in setter functions

### Proof of Concept

Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.
[setAlgorithm():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L64-L67)

```
    function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }
```

[setDigest():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L75-L78)

```
    function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }
```

### Recommendation

This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.

## L-11 SPDX license identifiers missing

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:
[SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/SHA1.sol)

```
// @audit no SPDX?
pragma solidity >=0.8.4;
```

### Recommendation

Include SPDX license identifiers

# Non-critical Issues

## NC-01 Adding a return statement when the function defines a named return variable, is redundant

### Proof of Concept

[substring():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L294-L318)

```
      /*
     * @dev Copies a substring into a new byte string.
     * @param self The byte string to copy from.
     * @param offset The offset to start copying at.
     * @param len The number of bytes to copy.
     */
    function substring(
        bytes memory self,
        uint256 offset,
        uint256 len
    ) internal pure returns (bytes memory) {
        require(offset + len <= self.length);


        bytes memory ret = new bytes(len);
        uint256 dest;
        uint256 src;


        assembly {
            dest := add(ret, 32)
            src := add(add(self, 32), offset)
        }
        memcpy(dest, src, len);


        return ret;// @audit named variable already defined
    }
```

### Recommendation

Remove this

## NC-02 Event is missing indexed fields

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:
[SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/SHA1.sol#L4)

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (threefields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question

### Recommendation

## NC-03 Lower case letters should not be used for constants

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:

[BytesUtils.sol:]https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L322)
`    bytes constant base32HexTable =  hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F";`

### Recommendation

As a best practice, only capital letters should be used for constants.

## NC-04 uint256 can be used instead of uint

### Proof of Concept

Multiple instances within code in/out of scope

### Recommendation

For explicitness and consistency, uint256 can be used instead uint, this also eases readability of code

## NC-05 If possible, use calldata instead of memory in function parameters

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:
[DNSSECImpl.verifyRRSet():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L87-L89)

```
    function verifyRRSet(
        RRSetWithSignature[] memory input
    )
```

### Recommendation

Change memory to calldata

## NC-06 Fix typos

### Proof of Concept

1. [BytesUtils.sol#L141](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L141)

L141: `* @dev Compares a range of 'self' to all of 'other' and returns True iff`

2. [RRUtils.sol#L146](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L146)

L146: `* @dev Returns true iff there are more RRs to iterate.`

3. [RRUtils.sol#L148](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L148)

L148: ` * @return True iff the iterator has finished.`

### Recommendation

Change from:

`* @dev Compares a range of 'self' to all of 'other' and returns True iff`

to

`* @dev Compares a range of 'self' to all of 'other' and returns True `IF``

Change from:

`* @dev Returns true iff there are more RRs to iterate.`

to

`* @dev Returns true `IF` there are more RRs to iterate.`

Change from:

`* @return True`IF` the iterator has finished.`

to

`* @return True`IF` the iterator has finished.`

## NC-07 Use a more recent version of solidity

### Proof of Concept

Multiple instances within codes both in/out of scope

### Recommendation

Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)

## NC-08 public functions not called by the contract should be declared external instead

### Proof of Concept

Multiple instances within code in/out of scope, below are a few instances:

[DNSSECImpl.setAlgorithm():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L58-L67)

```
    /**
     * @dev Sets the contract address for a signature verification algorithm.
     *      Callable only by the owner.
     * @param id The algorithm ID
     * @param algo The address of the algorithm contract.
     */
    function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }
```

[DNSSECImpl.setDigest():](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L69-L78)

```
    /**
     * @dev Sets the contract address for a digest verification algorithm.
     *      Callable only by the owner.
     * @param id The digest ID
     * @param digest The address of the digest contract.
     */
    function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }
```

### Recommendation

Change this and all other affected areas

## NC-09 Visibility for constructor is ignored, remove public visibility

### Proof of Concept

[Owned.sol#constructor](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/Owned.sol#L14-L16)

```
    constructor() public {
        owner = msg.sender;
    }
```

### Recommendation

Remove the constructor's visibility

## NC-10 Floating/unlocked pragma

### Proof of Concept

Multiple instances within code in/out of scope, below is one instance:

[RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L1)
`pragma solidity ^0.8.4;`

### Recommendation

Lock the pragma

## NC-11 Finally, best practices of source file layout should be followed

### Proof of Concept

Multiple instances within code in/out of scope

### Recommendation

I rcommend following best practices of solidity source file layout for readability.
Reference: https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout

This best practices is to layout a contract elements in following order:
Pragma statements, Import statements, Interfaces, Libraries, Contracts

Inside each contract, library or interface, use the following order:
Type declarations, State variables, Events, Modifiers, Functions
