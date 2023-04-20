# 1 - Inapropriate use of assert in readTXT()

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L162

the readTXT() function uses an assert statement to check if the start index plus the field length is less than the last index. In Solidity, assert statements should only be used to check for internal errors and not for validating external inputs. Instead, use a require statement for better error handling and to prevent unexpected contract termination. 

Replace the following line:https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/OffchainDNSResolver.sol#L162

```
assert(startIdx + fieldLength < lastIdx);
```
with:
```
require(startIdx + fieldLength < lastIdx, "Invalid TXT record");
```

# 2 - Unexpected input handling: The readKeyValue() function does not handle unexpected inputs very gracefully

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/RecordParser.sol#L14

If there is no equal sign ("=") in the input, it returns an empty key-value pair and a maximum uint256 value for the nextOffset. While this may not directly lead to a vulnerability, it could result in unexpected behavior when using the library, especially if the calling contract does not validate the output properly.

To address this issue, you may consider adding input validation checks and returning appropriate error messages for malformed inputs. For example, you can add a custom error message to handle cases when there is no equal sign in the input:

```
error MalformedInput(string message);

// ...

if (separator == type(uint256).max) {
    revert MalformedInput("No equal sign found in input.");
}
```


