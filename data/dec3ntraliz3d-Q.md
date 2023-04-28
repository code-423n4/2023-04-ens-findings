
## L-01
`find()` function in `BytesUtils.sol` should add a check for array out of bound.

## Summary
The `find()` function in `BytesUtils.sol` is used to locate a needle in a bytes array but does not check if `off + len` is within `bytes.length`, potentially leading to out-of-bounds access.

```solidity
   function find(
        bytes memory self,
        uint256 off,
        uint256 len,
        bytes1 needle
    ) internal pure returns (uint256) {
        for (uint256 idx = off; idx < off + len; idx++) {
            if (self[idx] == needle) {
                return idx;
            }
        }
        return type(uint256).max;
    }
}
```


[Link to the code on Github](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L387-L399)

Here are the recommended changes.

```diff
 function find(
        bytes memory self,
        uint256 off,
        uint256 len,
        bytes1 needle
    ) internal pure returns (uint256) {
+       require(off + len <= self.length);
		for (uint256 idx = off; idx < off + len; idx++) {
            if (self[idx] == needle) {
                return idx;
            }
        }
        return type(uint256).max;
    }
}

```