G1. When a != b at L84, There are only two cases: 1) NOT last iteration, we just need to return a-b, no masking is needed; 2) last iteration, we need masking, but we can return a-b or len-otherlen in this case. In both case, we are ready to return. 

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L84-L94](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L84-L94) 

Based on such observation, our optimization is as follows:
```diff
function compare(
        bytes memory self,
        uint256 offset,
        uint256 len,
        bytes memory other,
        uint256 otheroffset,
        uint256 otherlen
    ) internal pure returns (int256) {
        if (offset + len > self.length) {
            revert OffsetOutOfBoundsError(offset + len, self.length);
        }
        if (otheroffset + otherlen > other.length) {
            revert OffsetOutOfBoundsError(otheroffset + otherlen, other.length);
        }

        uint256 shortest = len;
        if (otherlen < len) shortest = otherlen;

        uint256 selfptr;
        uint256 otherptr;

        assembly {
            selfptr := add(self, add(offset, 32))
            otherptr := add(other, add(otheroffset, 32))
        }
        for (uint256 idx = 0; idx < shortest; idx += 32) {
            uint256 a;
            uint256 b;
            assembly {
                a := mload(selfptr)
                b := mload(otherptr)
            }
            if (a != b) {
                // Mask out irrelevant bytes and check again
                uint256 mask;
                if (shortest - idx >= 32) {
 -                   mask = type(uint256).max;
 +                   return int256(a) - int256(b); @audit: no need to mask
                } else {
                    mask = ~(2 ** (8 * (idx + 32 - shortest)) - 1);
+                   int256 diff = int256(a & mask) - int256(b & mask);
+                   if(diff != 0) return diff;           @audit: either return diff
+                   else return return int256(len) - int256(otherlen); @audit: or return the diff of lens
                }
-                int256 diff = int256(a & mask) - int256(b & mask);
-                if (diff != 0) return diff;
            }
            selfptr += 32;
            otherptr += 32;
        }

-        return int256(len) - int256(otherlen);
    }
```

G2. memcpy() might copy unnecessarily the remaining bytes. For example, when ``len`` is a multiple of 32, there are no remaining bytes to copy. However, the function still copy one word from ``dest`` to `dest`` (effectively), which is a waste of gas. We can save gas for this case by checking whether ``len == 0``:

[https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L273-L292](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L273-L292)

Optimization:
```diff
function memcpy(uint256 dest, uint256 src, uint256 len) private pure {
        // Copy word-length chunks while possible
        for (; len >= 32; len -= 32) {
            assembly {
                mstore(dest, mload(src))
            }
            dest += 32;
            src += 32;
        }

+       if(len == 0) return; 

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


