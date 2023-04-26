### Reduce the usage of the `mload` function wherever possible.

Using mload in a Solidity function can increase the gas cost, particularly if it is used multiple times.

Each use of mload requires accessing the EVM's memory, which can be expensive in terms of gas.


https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L11-L60
In the `hexStringToBytes32` function in `HexUtils.sol`, `mload` is used multiple times within an assembly block to load specific bytes of data from the input bytes memory.

The use of mload in this case may increase the gas cost of the function.

So we can modify `hexStringToBytes32` function in `HexUtils.sol` like this:
```
   function hexStringToBytes32(
        bytes memory str,
        uint256 idx,
        uint256 lastIdx
    ) internal pure returns (bytes32 r, bool valid) {
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
            valid := true
            for {
                let i := idx
            } lt(i, lastIdx) {
                i := add(i, 32)
            } {
                if eq(valid, false) {
                    break
                }
                let buffer := mload(add(ptr, i))
                let index := 32
                if gt(add(i, 32), lastIdx) {
                    index := sub(lastIdx, i)
                }
                for {
                    let j := 0
                } lt(j, index) {
                    j := add(j, 2)
                } {
                    let byte1 := getHex(byte(j, buffer))
                    let byte2 := getHex(byte(add(j, 1), buffer))
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
    }
```

