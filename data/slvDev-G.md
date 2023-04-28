### Gas Report
*Excluded everything that mentions in the automatic report.*

|  | Issue | Instances |
| --- | --- | --- |
| [G-01] | Pack state variables | 1 |
| [G-02] | State variables should be cached in stack variables rather than re-reading them from storage | 1 |
| [G-03] | Multiple address /ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate | 2 |
| [G-04] | Consolidate events | 2 |
| [G-05] | Use nested if and, avoid multiple check combinations | 2 |
| [G-06] | Use assembly to write address storage values | 5 |
| [G-07] | The solady Library's Ownable contract is significantly gas-optimized, which can be used | 1 |
| [G-08] | Sort Solidity operations using short-circuit mode | 1 |

## [G-01] Pack state variables

Solidity docs: [https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html)

State variable packaging in `DNSSECImpl.sol` contract (1 * 2k = 2k gas saved)

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

contract DNSSECImpl is DNSSEC, Owned {
    ...

    uint16 constant DNSCLASS_IN = 1;

    uint16 constant DNSTYPE_DS = 43;
    uint16 constant DNSTYPE_DNSKEY = 48;

-   uint256 constant DNSKEY_FLAG_ZONEKEY = 0x100;
+   uint128 constant DNSKEY_FLAG_ZONEKEY = 0x100;
        ^^^ Can be changed to any uint. Sum of all state uint should be less 256 bits to pack in one slot
```

## [G-02] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

```solidity
function verifyDSHash(
    uint8 digesttype,
    bytes memory data,
    bytes memory digest
) internal view returns (bool) {
+   Digest memory digest = digests[digesttype]
-   if (address(digests[digesttype]) == address(0)) {
+   if (address(digest) == address(0)) {
        return false;
    }
-   return digests[digesttype].verify(data, digest);
+   return digest.verify(data, digest);
}
```

## [G-03] Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate

Saves a storage slot for mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

mapping(uint8 => Algorithm) public algorithms;
mapping(uint8 => Digest) public digests;
```

## [G-04] Consolidate events

Instead of having separate events for **`AlgorithmUpdated`** and **`DigestUpdated`**, you can create a single event **`Updated`** with an additional field to specify if it's an algorithm or digest update.

This can save some gas when emitting events.

```solidity
event Updated(uint8 id, address addr, bool isAlgorithm);
or
event Updated(uint8 id, address addr, string "Algorithm" || "Digest");
```

## [G-05] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using `&&` multiple check combinations. There are more advantages, such as easier-to-read code and better coverage reports.

```solidity
File: contracts\dnsregistrar\OffchainDNSResolver.sol

if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {
```

```solidity
File: contracts\dnssec-oracle\algorithms\EllipticCurve.sol

if (x0 == 0 && y0 == 0) {
```

## [G-06] Use `assembly` to write *address storage values*

```solidity
File: contracts\dnsregistrar\DNSRegistrar.sol

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

## [G-07] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses `OR/AND` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```solidity
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

```solidity
File: contracts\Frankencoin.sol

} else if (isMinter(spender) || isMinter(isPosition(spender))){
```

## [G-08] The solady Library's Ownable contract is significantly gas-optimized, which can be used

The project uses the `onlyOwner` authorization model I recommend using *Solady's highly gas optimized contract.*

[https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol](https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol)

```solidity
File: contracts\dnssec-oracle\DNSSECImpl.sol

import "./Owned.sol";
contract DNSSECImpl is DNSSEC, Owned {

function setAlgorithm(uint8 id, Algorithm algo) public owner_only {
function setDigest(uint8 id, Digest digest) public owner_only {
```