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

QA10.  ``hexStringToBytes32()`` fails to check that range [idx, lastIdx] is within 32 bytes range and thus the returned ``r`` will fit into bytes32. 

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L11-L60](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L11-L60)

Mitigation: 
Introduce the check:
```diff
function hexStringToBytes32(
        bytes memory str,
        uint256 idx,
        uint256 lastIdx
    ) internal pure returns (bytes32 r, bool valid) {
+       if (lastIdx - idx > 32) return (bytes32(0x0), false);
        valid = true;
        assembly {
            // check that the index to read to is not past the end of the string
            if gt(lastIdx, mload(str)) {
                revert(0, 0)
            }

            function getHex(c) -> ascii {
                // chars 48-57: 0-9
                if and(gt(c, 47), lt(c, 58)) {
                    ascii := sub(c, 48)
                    leave
                }
                // chars 65-70: A-F
                if and(gt(c, 64), lt(c, 71)) {
                    ascii := add(sub(c, 65), 10)
                    leave
                }
                // chars 97-102: a-f
                if and(gt(c, 96), lt(c, 103)) {
                    ascii := add(sub(c, 97), 10)
                    leave
                }
                // invalid char
                ascii := 0xff
            }

            let ptr := add(str, 32)
            for {
                let i := idx
            } lt(i, lastIdx) {
                i := add(i, 2)
            } {
                let byte1 := getHex(byte(0, mload(add(ptr, i))))
                let byte2 := getHex(byte(0, mload(add(ptr, add(i, 1)))))
                // if either byte is invalid, set invalid and break loop
                if or(eq(byte1, 0xff), eq(byte2, 0xff)) {
                    valid := false
                    break
                }
                let combined := or(shl(4, byte1), byte2)
                r := or(shl(8, r), combined)
            }
        }
    }
```

QA11. readKeyValue() fails to enforce the constraint ``offset+len<=input.length``. As a result, the key-value pair might be read from dirty memory area that is beyond the memory range of ``input`` and thus could be wrong. 

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L14-L40](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L14-L40)

Mitigation: make sure ``offset+len<=input.length``:
```diff
function readKeyValue(
        bytes memory input,
        uint256 offset,
        uint256 len
    )
        internal
        pure
        returns (bytes memory key, bytes memory value, uint256 nextOffset)
    {

+       if(offset + len > input.length) revert outOfBoundAccess();

        uint256 separator = input.find(offset, len, "=");
        if (separator == type(uint256).max) {
            return ("", "", type(uint256).max);
        }

        uint256 terminator = input.find(
            separator,
            len + offset - separator,
            " "
        );
        if (terminator == type(uint256).max) {
-            terminator = input.length;
+            terminator = offset + len;
        }

        key = input.substring(offset, separator - offset);
        value = input.substring(separator + 1, terminator - separator - 1);
        nextOffset = terminator + 1;
    }
```

QA12: The mask here should have 12 ending zero instead of 14 ending zero. 

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L248](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L248)

Impact: so far, no affecting the result, but desirable to have a one-word mask instead of 34 bytes mask.

Mitigation: change the mask to have 12 ending zero instead of 14 ending zero. 