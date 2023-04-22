## Use assembly to check for address(0)
Saves 6 gas per instance 

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L317 

       if (address(algorithm) == address(0)) {


https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/DNSSECImpl.sol#L420 

        if (address(digests[digesttype]) == address(0)) { 


https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L115 

        if (addr != address(0)) {
            if (resolver == address(0)) { 

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L187 

        if (owner == address(0) || owner == previousRegistrar) {

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L145 

            return (address(0), ""); 

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L197

        if (resolver == address(0)) {
            return address(0); 

