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
QA6: memcpy() will have side effect (the copy might overwrite its own source) when the two memory ranges overlap, therefore, it is important to check that the two memory ranges do not overlap.

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L273-L292](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L273-L292)

Mitigation:
```diff
 function memcpy(uint256 dest, uint256 src, uint256 len) private pure {
+       if((src <= dest && src + len > dest) || dest <= src && dest + len > src) 
+             revert copyBetweenOverlappingMemoryRanges();

        // Copy word-length chunks while possible
        for (; len >= 32; len -= 32) {
            assembly {
                mstore(dest, mload(src))
            }
            dest += 32;
            src += 32;
        }

        // Copy remaining bytes
        unchecked {
            uint256 mask = (256 ** (32 - len)) - 1;
            assembly {
                let srcpart := and(mload(src), not(mask))
                let destpart := and(mload(dest), mask)
                mstore(dest, or(destpart, srcpart))
            }
        }
    }
```

QA7. "52" should be "51". Otherwise there might be an overflow issue here. This is because 
 52*5 = 260 bits > 32*8 bits. The largest ``len`` to avoid overflow is 51.  

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L332-L377](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L332-L377)

```diff
    function base32HexDecodeWord(
        bytes memory self,
        uint256 off,
        uint256 len
    ) internal pure returns (bytes32) {
-        require(len <= 52);
+        require(len <= 51);
```

QA8: Valid decoded value should be smaller than ``0x20``, not equal. 

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L345](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L345)

Mitigation:
```diff
 for (uint256 i = 0; i < len; i++) {
            bytes1 char = self[off + i];
            require(char >= 0x30 && char <= 0x7A);
            decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);
-            require(decoded <= 0x20);
+            require(decoded < 0x20);
            if (i == len - 1) {
                break;
            }
            ret = (ret << 5) | decoded;
        }
```

QA9: parseString() uses the wrong offset of ``lastIdx``:

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L66-L74](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/DNSClaimChecker.sol#L66-L74)

Mitigation:
```diff
 function parseString(
        bytes memory str,
        uint256 idx,
        uint256 len
    ) internal pure returns (address, bool) {
        // TODO: More robust parsing that handles whitespace and multiple key/value pairs
        if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false); // 0x613d3078 == 'a=0x'
-        return str.hexToAddress(idx + 4, idx + len);
+        return str.hexToAddress(idx + 4, idx + 4 + len);
    }
```