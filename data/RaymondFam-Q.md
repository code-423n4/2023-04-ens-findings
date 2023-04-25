## Hardcoded `_ens.` prefix string
The [`_ens.` prefix](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L25-L26) is used as a convention in the ENS system to distinguish ENS names from regular DNS names. It is a well-established convention and is unlikely to change.

In the line `buf.init(name.length + 5)`, 5 is added to name.length because the `_ens. prefix` is added to the front of the name. This prefix is added to indicate that the domain name is part of the ENS system. `_ens.` is a fixed string that is always added, so its length is known (5 characters), hence the need to add 5 to the length of the original name.

That being said, it is always a good practice to write code that is resilient to changes. In the unlikely event that the prefix is changed in the future, the code may not be updateable accordingly since a non-upgradeable contract is entailed here. It is important to stay up to date with any changes or updates to the libraries and protocols used in your code and make necessary changes to ensure it continues to function correctly.

## Input validation checks
Consider performing adequate input validation in various functions throughout the codebase where possible. 

For instance, [`OffchainDNSResolver.resolve()`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L49-L63) could validate input parameters on `name` and `data`, checking for edge cases like empty byte arrays that might lead to unexpected behavior.

## Interface nomenclature
By convention, an interface should have its name prefixed with `i` so users could more easily be imbued with the expected behavior of calls associated with it.

Here is one of the several instance entailed:

[File: DNSRegistrar.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol)
 
