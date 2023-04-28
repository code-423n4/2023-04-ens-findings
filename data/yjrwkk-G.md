### [G-01] Use != 0 instead of > 0 for unsigned integer comparison

When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.  

[contracts/dnssec-oracle/RRUtils.sol#L304](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/RRUtils.sol#L304)  
```
while (counts > 0 && !self.equals(off, other, otheroff)) {
```
[contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/algorithms/EllipticCurve.sol#L361)  

### [G-02] Splitting require() statements that use && saves gas

Instead of a single require statement with multiple conditions, multiple require statements with one condition per statement should be used to save gas.  

[contracts/dnssec-oracle/BytesUtils.sol#L343](https://github.com/code-423n4/2023-04-ens/blob/master/contracts/dnssec-oracle/BytesUtils.sol#L343)  
```
require(char >= 0x30 && char <= 0x7A);
```
