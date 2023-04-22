# Conversion to lowercase to remove if-clause

In [`hexStringToBytes32`](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L11) function gas can be saved by converting letters to lowercase:

```diff
diff --git a/HexUtils.sol.orig b/HexUtils.sol
index 3508390..004a784 100644
--- a/HexUtils.sol.orig
+++ b/HexUtils.sol
@@ -26,11 +26,8 @@ library HexUtils {
                     ascii := sub(c, 48)
                     leave
                 }
-                // chars 65-70: A-F
-                if and(gt(c, 64), lt(c, 71)) {
-                    ascii := add(sub(c, 65), 10)
-                    leave
-                }
+                // convert to lowercase
+                c := or(c, 32)
                 // chars 97-102: a-f
                 if and(gt(c, 96), lt(c, 103)) {
                     ascii := add(sub(c, 97), 10)
```

This is a common bit-trick, it works by leveraging the fact that ASCII representation of uppercase and lowercase letters differs only by one bit: `01000001` (`A`), `01100001` (`a`). By `or`-ing character value with 32 (in binary `00100000`) we convert it to it's lowercase equivalent.

Provided code passes project's tests, to further assure no change in function's behaviour, here is Python implementation of proposed optimization:
```python
# https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L11
def to_hex_original(c: int):
    if 47 < c and c < 58:
        return c - 48
    elif 64 < c and c < 71:
        return c - 65 + 10
    elif 96 < c and c < 103:
        return c - 97 + 10
    return 0xff

expected_values = [to_hex_original(c) for c in range(0x100)]
    
def to_hex_optimized(c: int):
    if 47 < c and c < 58:
        return c - 48
    c |= 0b00100000
    if 96 < c and c < 103:
        return c - 97 + 10
    return 0xff

optimized_results = [to_hex_optimized(c) for c in range(0x100)]
print('Expect: ', expected_values)
print('Result: ', optimized_results)

assert(expected_values == optimized_results)

print('Test passed!')
```

## Estimating GAS savings

Project test's report:
- Old Avg: `234290`
- New Avg: `230828`
- Diff `234290` - `230828`: `3462`

This optimization depends on values being converted, as it produces no savings when converting ASCII digits. 