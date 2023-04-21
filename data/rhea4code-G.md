# 1. Use ```calldata``` instead of ```memory``` for function parameters

## Sumary
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memoryindex. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

In some cases, having function arguments in calldata instead of memory is more optimal.

Changing to bytes calldata will decrease the gas. The total savings for this change across all such uses would be quite significant.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L90

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L101

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L133

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L23

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L87

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L87

# 2. ```internal``` functions only called once can be inlined to save gas

## Sumary
If internal functions are called only once, it is better to inline them to save gas. Failing to do this costs about 20 to 40 more gas. Inlining could improve execution time by avoiding the call overhead. It tells the compiler to copy the function's code into the calling function at compile time, which can improve performance, but note that it may increase the compiled code size.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L19

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L46

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L66

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L136

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L162

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L190

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L179

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L192

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L208

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L224

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L240

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L260

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L300

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L332

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L387

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L140

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L181

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L225

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L285

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L254

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L330

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L373

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L415

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L41

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L94

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L111

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L136

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L150

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L158

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L19

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L187

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L200

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L222

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L248

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L259

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L275

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L332

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L353

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L358

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/SHA1.sol#L6

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L65

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L77

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L117

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L137

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L77

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L208

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L247

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L286

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L159

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L302

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L316

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L377

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L386

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol#L7

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L30

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L37

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSAVerify.sol#L14

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/HexUtils.sol#L11

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/HexUtils.sol#L68

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L9

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L12

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L29

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L49

# 3. Use solidity version ```0.8.19``` to gain some gas boost

## Sumary
Upgrade to the latest solidity version 0.8.19 to get additional gas savings. See latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/

So, use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

## Proof of Concept
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

# 3. Use ```uint256(1)/uint256(2)``` instead for true and false boolean states

## Sumary
If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from true to false can cost up to ~20000 gas rather than uint256(2) to uint256(1) that would cost significantly less.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/HexUtils.sol#L16

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/HexUtils.sol#L53

# 4. Non-usage of specific imports

## Sumary
The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly. https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

Use specific imports syntax per solidity docs recommendation.

## Proof of Concept
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

# 4. Usage of ```uints/ints``` smaller than 32 bytes (256 bits) incurs overhead

## Sumary
When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L17

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L44

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L130

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L29

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L340

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L27

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L29

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L30

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L304

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L379

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L82

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L243

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L84

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L125

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L86

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L87

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L242

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L123

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L124

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L216

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L217

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L244

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L22

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L21

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L12


# 5. ```++i/i++``` Should Be ```unchecked{++i}/unchecked{i++}``` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

## Sumary
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L77

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L341

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L393

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L118

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L260

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L380

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L325

# 6. ```<x> += <y>``` Costs More Gas Than ```<x> = <x> + <y>``` For State Variables

## Proof of Concept

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L53

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSClaimChecker.sol#L60

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L77

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L95

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L96

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L275

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L279

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L280

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L359

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L363

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L367

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L371

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L27

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L63

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L67

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L169

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L169

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L173

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L169

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L309

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L384

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L393

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L397

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L429

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L36

# 7. Public Functions To External

## Sumary
Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L80

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L90

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L101

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/DNSRegistrar.sol#L166

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L64

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L75

# 8. ```require()``` Should Be Used Instead Of ```assert()```

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L169

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L25

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L61

# 9. ```require()/revert()``` Strings Longer Than ```32 Bytes``` Cost Extra Gas

## Sumary
When these strings get longer than 32 bytes, they cost more gas.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/algorithms/P256SHA256Algorithm.sol#L33

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L35

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/wrapper/BytesUtils.sol#L53

# 10. Direct assign Instead Of ```If```

## Sumary
Consider using a ternary operator instead of assigning a value to a variable and then changing afterwards.

This saves the gas for the assigning and the re-assigning of the variable.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/BytesUtils.sol#L68

# 11. Use ```Unchecked``` For ```Null Address``` Check

## Sumary
Consider adding the Null Address check inside the unchecked block as this doesnt add any additional checks that might increase the gas cost. 

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L102

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L115-L116

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L187

# 12. Ternary Operator Instead Of ```Else```

## Sumary
Consider using a ternary operator when you are checking for simple if else conditions as it is more gas efficient.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L123-L128

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L150-L158

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L188-L200

# 13. ```Modifier``` Called Once

## Sumary
Consider adding the check directly to the function if the check is required only once in the contract.

There is no need of creating a modifier for just one check.

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78

# 14. Using ```delete``` statement can save gas

## Proof of Concept
https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/BytesUtils.sol#L339

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L59

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L72

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L263

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L236

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L210

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/RRUtils.sol#L263

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L12

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L16

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L18

https://github.com/code-423n4/2023-04-ens/tree/main/contracts/utils/NameEncoder.sol#L12