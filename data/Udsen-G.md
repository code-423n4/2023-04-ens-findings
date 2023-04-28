## 1. USING `unchecked` BLOCKS TO SAVE GAS

In `RRUtils.nameLength()` function, the `return idx - offset;` can be `unchecked` since `idx` is either equal to `offset` or greater than `offset`. 

        return idx - offset;

Similarly In `RRUtils.rdata()` function, the `iter.nextOffset - iter.rdataOffset` can be `unchecked` since it can never underflow since `iter.nextOffset >= iter.rdataOffset`.

        iter.nextOffset - iter.rdataOffset
				
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L32
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L206