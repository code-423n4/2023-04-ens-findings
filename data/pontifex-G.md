### GAS-1 Pre increment/decrement costs less gas than post increment/decrement or `<x> += 1` and `<x> -= 1`
Pre increment/decrement costs less than post increment decrement/decrement or `<x> += 1` and `<x> -= 1`. It can save more than 5 gas per execution. You can also use `unchecked{++i;}` for even more gas savings.
There are seven instances:
```solidity
53:            idx += 1;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L53
```solidity
67:            count += 1;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L67
```solidity
269:            counts--;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L269
```solidity
294:            counts--;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L294
```solidity
300:            othercounts--;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L300
```solidity
309:            counts -= 1;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L309
```solidity
36:                    labelLength += 1;
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L36


### GAS-2 BytesUtils_#78-83 Declaring variables in a loop
There is no necessity to declare variables `a` and `b` inside of the loop. Declaring them outside of the loop will save some gas.
```solidity
77:        for (uint256 idx = 0; idx < shortest; idx += 32) {
78:            uint256 a;
79:            uint256 b;
80:            assembly {
81:                a := mload(selfptr)
82:                b := mload(otherptr)
83:            }
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L77-L83

### GAS-3 Using redundant caching
Using the `owner` variable for caching is redundant. I suggest using the `root.owner()` in the `require` instead of the redundant variable.
```solidity
75:        address owner = root.owner();
76:        require(msg.sender == owner);
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L75-L76

### GAS-4 Use `_suffixes` parameter instead of `suffixes` state variable in the `event` 
Using the `_suffixes` parameter instead of the `suffixes` state variable in the `event` will save gas.
```solidity
81:        suffixes = _suffixes;
82:        emit NewPublicSuffixList(address(suffixes));
``` 
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L82

### GAS-5 Move check up to save gas in case of `revert`
There is a [check](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L157-L161) in the bottom of the `_claim` function body, which can be moved up to save gas in case of revert. It can be put after the [line #137](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L137).

### GAS-6 Use caching for `<array>.length` in loops
Caching arrays length instead of using `<array>.length` in a loop can save some gas.
There are two instances:
```solidity
25:            assert(idx < self.length);
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L25
```solidity
61:            assert(offset < self.length);
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L61

