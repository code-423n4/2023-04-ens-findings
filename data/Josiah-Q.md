## NEW AND OLD VALUES SHOULD BE EMITTED
It is recommended having events associated with setter functions emit both the new and old values instead of just the new value. 

Here are the 3 instances found.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L64-L67

```
    function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
        algorithms[id] = algo;
        emit AlgorithmUpdated(id, address(algo));
    }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L75-L78

```
    function setDigest(uint8 id, Digest digest) public owner_only {
        digests[id] = digest;
        emit DigestUpdated(id, address(digest));
    }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83

```
    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }
```
## EFFICIENT USAGE OF STORAGE
It is recommended moving memory variables outside of a for loop rather than repeatedly re-declaring them on each iteration.

Here is 1 specific instance found pertaining to `uint256 a`, `uint256 b`, `uint256 mask`, and `int256 diff`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L77-L97

```
        for (uint256 idx = 0; idx < shortest; idx += 32) {
            uint256 a;
            uint256 b;
            assembly {
                a := mload(selfptr)
                b := mload(otherptr)
            }
            if (a != b) {
                // Mask out irrelevant bytes and check again
                uint256 mask;
                if (shortest - idx >= 32) {
                    mask = type(uint256).max;
                } else {
                    mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
                }
                int256 diff = int256(a & mask) - int256(b & mask);
                if (diff != 0) return diff;
            }
            selfptr += 32;
            otherptr += 32;
        }
```
## UNINITIALIZED STATE VARIABLES
Essential variables uninitialized at the constructor could readily make function calls dependent on them to behave in unexpected manners in the event they have not been assigned in time by the setters.

Here are the 2 instances found pertaining to `algorithms`, and `digests`.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L52-L78

```
    constructor(bytes memory _anchors) {
        // Insert the 'trust anchors' - the key hashes that start the chain
        // of trust for all other records.
        anchors = _anchors;
    }

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
## STATE VARIABLE VISIBILITY
It is recommended adopting the explicit approach by including `internal` in variable declarations if `public` or `private` isn't the intended visibility rather than expecting readers to understand that missing visibility is defaulted to `internal`.

Here are the 7 instances found.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21-L35

```
    uint256 constant a =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFC;
    uint256 constant b =
        0x5AC635D8AA3A93E7B3EBBD55769886BC651D06B0CC53B0F63BCE3C3E27D2604B;
    uint256 constant gx =
        0x6B17D1F2E12C4247F8BCE6E563A440F277037D812DEB33A0F4A13945D898C296;
    uint256 constant gy =
        0x4FE342E2FE1A7F9B8EE7EB4A7C0F9E162BCE33576B315ECECBB6406837BF51F5;
    uint256 constant p =
        0xFFFFFFFF00000001000000000000000000000000FFFFFFFFFFFFFFFFFFFFFFFF;
    uint256 constant n =
        0xFFFFFFFF00000000FFFFFFFFFFFFFFFFBCE6FAADA7179E84F3B9CAC2FC632551;

    uint256 constant lowSmax =
        0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0;
