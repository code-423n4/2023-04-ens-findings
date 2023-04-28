### N-1 Declare variables in one line with assignment
There is no necessity to declare variables separately from destructuring assignment when calling another function that returns multiple values. Declare them in one line with assignments similar to other places of the protocol.
There are four instances:
```solidity
35:            bool found;
36:            address addr;
37:            (addr, found) = parseRR(data, iter.rdataOffset, iter.nextOffset);
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L35-L37
```solidity
55:            bool found;
56:            address addr;
57:            (addr, found) = parseString(rdata, idx, len);
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L55-L57
```solidity
39:        bool ok;
40:        bytes memory result;
41:        (ok, result) = RSAVerify.rsarecover(modulus, exponent, sig);
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol#L39-L41
```solidity
38:        bool ok;
39:        bytes memory result;
40:        (ok, result) = RSAVerify.rsarecover(modulus, exponent, sig);
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol#L38-L40


### N-2 `constant` should be defined rather than using magic number
Use readable constant instead of a numeric value with a comment.
There are two instances:
```solidity
72:        if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false); // 0x613d3078 == 'a=0x'
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L72
```solidity
294:        if (dnskey.protocol != 3) {
```
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/DNSSECImpl.sol#L294
