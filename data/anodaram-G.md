# In `NameEncoder.dnsEncodeNames`, we can reduce the length of the return value.
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L15
``` solidity
File: NameEncoder.sol
15:         dnsName = new bytes(length + 2);
```
In the current code, it mallocs `length + 2` bytes for `dnsName`.
But by checking the code, it always use `length + 1`.
So we can use `length + 1`.
Fixed:
``` solidity
File: NameEncoder.sol
15:         dnsName = new bytes(length + 1);
```
