QA1. ``equals()`` fails to handle the ``OffsetOutOfBoundsError`` case.

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L111-L119](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L111-L119)

Mitigation: check the ``OffsetOutOfBoundsError`` case using a requirement statement.
```diff
 function equals(
        bytes memory self,
        uint256 offset,
        bytes memory other,
        uint256 otherOffset,
        uint256 len
    ) internal pure returns (bool) {
+       if (offset + len > self.length || otherOffset+len > other.length) {
+            revert OffsetOutOfBoundsError(offset + len, self.length);
+        }


        return keccak(self, offset, len) == keccak(other, otherOffset, len);
    }
```

QA2. The NatSpec says for ``compare()``: "Returns a positive number if `other` comes lexicographically after `self`, a negative number if it comes before, or zero if the contents of the two bytes are equal.". Acutually, the opposite is true: "Returns a negative number if `other` comes lexicographically after `self`, a positive number if it comes before, or zero if the contents of the two bytes are equal."

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L52-L100](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L52-L100)
