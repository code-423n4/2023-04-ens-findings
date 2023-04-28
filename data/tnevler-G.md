# Report
## Gas Optimizations ##
### [G-1]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```uint256 lastTxtIdx = txt.find(5, txt.length - 5, " ");``` [L149](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L149) (txt.length - 5, checked in L144)

**Description:**

About 35 gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.