``` 
## INPUT VALIDATION
It is recommended implementing essential input validation whenever possible.

Here is 1 specific instance pertaining to whether or not the inputted key length is long enough, i.e. 7 or above, catering for the exponent handling of the 6th and the 7th bytes. 

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L13-L44

```
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
            exponentLen = key.readUint16(5);
            exponent = key.substring(7, exponentLen);
            modulus = key.substring(
                exponentLen + 7,
                key.length - exponentLen - 7
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
## RESILIENT CODE WRITING
It is recommended avoiding hard code a function logic so that it would be resilient to changes that might surface in the future.

Here is 1 specific instance pertaining to adding `5` to `name.length` in consideration of adding "_ens." to the front of the name. 

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L19-L44

```
    function getOwnerAddress(
        bytes memory name,
        bytes memory data
    ) internal pure returns (address, bool) {
        // Add "_ens." to the front of the name.
        Buffer.buffer memory buf;
        buf.init(name.length + 5);
        buf.append("\x04_ens");
        buf.append(name);

        for (
            RRUtils.RRIterator memory iter = data.iterateRRs(0);
            !iter.done();
            iter.next()
        ) {
            if (iter.name().compareNames(buf.buf) != 0) continue;
            bool found;
            address addr;
            (addr, found) = parseRR(data, iter.rdataOffset, iter.nextOffset);
            if (found) {
                return (addr, true);
            }
        }

        return (address(0x0), false);
    }
```
## UNCOMMENTED ASSEMBLY BLOCK
Assembly is a low-level language that is harder to parse by readers, consider including extensive documentation regarding the rationale behind its use, clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code less safe and more error-prone. 

## SOLIDITY COMPILER OPTIMIZATIONS COULD BE PROBLEMATIC
```
hardhat.config.js:
  29  module.exports = {
  30:   solidity: {
  31:     compilers: [
  32:       {
  33:         version: "0.8.4",
  34:         settings: {
  35:           optimizer: {
  36:               enabled: true,
  37:               runs: 1000000
  38
            }
```
Description: Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

Recommendation: Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug. Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## UNUTILIZED CODES
It is recommended removing unused imports, functions, modifiers or events to constitute a cleaner set of codebase whenever possible. 

Here are the 2 instances found.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4

```
    event Debug(bytes32 x);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L4

```
import "../dnssec-oracle/DNSSEC.sol";
```
## PRECISION ISSUE
It is recommended scaling the numerator of a division/ratio whenever possible to avoid truncation involving small integers.

Here is 1 instance found pertaining to the ratio of `r1/r2`. 

u = 5
m = 3
u = 5 % 3 = 2
r1 = m = 3
r2 = u = 2
q = r1/r2 = 3/2 = 1.5 = 1

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L40-L60

```
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
                q = r1 / r2;
                (t1, t2, r1, r2) = (t2, t1 - int256(q) * t2, r2, r1 - q * r2);
            }

            if (t1 < 0) return (m - uint256(-t1));

            return uint256(t1);
        }
    }
```
## GLOBAL VARIABLES UTILIZATION
It is recommended using the global variables rather than inputting its equivalent in the function argument.

Here is 1 instance found pertaining to `now` that could have been omitted and replaced by `block.timestamp` in the function logic.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L140-L174

```
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
## SANITY CHECKS
Zero address and zero value checks at the constructor could avoid human errors leading to non-functional calls associated with the mistakes. This is especially so when the essential instants and variables are devoid of their respective setters that could end up having had to redeploy the contract. 

Here is 1 instance found.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L55-L68

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
## POTENTIAL DDOS
The use of `gas()` in the function logic of `modexp()` could possibly lead to DoS attack due to running out of gas. Apparently, a function call utilizing this library method could use up a major part of the gas depriving `staticcall()` of the needed fuel to execute. It is recommended allowing the caller to specify a beyond threshold gas limit for the successful execution of the library method.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L7-L33

```
    function modexp(
        bytes memory base,
        bytes memory exponent,
        bytes memory modulus
    ) internal view returns (bool success, bytes memory output) {
        bytes memory input = abi.encodePacked(
            uint256(base.length),
            uint256(exponent.length),
            uint256(modulus.length),
            base,
            exponent,
            modulus
        );

        output = new bytes(modulus.length);

        assembly {
            success := staticcall(
                gas(),
                5,
                add(input, 32),
                mload(input),
                add(output, 32),
                mload(modulus)
            )
        }
    }
```
## THE USE OF SHA-1
SHA-1 is comparatively a weak hashing algorithm due to issues pertaining to collision attacks. It is recommended adopting a more secure hashing algorithms like SHA-256 or SHA-3.

Here is 1 contract instance found.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/digests/SHA1Digest.sol#L10-L22

```
contract SHA1Digest is Digest {
    using BytesUtils for *;

    function verify(
        bytes calldata data,
        bytes calldata hash
    ) external pure override returns (bool) {
        require(hash.length == 20, "Invalid sha1 hash length");
        bytes32 expected = hash.readBytes20(0);
        bytes20 computed = SHA1.sha1(data);
        return expected == computed;
    }
}
```
## SPDX LICENSE IDENTIFIER
It is recommended including the `SPDX-License-Identifier` in all contracts whenever possible.

Here are some of the contract instances found.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol
  
## USE CONSTANTS OVER MAGIC NUMBERS
Constants should be used rather than magic numbers in the codebase.

Here are some of the instances found.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L395

```
                        0xFF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00) >>
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L398

```
                    0x00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF00FF);
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L402

```
                    0x0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF) +
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L404

```
                    0xFFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000FFFF0000) >>
```