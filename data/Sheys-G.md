#1 In DNSSECImpl.sol:
subsequent use of <array>.length in loops costs more gas because every instances reads from storage. It should be cached into a local variable for subsequent reads. Instances of syntaxes includes: 
L118:  https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L118, 
L196: https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L196, 
L202: https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L202, 
L203: https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L203

For example, in L118, instead of using:
```
 for (uint256 i = 0; i < input.length; i++) {
```
replace with:
```
uint256 inputs =  input.length;
 for (uint256 i = 0; i < inputs; i++) {
```

#2 OffchainDNSResolver.sol:
L144: https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L144,
L149: https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L149,
L150:https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L150,
L151: https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L151,
L157: https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/OffchainDNSResolver.sol#L157