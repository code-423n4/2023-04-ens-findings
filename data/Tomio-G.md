1.
Title: function `labelCount()` gas improvement on returning value

Proof of Concept:
[RRUtils.sol#L59](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/RRUtils.sol#L59)

Recommended Mitigation Steps:
by set `count` in returns L#58 and delete L#59 can save gas

```
function labelCount(
        bytes memory self,
        uint256 offset
    ) internal pure returns (uint256 count) { //@audit-info: set here
```
________________________________________________________________________

2.
Title: Unused code

Proof of Concept:
[SHA1.sol#L4](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/SHA1.sol#L4)

Recommended Mitigation Steps:
The `Debug` event is not necessary for the functionality of the code and adds unnecessary gas costs. It should be removed.
________________________________________________________________________