   ### Low risk issues
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [L&#x2011;01] | Files does not contain an SPDX License Identifier | 12 |
| [L&#x2011;02] | global variable now is deprecated, block.timestamp should be used instead. | 9 |
| [L&#x2011;03] | For immutable variables, Zero address checks are missing in constructor | 6 |
| [L&#x2011;04] | Missing checks for address(0x0) when assigning values to address state variables | 1 |
| [L&#x2011;05] | Consider isContract checks in constructors | 6 |



### Non-Critical issues
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [N&#x2011;01] | For internal functions, follow Solidity standard naming conventions | 74 |
| [N&#x2011;02] | Use named parameters for mapping type declarations | 3 |
| [N&#x2011;03] | Solidity compiler version should be exactly same in all smart contracts | 18 |



### Low risk issues
### [L&#x2011;01]  Files does not contain an SPDX License Identifier
Every contract should start with a comment indicating its license. In few contracts where SPDX licensed is missing. 
There are 12 instances of this issue:

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol
File: contracts/dnssec-oracle/RRUtils.sol
File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol
File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol
File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol
File: contracts/dnssec-oracle/algorithms/RSAVerify.sol
File: contracts/dnssec-oracle/digests/SHA1Digest.sol
File: contracts/dnssec-oracle/digests/SHA256Digest.sol
File: contracts/dnssec-oracle/DNSSECImpl.sol
File: contracts/dnssec-oracle/SHA1.sol
```

As per solidity documentation:

"Trust in smart contract can be better established if their source code is available. Since making source code available always touches on legal problems with regards to copyright, the Solidity compiler encouranges the use of machine-readable SPDX license identifiers. Every source file should start with a comment indicating its license:
// SPDX-License-Identifier: MIT

The compiler does not validate that the license is part of the list allowed by SPDX, but it does include the supplied string in the bytecode metadata.
If you do not want to specify a license or if the source code is not open-source, please use the special value UNLICENSED.
Supplying this comment of course does not free you from other obligations related to licensing like having to mention a specific license header in each source file or the original copyright holder.
The comment is recognized by the compiler anywhere in the file at the file level, but it is recommended to put it at the top of the file. "[Link to solidity reference](https://docs.soliditylang.org/en/v0.6.8/layout-of-source-files.html#spdx-license-identifier)

### Recommended Mitigation steps
Add SPDX-License-Identifier:MIT/Unlicensed(choose license type as per project) to start of contract.

### [L&#x2011;02]  global variable now is deprecated, block.timestamp should be used instead.
### Impact
DNSSECImpl.sol used "now" keyword for the Unix timestamp to validate the records. "now" is deprecated with block.timestamp in Solidity v0.7.0 Breaking Changes. 

As per Solidity v0.7.0 Breaking Changes, 
"The global variable now is deprecated, block.timestamp should be used instead. The single identifier now is too generic for a global variable and could give the impression that it changes during transaction processing, whereas block.timestamp correctly reflects the fact that it is just a property of the block."
[Link to Solidity reference](https://docs.soliditylang.org/en/latest/070-breaking-changes.html#changes-to-the-syntax)

There are 9 instances of this issue.

### Proof of Concept
[Link to affected code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L35-L167)

### Recommended Mitigation steps
Use block.timestamp instead of "now"

### [L&#x2011;03]  For immutable variables, Zero address checks are missing in constructor
Zero address checks should be used in the constructors, to avoid the risk of setting a storage variable as zero address at the time of deployment.
There are 6 instances of this issue:

In DNSRegistrar.sol contract constructor, Below are immutable variables.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

26    ENS public immutable ens;
27    DNSSEC public immutable oracle;
28    PublicSuffixList public suffixes;
29    address public immutable previousRegistrar;
30    address public immutable resolver;
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L26-L30)

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

55    constructor(
56        address _previousRegistrar,
57        address _resolver,
58        DNSSEC _dnssec,
59        PublicSuffixList _suffixes,
60        ENS _ens
61    ) {
62        previousRegistrar = _previousRegistrar;
63        resolver = _resolver;
64        oracle = _dnssec;
65        suffixes = _suffixes;
66        emit NewPublicSuffixList(address(suffixes));
67        ens = _ens;
68    }
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L55-L68)

### Recommended Mitigation steps
Add address(0) check in constructor for immutable variables.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

    constructor(
        address _previousRegistrar,
        address _resolver,
        DNSSEC _dnssec,
        PublicSuffixList _suffixes,
        ENS _ens
    ) {
+       require(_previousRegistrar != address(0), "invalid address");
+       require(_resolver != address(0), "invalid address");
+       require(_dnssec != address(0), "invalid address");
+       require(_ens != address(0), "invalid address");
        previousRegistrar = _previousRegistrar;
        resolver = _resolver;
        oracle = _dnssec;
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
        ens = _ens;
    }
```

In OffchainDNSResolver.sol contract constructor, Below are immutable variables.

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

37    ENS public immutable ens;
38    DNSSEC public immutable oracle;
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L37-L38)

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

43    constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
44        ens = _ens;
45        oracle = _oracle;
46        gatewayURL = _gatewayURL;
47    }
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L43-L47)

### Recommended Mitigation steps
Add address(0) check in constructor for immutable variables.

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

    constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
+       require(_ens != address(0), "invalid address");
+       require(_oracle != address(0), "invalid address");
        ens = _ens;
        oracle = _oracle;
        gatewayURL = _gatewayURL;
    }
```

### [L&#x2011;04]  Missing checks for address(0x0) when assigning values to address state variables
While setting the address state variables, add the address(0) check to prevent address state variables to address(0).
There is 1 instance of this issue. 

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

80    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
81        suffixes = _suffixes;
82        emit NewPublicSuffixList(address(suffixes));
83    }
```

### Recommended Mitigation steps

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
+       require(_suffixes != address(0), "invalid address");
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }
```


### [L&#x2011;05]  Consider isContract checks in constructors
Several addresses are assigned in the contract constructors and assigned to immutable variables. A successful deployment is sensitive to these addresses being assigned correctly for the current network, and that addresses were specified in the correct order. Consider adding checks, as aggressively as possible for the use case, to help ensure the deployment configuration is correct.

There are 6 instances of this issue:

In DNSRegistrar.sol contract constructor, Below are immutable variables.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

26    ENS public immutable ens;
27    DNSSEC public immutable oracle;
28    PublicSuffixList public suffixes;
29    address public immutable previousRegistrar;
30    address public immutable resolver;
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L26-L30)

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

55    constructor(
56        address _previousRegistrar,
57        address _resolver,
58        DNSSEC _dnssec,
59        PublicSuffixList _suffixes,
60        ENS _ens
61    ) {
62        previousRegistrar = _previousRegistrar;
63        resolver = _resolver;
64        oracle = _dnssec;
65        suffixes = _suffixes;
66        emit NewPublicSuffixList(address(suffixes));
67        ens = _ens;
68    }
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L55-L68)

In OffchainDNSResolver.sol contract constructor, Below are immutable variables.

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

37    ENS public immutable ens;
38    DNSSEC public immutable oracle;
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L37-L38)

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

43    constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
44        ens = _ens;
45        oracle = _oracle;
46        gatewayURL = _gatewayURL;
47    }
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L43-L47)

### Recommended Mitigation steps
Consider using .isContract() of Openzeppelin Address library or similar implementation.
[Link to OZ Address.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L40)























### Non-Critical issues
### [N&#x2011;01]  For internal functions, follow Solidity standard naming conventions
Proper use of _ as a function name prefix should be taken care and a common pattern is to prefix internal and private function names with _. This is partially incorporated in contracts. Internal functions does not follow this pattern which leads to confusion while reading code and affects overall readability of the code.

There are 74 instances of this issue in all smart contracts in scope.

### [N&#x2011;02]  Use named parameters for mapping type declarations
Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18.
There are 3 instances of this issue.

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

45    mapping(uint8 => Algorithm) public algorithms;
46    mapping(uint8 => Digest) public digests;
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L45-L46)

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

32    mapping(bytes32 => uint32) public inceptions;
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L32)

### [N&#x2011;03]  Solidity compiler version should be exactly same in all smart contracts
Solidity compiler version should be exactly same in all smart contracts. Different Solidity compiler versions are used, the following contracts have mix versions.

There are 18 instances of this issue:

```solidity
File: contracts/utils/HexUtils.sol
2     pragma solidity ^0.8.4;

File: contracts/utils/NameEncoder.sol#L2
2     pragma solidity ^0.8.13;

File: contracts/dnsregistrar/RecordParser.sol
2     pragma solidity ^0.8.11;

File: contracts/dnsregistrar/DNSClaimChecker.sol
2     pragma solidity ^0.8.4;

File: contracts/dnsregistrar/OffchainDNSResolver.sol
2     pragma solidity ^0.8.4;

File: contracts/dnsregistrar/DNSRegistrar.sol
2     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/BytesUtils.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/RRUtils.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/algorithms/RSAVerify.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/digests/SHA1Digest.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/digests/SHA256Digest.sol
1     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/DNSSECImpl.sol
2     pragma solidity ^0.8.4;

File: contracts/dnssec-oracle/SHA1.sol
1     pragma solidity >=0.8.4;
```

