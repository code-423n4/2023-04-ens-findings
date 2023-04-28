# QA Report (low/non-critical)

## Use of floating compiler version pragma

### Description 

All in-scope contracts float their Solidity compiler versions (e.g. `pragma solidity ^0.8.4`).

Locking the compiler version prevents accidentally deploying the contracts with a different version than what was used for testing. The current pragma prevents contracts from being deployed with an outdated compiler version, but still allows contracts to be deployed with newer compiler versions that may have higher risks of undiscovered bugs.

It is best practice to deploy contracts with the same compiler version that is used during testing and development.

### Recommendation

Consider locking the compiler pragma to the specific version of the Solidity compiler used during testing and development.

## Missing contract address checks

### Description

The following functions call external contracts without checking that code exists at the addresses used to direct those calls:

- [`dnsresolver`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L95-L129)
- [`resolver`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L196-L200)

### Recommendation

Consider adding a check using the `extcodesize` instruction to ensure there is code present at a target address before calling a function at that address.

## Naming discrepancies between implementations and interfaces

### Description

The following functions use parameter names that do not match the names defined in the interfaces they implement:

- [`verify`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L17): `sig` parameter name should be [`signature`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/Algorithm.sol#L17)
- [`verify`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L16): `sig` parameter name should be [`signature`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/Algorithm.sol#L17)

Discrepancies between an interface and an implementation can make working with the implementation more difficult for developers.

### Recommendation

Consider renaming both `sig` parameters to `signature` to match the definition of the interface.

## State variables shadowed in functions

### Description

The following functions shadow at least one state variable:

- [`proveAndClaimWithResolver`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L101) shadows:
    - [`resolver`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L30) with [`resolver`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L104)

Name collisions and variable shadowing can lead to confusion when reading or writing code.

### Recommendation

Consider renaming all the function variables that shadow state variables or prefixing them with an underscore to avoid shadowing.

## `revert` used for off-chain communication

### Description

The [`resolve`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L56) function in `OffchainDNSResolver` uses `revert` to send an `OffchainLookup` request. Use of `revert` limits the function's usefulness when called from other contracts.

### Recommendation

Consider emitting an event instead of reverting to communicate with off-chain components.

## TODOs in code

### Description

The following lines contain "TODO" comments:

- `DNSClaimChecker.sol`, [line 71](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L71)
- `OffchainDNSResolver.sol`, [line 167](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L167)
- `DNSSECImpl.sol`, [line 291](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L291)

If these TODOs are overlooked, there is a risk that deployed code will not match the design specification of the protocol.

### Recommendation

Consider implementing TODOs or documenting the reasons for not doing so.

## Duplicated resource record parsing code

### Description

The [DNSClaimChercker](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol) and [OffchainDNSResolver](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol) contracts both contain `parseRR` functions. While the code in these contracts is not identical, parsers that behave differently can lead to security flaws, or situations where invalid inputs are considered valid in one part of the system versus another.

### Recommendation

Consider moving resource record parsing functions into their own library.

## Unused constant definition

### Description

The [lowSmax](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L34) constant in `EllipticCurve.sol` is defined but unused.

### Recommendation

Consider removing unused constants.

## Elliptic curve signature malleability

### Description

The [`validateSignature`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L391) function in `EllipticCurve.sol` contains the comment "To disambiguate between public key solutions, include comment below," referencing the comment on [line 393](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L393).

Without the check for the low-S value, the elliptic curve signature is malleable by third parties. This can lead to vulnerabilities in some usecases.

### Recommendation

Consider including the check for low-S signature values.

## Incorrect documentation

### Description

The following functions have incorrect documentation:

- [`readBytes20`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L240)
- [`done`](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L150)

Incorrect documentation can lead to misuse of functions by developers.

### Recommendation

Consider updating the docstring for `readBytes20` to read:

    /*
     * @dev Returns the 20 byte value at the specified index of self.
     * @param self The byte string.
     * @param idx The index into the bytes
     * @return The specified 32 bytes of the string.
     */

Consider updating the docstring for `done` to read:

    /**
     * @dev Returns true iff there are no more RRs to iterate.
     * @param iter The iterator to check.
     * @return True iff the iterator has finished.
     */