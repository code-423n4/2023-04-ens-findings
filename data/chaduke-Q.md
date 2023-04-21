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

QA3. Similar to ``readUint16()``, we need to check the range of ``idx`` for readUint8():

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L179-L184](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L179-L184)

Mitigation: 
```diff
 function readUint8(
        bytes memory self,
        uint256 idx
    ) internal pure returns (uint8 ret) {
+      require(idx + 1 <= self.length);
        return uint8(self[idx]);
    }
```

QA4. The NatSpec for ``readBytes20()`` should use the phrase of ``20 bytes`` instead of ``32 bytes``. 

QA5. The ``equals()`` function can be improved by one more condition: the lengths of the two bytes ranges are equal:

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L129-L138](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L129-L138)

Mitigation:
```diff
 function equals(
        bytes memory self,
        uint256 offset,
        bytes memory other,
        uint256 otherOffset
    ) internal pure returns (bool) {
        return
+           self.length - offset == other.length - otherOffset && 
            keccak(self, offset, self.length - offset) ==
            keccak(other, otherOffset, other.length - otherOffset);
    }
```