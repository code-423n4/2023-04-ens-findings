### [QA-01] `require` should be used instead of `assert`

`assert` should be avoided  

[contracts/dnsregistrar/OffchainDNSResolver.sol#L169](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/OffchainDNSResolver.sol#L169)  
```
assert(startIdx + fieldLength < lastIdx);
```
[contracts/dnssec-oracle/RRUtils.sol#L25](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/RRUtils.sol#L25)  
[contracts/dnssec-oracle/RRUtils.sol#L61](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/RRUtils.sol#L61)  

### [QA-02] Lines too long

Keep line width to max 120 characters for better readability where possible.  

[contracts/dnssec-oracle/BytesUtils.sol#L323](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/BytesUtils.sol#L323)  
```
hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1F";
```
[contracts/dnssec-oracle/DNSSECImpl.sol#L81](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/DNSSECImpl.sol#L81)  
[contracts/dnssec-oracle/DNSSECImpl.sol#L100](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/DNSSECImpl.sol#L100)  

### [QA-03] Constants should be all uppercase with words separated by underscores

[contracts/dnssec-oracle/BytesUtils.sol#L322](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/BytesUtils.sol#L322)  
```
bytes constant base32HexTable =
```
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L21)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L23](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L23)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L25](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L25)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L27](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L27)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L29](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L29)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L31](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L31)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L34](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L34)  

### [QA-04] Uppercase variable names should be reserved for constants

[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L142](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L142)  
```
uint256 LHS = mulmod(y, y, p); // y^2
```
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L143](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L143)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L152](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L152)  

### [QA-05] Events associated with setter functions

Consider having events associated with setter functions emit both the new and old values instead of just the new value.

[contracts/dnsregistrar/DNSRegistrar.sol#L82](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L82)
```
emit NewPublicSuffixList(address(suffixes));
```
[contracts/dnssec-oracle/DNSSECImpl.sol#L77](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L77)

### [QA-06] Missing docstring `@param`

Function `readKeyValue` is missing docstring for param `len`.

[contracts/dnsregistrar/RecordParser.sol#L9-L21](https://github.com/ensdomains/ens-contracts/blob/master/contracts/dnsregistrar/RecordParser.sol#L9-L21)  
```
/**
 * @dev Parses a key-value record into a key and value.
 * @param input The input string
 * @param offset The offset to start reading at
 */
function readKeyValue(
    bytes memory input,
    uint256 offset,
    uint256 len
)
    internal
    pure
    returns (bytes memory key, bytes memory value, uint256 nextOffset)
```

### [QA-07] Missing docstring `@return`

[contracts/dnssec-oracle/BytesUtils.sol#L294-L304](https://github.com/ensdomains/ens-contracts/blob/master/contracts/dnssec-oracle/BytesUtils.sol#L294-L304)  
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
```
[contracts/dnsregistrar/RecordParser.sol#L9-L21](https://github.com/ensdomains/ens-contracts/blob/master/contracts/dnsregistrar/RecordParser.sol#L9-L21)  

### [QA-08] This declaration shadows a builtin symbol

[contracts/dnssec-oracle/DNSSECImpl.sol#L143](https://github.com/ensdomains/ens-contracts/blob/master/contracts/dnssec-oracle/DNSSECImpl.sol#L143)  
```
uint256 now
```

### [QA-09] Contracts use different solidity versions

[contracts/utils/HexUtils.sol#L2](https://github.com/ensdomains/ens-contracts/blob/master/contracts/utils/HexUtils.sol#L2)
```
pragma solidity ^0.8.4;
```
[contracts/dnsregistrar/RecordParser.sol#L2](https://github.com/ensdomains/ens-contracts/blob/master/contracts/dnsregistrar/RecordParser.sol#L2)
```
pragma solidity ^0.8.11;
```
[contracts/utils/NameEncoder.sol#L2](https://github.com/ensdomains/ens-contracts/blob/master/contracts/utils/NameEncoder.sol#L2)
```
pragma solidity ^0.8.13;
```

### [QA-10] Unspecific compiler version pragma

Avoid floating pragmas for non-library contracts.  
It is recommended to pin to a concrete compiler version.  

[contracts/dnsregistrar/DNSClaimChecker.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/DNSClaimChecker.sol#L2)  
```
pragma solidity ^0.8.4;
```
[contracts/dnsregistrar/DNSRegistrar.sol#L3](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/DNSRegistrar.sol#L3)  
[contracts/dnsregistrar/OffchainDNSResolver.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/OffchainDNSResolver.sol#L2)  
[contracts/dnsregistrar/RecordParser.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/RecordParser.sol#L2)  
[contracts/dnssec-oracle/BytesUtils.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/BytesUtils.sol#L1)  
[contracts/dnssec-oracle/DNSSECImpl.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/DNSSECImpl.sol#L2)  
[contracts/dnssec-oracle/RRUtils.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/RRUtils.sol#L1)  
[contracts/dnssec-oracle/SHA1.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/SHA1.sol#L1)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1)  
[contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1)  
[contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1)  
[contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1)  
[contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1)  
[contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1)  
[contracts/dnssec-oracle/digests/SHA1Digest.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/digests/SHA1Digest.sol#L1)  
[contracts/dnssec-oracle/digests/SHA256Digest.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/digests/SHA256Digest.sol#L1)  
[contracts/utils/HexUtils.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/utils/HexUtils.sol#L2)  
[contracts/utils/NameEncoder.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/utils/NameEncoder.sol#L2)  

### [QA-11] Use a more recent version of solidity

[contracts/dnsregistrar/DNSClaimChecker.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/DNSClaimChecker.sol#L2)  
```
pragma solidity ^0.8.4;
```
[contracts/dnsregistrar/DNSRegistrar.sol#L3](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/DNSRegistrar.sol#L3)  
[contracts/dnsregistrar/OffchainDNSResolver.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/OffchainDNSResolver.sol#L2)  
[contracts/dnsregistrar/RecordParser.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnsregistrar/RecordParser.sol#L2)  
[contracts/dnssec-oracle/BytesUtils.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/BytesUtils.sol#L1)  
[contracts/dnssec-oracle/DNSSECImpl.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/DNSSECImpl.sol#L2)  
[contracts/dnssec-oracle/RRUtils.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/RRUtils.sol#L1)  
[contracts/dnssec-oracle/SHA1.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/SHA1.sol#L1)  
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L1)  
[contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L1)  
[contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L1)  
[contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L1)  
[contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L1)  
[contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L1)  
[contracts/dnssec-oracle/digests/SHA1Digest.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/digests/SHA1Digest.sol#L1)  
[contracts/dnssec-oracle/digests/SHA256Digest.sol#L1](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/digests/SHA256Digest.sol#L1)  
[contracts/utils/HexUtils.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/utils/HexUtils.sol#L2)  
[contracts/utils/NameEncoder.sol#L2](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/utils/NameEncoder.sol#L2)  
