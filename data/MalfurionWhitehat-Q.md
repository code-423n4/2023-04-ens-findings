# QA-01 `DNSClaimChecker.parseString` reverts with unexpected reason if `str` contains less than 4 bytes

```
    function parseString(
        bytes memory str,
        uint256 idx,
        uint256 len
    ) internal pure returns (address, bool) {
        // TODO: More robust parsing that handles whitespace and multiple key/value pairs
        if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false); // 0x613d3078 == 'a=0x'
        return str.hexToAddress(idx + 4, idx + len);
    }
```

For example, attempting to claim the RRset `a=` will revert, as it contains only 2 bytes

Remediation: check if `len - idx >= 4` before calling `str.readUint32(idx)`


