## Gas Optimization

| |Issue|Instances|
|-|:-|:-:|
| [GAS-01](#GAS-01) |  Structs can be packed into fewer storage slots | 4 |

## [G‑01] Structs can be packed into fewer storage slots

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

L:81     struct SignedSet {
           uint16 typeCovered;
           uint8 algorithm;
           uint8 labels;
           uint32 ttl;
           uint32 expiration;
           uint32 inception;
           uint16 keytag;
           bytes signerName;
           bytes data;
           bytes name;
         }

L:120      struct RRIterator {
            bytes data;
            uint256 offset;
            uint16 dnstype;
            uint16 class;
            uint32 ttl;
            uint256 rdataOffset;
            uint256 nextOffset;
          }  

L:215     struct DNSKEY {
            uint16 flags;
            uint8 protocol;
            uint8 algorithm;
            bytes publicKey;
          }

L:241     struct DS {
           uint16 keytag;
           uint8 algorithm;
           uint8 digestType;
           bytes digest;
          }
```
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L81-L92
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L120-L128
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L215-L220
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L241-L246