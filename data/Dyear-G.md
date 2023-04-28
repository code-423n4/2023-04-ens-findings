## Local variables that don't need to be defined

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L45

The `len` variable does not need to be defined, and gas can be saved by using nested function calls. And the return parameter `ret` can be removed
Like this
```solidity
 function readName(bytes memory self, uint256 offset) internal pure returns (bytes memory) {
        return self.substring(offset, nameLength(self, offset));
    }
```

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/digests/SHA1Digest.sol#L18

The `expected` and `computed` variable also does not need to be defined, can be removed
Like this
```solidity
function verify(bytes calldata data, bytes calldata hash) external pure override returns (bool) {
        require(hash.length == 20, "Invalid sha1 hash length");
        return hash.readBytes20(0) == SHA1.sha1(data);
    }
```

Like this, the `name()` function
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L189

