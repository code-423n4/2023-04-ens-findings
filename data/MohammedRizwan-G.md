## Summary

### Gas Optimizations
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [G&#x2011;01] | Avoid using state variable in emit | 1 |
| [G&#x2011;02] | Use nested if and, avoid multiple check combinations | 2 |


### [G&#x2011;01]  Avoid using state variable in emit
While emitting events, Passing parameter variables will cost less as compared to passing the storage values.
There are 1 instances of this issues.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

80    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
81        suffixes = _suffixes;
82        emit NewPublicSuffixList(address(suffixes));
83    }
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83)

### Recommended Mitigation steps
```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

    function setPublicSuffixList(PublicSuffixList _suffixes) public onlyOwner {
        suffixes = _suffixes;
-        emit NewPublicSuffixList(address(suffixes));
+        emit NewPublicSuffixList(address(_suffixes));
    }
```

### [G&#x2011;02]  Use nested if and, avoid multiple check combinations
Using nested if is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.
There are 2 instances of this issue.

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

178        if (nameOrAddress[idx] == "0" && nameOrAddress[idx + 1] == "x") {
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L178)

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

128        if (x0 == 0 && y0 == 0) {
```
[Link to code](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L128)
