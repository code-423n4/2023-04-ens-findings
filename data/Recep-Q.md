## LOW
### Missing zero-address check
setPublicSuffixList function in DNSRegistrar contract accepts zero-address. It is a better practice to check whether it is a zero-address and continue after the check because it can affect the functionality.

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L80-L83
