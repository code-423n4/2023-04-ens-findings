QA Report



### [N-01] Inconsistent Solidity Versions: 

### Description
Different Solidity compiler versions are used, the following contracts use a mix of different Versions

```json
contracts\dnsregistrar\DNSClaimChecker.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\dnsregistrar\DNSRegistrar.sol:
  2  
  3: pragma solidity ^0.8.4;
  4  

contracts\dnsregistrar\IDNSRegistrar.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\dnsregistrar\OffchainDNSResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\dnsregistrar\PublicSuffixList.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnsregistrar\RecordParser.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.11;
  3  

contracts\dnsregistrar\SimplePublicSuffixList.sol:
  1: pragma solidity ^0.8.4;
  2  pragma experimental ABIEncoderV2;

contracts\dnsregistrar\TLDPublicSuffixList.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnsregistrar\mocks\DummyDnsRegistrarDNSSEC.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnsregistrar\mocks\DummyExtendedDNSSECResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\dnsregistrar\mocks\DummyLegacyTextResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\dnssec-oracle\BytesUtils.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\DNSSEC.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  pragma experimental ABIEncoderV2;

contracts\dnssec-oracle\DNSSECImpl.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  pragma experimental ABIEncoderV2;

contracts\dnssec-oracle\Owned.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\RRUtils.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\SHA1.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\dnssec-oracle\algorithms\Algorithm.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\algorithms\DummyAlgorithm.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\algorithms\EllipticCurve.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\algorithms\ModexpPrecompile.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\algorithms\P256SHA256Algorithm.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\algorithms\RSASHA1Algorithm.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\algorithms\RSASHA256Algorithm.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\algorithms\RSAVerify.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\digests\Digest.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\digests\DummyDigest.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\digests\SHA1Digest.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\dnssec-oracle\digests\SHA256Digest.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\ethregistrar\mocks\DummyProxyRegistry.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\registry\ENS.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\registry\ENSRegistry.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\registry\ENSRegistryWithFallback.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\registry\FIFSRegistrar.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\registry\TestRegistrar.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\resolvers\IMulticallable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\resolvers\Multicallable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\resolvers\OwnedResolver.sol:
  1: pragma solidity >=0.8.4;
  2  import "@openzeppelin/contracts/access/Ownable.sol";

contracts\resolvers\PublicResolver.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.17 <0.9.0;
  3  

contracts\resolvers\Resolver.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\ResolverBase.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\mocks\DummyNameWrapper.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\resolvers\profiles\ABIResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\AddrResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\ContentHashResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\DNSResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\ExtendedResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\resolvers\profiles\IABIResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IAddressResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IAddrResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IContentHashResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IDNSRecordResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IDNSZoneResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IExtendedDNSResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\resolvers\profiles\IExtendedResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\resolvers\profiles\IInterfaceResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\INameResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\InterfaceResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IPubkeyResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\ITextResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\IVersionableResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\NameResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\PubkeyResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\resolvers\profiles\TextResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.4;
  3  

contracts\reverseRegistrar\IReverseRegistrar.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\reverseRegistrar\ReverseClaimer.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.17 <0.9.0;
  3  

contracts\reverseRegistrar\ReverseRegistrar.sol:
  1: pragma solidity >=0.8.4;
  2  

contracts\root\Controllable.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\root\Ownable.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\root\Root.sol:
  1: pragma solidity ^0.8.4;
  2  

contracts\utils\DummyOldResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.4.11;
  3  

contracts\utils\ERC20Recoverable.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.17 <0.9.0;
  3  

contracts\utils\HexUtils.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

contracts\utils\LowLevelCallUtils.sol:
  2  
  3: pragma solidity ^0.8.13;
  4  

contracts\utils\NameEncoder.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.13;
  3  

contracts\utils\TestHexUtils.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity ~0.8.17;
  3  

contracts\utils\TestNameEncoder.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.13;
  3  

contracts\utils\UniversalResolver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity >=0.8.17 <0.9.0;
  3  

contracts\wrapper\BytesUtils.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity ~0.8.17;
  3  

contracts\wrapper\IMetadataService.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity ~0.8.17;
  3  

contracts\wrapper\INameWrapper.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity ~0.8.17;
  3  

contracts\wrapper\INameWrapperUpgrade.sol:
  1  //SPDX-License-Identifier: MIT
  2: pragma solidity ~0.8.17;
  3  
```



### [N-02] using BytesUtils for bytes; and using HexUtils for bytes; can be clubbed together in a single line

### Description

using keyword can be used for a list of functions in a single line

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L11
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L34

```solidity
    using BytesUtils for bytes;
    using HexUtils for bytes;
```
The above lines can be updated to this 
```solidity
    using {BytesUtils, HexUtils} for bytes;
```


### [N-03] unused constant variables in contracts/dnsregistrar/DNSClaimChecker.sol

### Description
```solidity
    uint16 constant CLASS_INET = 1;
    uint16 constant TYPE_TXT = 16;
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16

The above 2 constants are declared but were never used in the OffchainDNSResolver contract. Please to remove those constants if they are not intented to be used anywhere in the contract



### [N-04] unused imports 

### Description

ENSRegistry is imported but never used in few contracts
```solidity
import "../registry/ENSRegistry.sol";
```

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L11
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L10


BytesUtils is imported in contracts/dnssec-oracle/algorithms/RSAVerify.sol but never used 
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L3

