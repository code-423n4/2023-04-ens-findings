
1. Shorten path “../../contracts” into “../”
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L4C63-L6
2. Unused imports:
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L8
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L4
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L6-L9
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L8
- Suggestion: Use named imports for explicitness
3. The name `BytesUtils` is re-used in two different files: `dnssec-oracle/BytesUtils.sol` and `wrapper/BytesUtils.sol`. Second one is not in scope but still dangerous as it can lead to confusion and errors when the contracts are compiled. It is good practice to avoid naming conflicts.
4. Unused variables:
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L16-L17C35
5. State variables modified after external call (possible re-entrancy):
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L155
6. Unused returns:
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol#L24-L27C26
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L98
7. Missing zero address checks:
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L62-L67
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L44-L45
8. Revert without any message:
- https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSRegistrar.sol#L76


