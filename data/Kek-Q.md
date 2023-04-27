


# [L-1] Function [HexUtils.hexStringToBytes32()](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L11) returns inaccurate hex Bytes32 for odd length `str`

[HexUtils.hexStringToBytes32()](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L11) evaluates bytes 2 at a time [HexUtils.sol#L47](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L47). Due to this, the output of a conversion from hexStringtoBytes32() may return invalid results. The current implementation of this protocol is unaffected as all instances of this function's use is with even length ETH addresses. 

The comment above the function + function name make no indication that the length of the input `str` should contain an even number of bytes.

## POC

Good input (even # of bytes for `str`)

```text
input: 0x6161,0,2

output:
bytes32: 0x00000000000000000000000000000000000000000000000000000000000000aa
bool: valid true
```

Bad input (odd # of bytes for `str`)

```text
input: 0x616161,0,3

output:
bytes32: 0x00000000000000000000000000000000000000000000000000000000000000aa
bool: valid false
```

expected output should have been:

```text
input: 0x616161,0,3

output:
bytes32: 0x0000000000000000000000000000000000000000000000000000000000000aaa
bool: valid true
```

## Reccomendation

It is suggested to evaluate one bytes at a time. The current implementation may cause issues if the protocol is upgraded in the future and this function is used in other contexts.