```solidity
14: import "./PublicSuffixList.sol";

28:    PublicSuffixList public suffixes;

168:        if (!suffixes.isPublicSuffix(domain)) {
```
[File: PublicSuffixList.sol#L3-L5](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/PublicSuffixList.sol#L3-L5)

```solidity
interface PublicSuffixList {
    function isPublicSuffix(bytes calldata name) external view returns (bool);
}
```
## Events associated with setter functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value.

Here are some of the instances entailed:

[File: DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol)

```solidity
66:        emit AlgorithmUpdated(id, address(algo));

77:        emit DigestUpdated(id, address(digest));
```
[File: DNSRegistrar.sol#L82](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82)

```solidity
        emit NewPublicSuffixList(address(suffixes));
```
## Use of block.timestamp on the current timestamp
Consider making use of the global variable, `block.timestamp`, instead of inputting as a `now` argument in `validateSignedSet()`:

[File: DNSSECImpl.sol#L140-L174](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L140-L174)

```diff
- 143:        uint256 now

- 160:        if (!RRUtils.serialNumberGte(rrset.expiration, uint32(now))) {
- 161:            revert SignatureExpired(rrset.expiration, uint32(now));
+ 160:        if (!RRUtils.serialNumberGte(rrset.expiration, uint32(block.timestamp))) {
+ 161:            revert SignatureExpired(rrset.expiration, uint32(block.timestamp));

- 166:        if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {
- 167:            revert SignatureNotValidYet(rrset.inception, uint32(now));
+ 166:        if (!RRUtils.serialNumberGte(uint32(block.timestamp), rrset.inception)) {
+ 167:            revert SignatureNotValidYet(rrset.inception, uint32(block.timestamp));
```
## Sanity zero address check 
Zero address and zero value checks should be implemented at all times where possible. For instance, [`setPublicSuffixList()`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83) has `_suffixes` set without checking if the instant address associated is a valid contract address. It is a good practice to validate the address before assigning it to avoid potential issues.

## Constants should be defined rather than using magic numbers
Consider defining/assigning magic numbers to constants, serving to improve code readability and maintainability just as it has been done so in [EllipticCurve.sol#L21-L35](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L35).

Here are some of the instances pertaining to the addresses entailed:

[File: RRUtils.sol#L393-L429](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L393-L429)

```solidity
                ac1 +=
                    (word &
                        0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>
                    8;
                ac2 += (word &
                    0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);
            }
            ac1 =
                (ac1 &
                    0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +
                ((ac1 &
                    0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>
                    16);
            ac2 =
                (ac2 &
                    0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +
                ((ac2 &
                    0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>
                    16);
            ac1 = (ac1 << 8) + ac2;
            ac1 =
                (ac1 &
                    0x00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF) +
                ((ac1 &
                    0xFFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000FFFFFFFF00000000) >>
                    32);
            ac1 =
                (ac1 &
                    0x0000000000000000FFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF) +
                ((ac1 &
                    0xFFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFFFF0000000000000000) >>
                    64);
            ac1 =
                (ac1 &
                    0x00000000000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF) +
                (ac1 >> 128);
            ac1 += (ac1 >> 16) & 0xFFFF;
```
## Missing SPDX-License
Consider inserting [`//SPDX-License-Identifier: MIT`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L1) at the top of all contracts/libraries just like it has been done so in DNSRegistrar.sol.

Here are the contract instances with missing SPDX-License:

[File: BytesUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol)
[File: RRUtils.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol)
[File: RSASHA1Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol)
[File: EllipticCurve.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol)
[File: P256SHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol)
[File: ModexpPrecompile.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol)
[File: RSASHA256Algorithm.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol)
[File: RSAVerify.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol)
[File: SHA1Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol)
[File: SHA256Digest.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA256Digest.sol)
[File: SHA1.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol)

## Devoid of system documentation and complete technical specification
A system’s design specification and supporting documentation should be almost as important as the system’s implementation itself. Users rely on high-level documentation to understand the big picture of how a system works. Without spending time and effort to create palatable documentation, a user’s only resource is the code itself, something the vast majority of users cannot understand. Security assessments depend on a complete technical specification to understand the specifics of how a system works. When a behavior is not specified (or is specified incorrectly), security assessments must base their knowledge in assumptions, leading to less effective review. Maintaining and updating code relies on supporting documentation to know why the system is implemented in a specific way. If code maintainers cannot reference documentation, they must rely on memory or assistance to make high-quality changes. Currently, the only documentation available is a single README file, as well as code comments.

## Constant visibility
Although missing visibility is defaulted to `internal`, it is a good practice to specify visibility (public, internal or private) for constant variables like [`a`, `b`, `gx`, `gy`, `p`, `n`, and `lowSmax`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L35) in EllipticCurve.sol. This ensures that developers understand the intended visibility and usage of these variables.

## Unscaled division
Consider scaling all arithmetic divisions/ratios failure of which could lead to precision issue.

Here is a specific instance entailed pertaining to line 52 of `inverseMod()`:

[File: EllipticCurve.sol#L40-L60](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L40-L60)

```solidity
    function inverseMod(uint256 u, uint256 m) internal pure returns (uint256) {
        unchecked {
            if (u == 0 || u == m || m == 0) return 0;
            if (u > m) u = u % m;

            int256 t1;
            int256 t2 = 1;
            uint256 r1 = m;
            uint256 r2 = u;
            uint256 q;

            while (r2 != 0) {
52:                q = r1 / r2;
                (t1, t2, r1, r2) = (t2, t1 - int256(q) * t2, r2, r1 - q * r2);
            }

            if (t1 < 0) return (m - uint256(-t1));

            return uint256(t1);
        }
    }
```
## Gas usage handling
[`ModexpPrecompile.modexp()`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L7-L33) uses `gas()` to forward all the remaining gas to `staticcall()` in its assembly logic. While this is generally safe, it could lead to a situation where the contract consumes all available gas if `staticcall()` fails. It might be useful to specify a reasonable gas limit for the staticcall operation, depending on the use case of the contract.

## SHA-1 versus SHA-256 or SHA-3 
SHA-1 adopted in [`SHA1Digest.verify()`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L19) is considered a weak hashing algorithm due to known vulnerabilities like collision attacks. It is generally recommended to use more secure hashing algorithms like SHA-256 or SHA-3. 

Currently, this does not pose any critical issue to the protocol with the implemented `DNSSEC` that specifically supports the SHA-1 algorithm. However, elsewhere this might be concern for consideration. 

## Unsupported NSEC, NSEC3, and wildcards 
As indicated in the [NatSpec of DNSSECImpl.sol](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L16-L17), the contract does not support NSEC, NSEC3, and wildcard records, which might lead to incomplete or incorrect DNSSEC validation. 

Consider implementing the missing features, where possible, to ensure proper validation and support for all record types.

## Inadequately Commented Assembly Block
Assembly block is used in numerous contracts throughout the codebase. While this does not pose a security risk per se, it is at the same time a complicated and critical part of the system. Moreover, as this is a low-level language that is harder to parse by readers, consider including extensive documentation regarding the rationale behind its use, clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code less safe and more error-prone.

## `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. `abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456`), but `abi.encode(0x123,0x456) => 0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()`, it can often be cast to `bytes()` or `bytes32()` instead. If all arguments are strings and or bytes, `bytes.concat()` should be used instead.

For instance, the specific instance below may be refactored as follows:

[File: NameEncoder.sol#L24-L47](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/NameEncoder.sol#L24-L47)

```diff
        unchecked {
            for (uint256 i = length - 1; i >= 0; i--) {
                if (bytesName[i] == ".") {
                    dnsName[i + 1] = bytes1(labelLength);
                    node = keccak256(
-                        abi.encodePacked(
+                        abi.encode(
                            node,
                            bytesName.keccak(i + 1, labelLength)
                        )
                    );
                    labelLength = 0;
                } else {
                    labelLength += 1;
                    dnsName[i + 1] = bytesName[i];
                }
                if (i == 0) {
                    break;
                }
            }
        }

        node = keccak256(
-            abi.encodePacked(node, bytesName.keccak(0, labelLength))
+            abi.encode(node, bytesName.keccak(0, labelLength))
        );
```
## YUL inline assembly error messages 
`revert(0, 0)` in the assembly block of [`HexUtils.hexStringToBytes32()`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L20) provides no information about the reason for the failure. 

Consider implementing error messages or custom error types to provide more information about the cause of the revert.