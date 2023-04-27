
# GAS OPTIMIZATION

##

## [G-1] State variable value only set in the constructor can be declared as a constants to save large volume of the gas 

> Instances (1) 

> Approximate Gas Saved : (4000 gas) (As per my remix test 90000 gas)

gatewayURL string variable value is only set inside the constructor. The value is not changed anywhere inside the contract. This gatewayURL can be declared as constant to save large volume of gas 

As per [Remix gas test](https://gist.github.com/sathishpic22/0079888aca5a6fe626b74f0063546138) approximately possible to saves 90000 gas

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

39:  string public gatewayURL;

43: constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
        ens = _ens;
        oracle = _oracle;
        gatewayURL = _gatewayURL;
    }

```
[OffchainDNSResolver.sol#L39-L47](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L39-L47)

##

## [G-2] Using storage instead of memory for structs/arrays saves gas

> Instances(3)

> Approximate gas saved: 6300 gas 

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

53: string[] memory urls = new string[](1);


```
[OffchainDNSResolver.sol#L53](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L53)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

307: bytes memory ret = new bytes(len);

```
[BytesUtils.sol#L307](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L307)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

408: uint256[3] memory P = addAndReturnProjectivePoint(x1, y1, x2, y2);
```
[EllipticCurve.sol#L408](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L408)

##

## [G-3] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

> Instances(1)

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key’s keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

45: mapping(uint8 => Algorithm) public algorithms;
46: mapping(uint8 => Digest) public digests;

```
[DNSSECImpl.sol#L45-L46](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L45-L46)

##

## [G-4] For events use 3 indexed rule to save gas

> Instances(2)

Need to declare 3 indexed fields for event parameters. If the event parameter is less than 3 should declare all event parameters indexed

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

47: event Claim(
        bytes32 indexed node,
        address indexed owner,
        bytes dnsname,
        uint32 inception
    );
53: event NewPublicSuffixList(address suffixes);

```
[DNSRegistrar.sol#L47-L53](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L47-L53)


##

## [G-5] Lack of input value checks cause a redeployment if any human/accidental errors

> Instances(4)

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables.

If any human/accidental errors happen need to redeploy the contract so this create the huge gas lose

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

constructor(ENS _ens, DNSSEC _oracle, string memory _gatewayURL) {
        ens = _ens;
        oracle = _oracle;
        gatewayURL = _gatewayURL;
    }

```
[OffchainDNSResolver.sol#L43-L47](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L43-L47)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

62: previousRegistrar = _previousRegistrar;
63: resolver = _resolver;
64: oracle = _dnssec;
65: suffixes = _suffixes;
67: ens = _ens;
```
[DNSRegistrar.sol#L62-L67](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L62-L67)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

80: function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
        emit NewPublicSuffixList(address(suffixes));
    }

```
[DNSRegistrar.sol#L80-L83](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

55: anchors = _anchors;

```
[DNSSECImpl.sol#L55](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L55)

##

## [G-6] Use nested if and, avoid multiple check combinations

> Instances(2)

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

As per Solidity [reports](https://gist.github.com/sathishpic22/fe96671bafb22ceaace7fc05a66bd115) possible to save 9 gas

```solidity
FILE: FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

178: if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {

```
[OffchainDNSResolver.sol#L178](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L178)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

128: if (x0 == 0 && y0 == 0) {

```
[EllipticCurve.sol#L128](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128)

##

## [G-7] Unnecessary look up in if condition

> Instances(7)

If the || condition isn’t required, the second condition will have been looked up unnecessarily.

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

86:  if (
                !rrname.equals(name) ||
                iter.class != CLASS_INET ||
                iter.dnstype != TYPE_TXT
            ) {

144:  if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {

```
[OffchainDNSResolver.sol#L86-L90](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L90)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

187: if (owner == address(0) || owner == previousRegistrar) {

```
[DNSRegistrar.sol#L187](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L187)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

138: if (0 == x || x == p || 0 == y || y == p) {
392: if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {
41:  if (u == 0 || u == m || m == 0) return 0;

```
[EllipticCurve.sol#L138](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L138)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

201: if (
                    name.length != iter.data.nameLength(iter.offset) ||
                    !name.equals(0, iter.data, iter.offset, name.length)
                ) {

```
[DNSSECImpl.sol#L201-L204](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L201-L204)

##


## [G-8] Don't declare the variable inside the loops

> Instances(3)

In every iterations the new variables instance created this will consumes more gas . So just declare variables outside the loop and only use inside to save gas

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSClaimChecker.sol

29: for (
            RRUtils.RRIterator memory iter = data.iterateRRs(0);
            !iter.done();
            iter.next()
        ) {
            if (iter.name().compareNames(buf.buf) != 0) continue;
            bool found;  /// @audit found inside loop
            address addr;  /// @audit addr inside loop
            (addr, found) = parseRR(data, iter.rdataOffset, iter.nextOffset);
            if (found) {
                return (addr, true);
            }
        }

51: while (idx < endIdx) {
            uint256 len = rdata.readUint8(idx);
            idx += 1;

            bool found;
            address addr;

```
[DNSClaimChecker.sol#L29-L40](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L29-L40)

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L341-L342


##

## [G-9] Functions should be used instead of modifiers to save gas

> Instances(1)

```solidity
File; 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

modifier onlyOwner() {
        Root root = Root(ens.owner(bytes32(0)));
        address owner = root.owner();
        require(msg.sender == owner);
        _;
    }

```
[DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

##

## [G-10] Sort Solidity operations using short-circuit mode

> Instances(5)

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```

//f(x) is a low gas cost operation
//g(y) is a high gas cost operation
//Sort operations with different gas costs as follows
f(x) || g(y)
f(x) && g(y)

```

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

42:   if (u == 0 || u == m || m == 0) return 0;
138:  if (0 == x || x == p || 0 == y || y == p) {
392:  if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {

```
[EllipticCurve.sol#L42](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L42)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

187:  if (owner == address(0) || owner == previousRegistrar) {
```
[DNSRegistrar.sol#L187](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L187)


```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

if (
                !rrname.equals(name) ||
                iter.class != CLASS_INET ||
                iter.dnstype != TYPE_TXT
            ) {

```
[OffchainDNSResolver.sol#L86-L90](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L86-L90)



##

## [G-11] Use assembly to check for address(0)

> Instances(7)

Saves 6 gas per instance

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

102: if (dnsresolver != address(0)) 
197: if (resolver == address(0)) {

```
[OffchainDNSResolver.sol#L102](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L102)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

115: if (addr != address(0)) {
116: if (resolver == address(0)) {
187: if (owner == address(0) || owner == previousRegistrar) {

```
[DNSRegistrar.sol#L115-L116](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L115-L116)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

317:  if (address(algorithm) == address(0)) {
420:  if (address(digests[digesttype]) == address(0)) {

```
[DNSSECImpl.sol#L317](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L317)

##

## [G-12] Shorthand way to write if / else statement can reduce the deployment cost

> Instances(4)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/OffchainDNSResolver.sol

if (separator < lastIdx) {
            parentNode = textNamehash(name, separator + 1, lastIdx);
        } else {
            separator = lastIdx;
        }

```
[OffchainDNSResolver.sol#L216-L220](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L216-L220)

```solidity
FILE : 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

if (shortest - idx >= 32) {
                    mask = type(uint256).max;
                } else {
                    mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
                }

```
[BytesUtils.sol#L87-L91](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L87-L91)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

221: if (isZeroCurve(x0, y0)) {
            return (x1, y1, z1);
        } else if (isZeroCurve(x1, y1)) {
            return (x0, y0, z0);
        }

234: if (t0 == t1) {
                return twiceProj(x0, y0, z0);
            } else {
                return zeroProj();
            }

```
[EllipticCurve.sol#L221-L225](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L221-L225)

### Recommended Mitigation

```solidity

if (separator < lastIdx) {
            parentNode = textNamehash(name, separator + 1, lastIdx);
        } else {
            separator = lastIdx;
        }

```

```solidity

separator < lastIdx ? parentNode = textNamehash(name, separator + 1, lastIdx); : separator = lastIdx ;

```

##

## [G-13] The Less gas consuming condition checks should be on top

> Instances(1)

When writing conditional statements in smart contracts, it is generally best practice to order the conditions so that the less gas-consuming checks are performed first. This can help to optimize the gas usage of the contract and improve its overall efficiency

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

> "if (!isOnCurve(Q[0], Q[1])) {" check should come top then "if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) { " condition 

  if (rs[0] == 0 || rs[0] >= n || rs[1] == 0) {
            // || rs[1] > lowSmax)
            return false;
        }
        if (!isOnCurve(Q[0], Q[1])) {
            return false;
        }

```
[EllipticCurve.sol#L392-L396](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L392-L396)

##

## [G-14] internal functions not called by the contract should be removed to save deployment gas

> Instances(3)

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword


```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

386: function validateSignature(
        bytes32 message,
        uint256[2] memory rs,
        uint256[2] memory Q
    ) internal pure returns (bool) {

377: function multipleGeneratorByScalar(
        uint256 scalar
    ) internal pure returns (uint256, uint256) {

316: function multiplyPowerBase2(
        uint256 x0,
        uint256 y0,
        uint256 exp
    ) internal pure returns (uint256, uint256) {

```
[EllipticCurve.sol#L386-L390](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L386-L390)

##

## [G-15] Modifiers or private functions only called once can be inlined to save gas 

Instances (2)

ITs possible to save 40-50 gas 

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

73: modifier onlyOwner() {

```
[DNSRegistrar.sol#L73-L78](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L73-L78)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

273: function memcpy(uint256 dest, uint256 src, uint256 len) private pure {

```
[BytesUtils.sol#L273](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L273)

##

## [G-16] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

Instances (12)

```solidity
FILE:  2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

166: function enableNode(bytes memory domain) public returns (bytes32 node) {

174: function _enableNode(
        bytes memory domain,
        uint256 offset
    ) internal returns (bytes32 node) {

```
[DNSRegistrar.sol#L166](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L166)

```solidity
FILE: 2023-04-ens/contracts/utils/NameEncoder.sol

9: function dnsEncodeName(
        string memory name
    ) internal pure returns (bytes memory dnsName, bytes32 node) {

```
[NameEncoder.sol#L9-L11](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L9-L11)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/algorithms/EllipticCurve.sol

106: function zeroProj()
        internal
        pure
        returns (uint256 x, uint256 y, uint256 z)
    {

117:  function zeroAffine() internal pure returns (uint256 x, uint256 y) {

124: function isZeroCurve(
        uint256 x0,
        uint256 y0
    ) internal pure returns (bool isZero) {

208: function addProj(
        uint256 x0,
        uint256 y0,
        uint256 z0,
        uint256 x1,
        uint256 y1,
        uint256 z1
    ) internal pure returns (uint256 x2, uint256 y2, uint256 z2) {

335: function multiplyScalar(
        uint256 x0,
        uint256 y0,
        uint256 scalar
    ) internal pure returns (uint256 x1, uint256 y1) {

```
[EllipticCurve.sol#L106-L110](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L106-L110)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/DNSSECImpl.sol

87: function verifyRRSet(
        RRSetWithSignature[] memory input
    )
        external
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)

107: function verifyRRSet(
        RRSetWithSignature[] memory input,
        uint256 now
    )
        public
        view
        virtual
        override
        returns (bytes memory rrs, uint32 inception)

140: function validateSignedSet(
        RRSetWithSignature memory input,
        bytes memory proof,
        uint256 now
    ) internal view returns (RRUtils.SignedSet memory rrset) {

181: function validateRRs(
        RRUtils.SignedSet memory rrset,
        uint16 typecovered
    ) internal pure returns (bytes memory name) {



```
[DNSSECImpl.sol#L87-L94](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L87-L94)

##

## [G-17] Use constants instead of type(uintx).max

Instances (5):

type(uint256).max it uses more gas in the distribution process and also for each transaction than constant usage

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/RecordParser.sol

24: if (separator == type(uint256).max) {
25: return ("", "", type(uint256).max);

33: if (terminator == type(uint256).max) {

```
[RecordParser.sol#L24-L25](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L24-L25)

```solidity
FILE: 2023-04-ens/contracts/dnssec-oracle/BytesUtils.sol

398: return type(uint256).max;
88:  mask = type(uint256).max;

```
[BytesUtils.sol#L398](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L398)

##

## [G-18] Instead of calculating bytes32(0) every time inside the contract its possible to use constants to reduce the gas cost 

Instances (1)

By defining a constant variable, you can avoid computing the value of bytes32(0) every time it is used in your contract. Instead, you can simply reference the constant variable, which will have the same value as bytes32(0)

```solidity
FILE: 2023-04-ens/contracts/dnsregistrar/DNSRegistrar.sol

74:  Root root = Root(ens.owner(bytes32(0)));
180: return bytes32(0);
188: if (parentNode == bytes32(0)) {
189: Root root = Root(ens.owner(bytes32(0)));


```

[DNSRegistrar.sol#L74](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L74)

##








GAS‑1	abi.encode() is less efficient than abi.encodepacked()	1	100
GAS‑2	<array>.length Should Not Be Looked Up In Every Loop Of A For-loop	2	194
GAS‑3	Use calldata instead of memory for function parameters	6	1800
GAS‑4	Setting the constructor to payable	3	39
GAS‑5	Duplicated require()/revert() Checks Should Be Refactored To A Modifier Or Function	2	56
GAS‑6	Using delete statement can save gas	10	-
GAS‑7	++i Costs Less Gas Than i++, Especially When It’s Used In For-loops (--i/i-- Too)	5	30
GAS‑8	Functions guaranteed to revert when called by normal users can be marked payable	3	63
GAS‑9	Use hardcoded address instead address(this)	4	-
GAS‑10	It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied	4	-
GAS‑11	internal functions only called once can be inlined to save gas	63	1386
GAS‑12	Optimize names to save gas	9	198
GAS‑13	<x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables	25	-
GAS‑14	Public Functions To External	6	-
GAS‑15	require() Should Be Used Instead Of assert()	3	-
GAS‑16	require()/revert() Strings Longer Than 32 Bytes Cost Extra Gas	3	-
GAS‑17	Save gas with the use of specific import statements	44	-
GAS‑18	Shorten the array rather than copying to a new one	1	-
GAS‑19	Splitting require() Statements That Use && Saves Gas	1	9
GAS‑20	Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It	1	-
GAS‑21	Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead	40	-
GAS‑22	++i/i++ Should Be unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops	7	245
GAS‑23	Using unchecked blocks to save gas	4	80
GAS‑24	Use v4.8.1 OpenZeppelin contracts	1	-
GAS‑25	Use solidity version 0.8.19 to gain some gas boost	19	1672
GAS‑26	Use uint256(1)/uint256(2) instead for true and false boolean states	2	10000