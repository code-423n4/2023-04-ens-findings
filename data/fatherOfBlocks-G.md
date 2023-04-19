**contracts/dnsregistrar/DNSClaimChecker.sol**
- L4 - The DNSSEC.sol contract is imported, but it is never used, therefore extra gas would be spent in the deploy, in addition to seeing the entire contract, it would become much more confusing to understand since it opened dead code,


**contracts/dnsregistrar/OffchainDNSResolver.sol**
- L11 - The ENSRegistry.sol abstract is imported, but it is never used, therefore extra gas would be spent on the deploy, in addition to seeing the entire contract, it would become much more confusing to understand since it opened dead code,

- L29/30 - Two constants are created, CLASS_INET and TYPE_TXT but these are already found in the DNSClaimChecker(L16/17) library. This should be unified in one place.

- L144/149/150/151/157 - The same value is used multiple times within the function, therefore a variable in memory of that value could be created. In this case txt.length.

- L151/152/154/155 - It is not necessary to create a variable in memory if it is only going to be used once.


**contracts/dnsregistrar/DNSRegistrar.sol**
- L11 - The ENSRegistry.sol contract is imported, but it is never used, therefore extra gas would be spent in the deploy, besides that when seeing the entire contract, it would become much more confusing to understand since it opened dead code,

- L73/80 - The onlyOwner() modifier is created but it is only used once, therefore it would be less expensive to do the check directly on L81.


**contracts/dnssec-oracle/digests/SHA1Digest.sol**
- L18/19/20 - It is not necessary to create a variable in memory if it is only going to be used once. Therefore, gas would be saved if the line would be:
return hash.readBytes20(0) == SHA1.sha1(data);


**contracts/dnssec-oracle/SHA1.sol**
- L4 - Dead code - An event is created, DEBUG that is never used, therefore it generates an extra gas cost, which is not necessary.

