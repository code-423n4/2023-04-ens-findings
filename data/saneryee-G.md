# Gas Optimizations Report

Report Date: 2023-04-20 06:00:54
| |Issue|Instances|
|-|:-|:-:|
|[G-01]|Multiple accesses of a `mapping/array` should use a local variable cache|1|


## [G-01] Multiple accesses of a `mapping/array` should use a local variable cache

### Impact
The instances below point to the second+ access of a value inside a mapping/array, 1within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory/calldata` 

### Findings
Total:1

[contracts/dnssec-oracle/DNSSECImpl.sol#L46](https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L46)
[contracts/dnssec-oracle/DNSSECImpl.sol#L415-L425](https://github.com/code-423n4/2023-04-ens/tree/main/contracts/dnssec-oracle/DNSSECImpl.sol#L415-L425)
```solidity
46:     mapping(uint8 => Digest) public digests;
......
415:    function verifyDSHash(
416:         uint8 digesttype,
417:         bytes memory data,
418:         bytes memory digest
419:     ) internal view returns (bool) {
420:         if (address(digests[digesttype]) == address(0)) {
421:             return false;
422:         }
423:         return digests[digesttype].verify(data, digest);
424:     }
425: }
```
