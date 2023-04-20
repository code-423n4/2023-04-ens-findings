# 1 - Inapropriate use of assert in readTXT()

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L162

the readTXT() function uses an assert statement to check if the start index plus the field length is less than the last index. In Solidity, assert statements should only be used to check for internal errors and not for validating external inputs. Instead, use a require statement for better error handling and to prevent unexpected contract termination. 

Replace the following line:https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L162

```
assert(startIdx + fieldLength < lastIdx);
```
with:
```
require(startIdx + fieldLength < lastIdx, "Invalid TXT record");
```
