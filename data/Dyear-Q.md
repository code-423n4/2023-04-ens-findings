## Specify the scope of the using library.
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/digests/SHA1Digest.sol#L11
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/digests/SHA256Digest.sol#L10
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/RRUtils.sol#L10

```
using BytesUtils for bytes;
```

Like this 
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L7

## When RecordParser.sol read a input don't have separator, Using revert instead of return is better because it expresses the meaning more clearly and saves gas

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L25

Can define error `MisSeparator`, If don't have "=" separator, can revert the `MisSeparator`  error.

You can use the way of comparing the return value  `nextOffset` with the length of `input` to determine whether the Parses is over.

```solidity

library RecordParser {
    using BytesUtils for bytes;

    error MisSeparator();

    /**
     * @dev Parses a key-value record into a key and value.
     * @param input The input string
     * @param offset The offset to start reading at
     */
    function readKeyValue(bytes memory input, uint256 offset, uint256 len)
        internal
        pure
        returns (bytes memory key, bytes memory value, uint256 nextOffset)
    {
        uint256 separator = input.find(offset, len, "=");
        if (separator == type(uint256).max) {
            // return ("", "", type(uint256).max); //@audit can use revert here
            revert MisSeparator();
        }


        uint256 terminator = input.find(separator, len + offset - separator, " ");
        if (terminator == type(uint256).max) {
            terminator = input.length;
        }

        key = input.substring(offset, separator - offset);
        value = input.substring(separator + 1, terminator - separator - 1);
        nextOffset = terminator + 1;
    }
}

```

