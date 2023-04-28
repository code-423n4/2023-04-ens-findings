<h1>[G-1] Use ternary operation instead of if-else statement where necessary</h1>

if else statements more has than ternary operations, using ternary operator saves around 400 gas

there are 4 instances

1.OffchainDNSResolver.sol
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L123-L127
the blob above can be rewritten as 
```solidity 
   (ok != 0) ? (return ret) :  revert CouldNotResolve(name);
```

2.OffchainDNSResolver.sol
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L216-L220
the blob above can be rewritten as 
```solidity 
   separator < lastIdx ? parentNode = textNamehash(name, separator + 1, lastIdx) :  separator = lastIdx;
```

3.EllipticCurve.sol
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L234-L238
the blob above can be rewritten as 
```solidity 
  return (t0 == t1 ? wiceProj(x0, y0, z0) : zeroProj());
```

4.BytesUtils.sol
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L87-L91
the blob above can be rewritten as 
```solidity 
 shortest - idx >= 32 ? mask = type(uint256).max :  mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
```