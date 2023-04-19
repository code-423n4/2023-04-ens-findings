1: Three ways to reduce gas in DNSClaimChecker.sol

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

-Avoid unnecessary storage operations: The Buffer library creates a new buffer for each call to getOwnerAddress. Since the Buffer is only used to construct the buf variable, which is a memory variable, we can replace the Buffer with a memory array.
-Optimize loop variables: The parseRR function contains a loop that increments the idx variable by 1 and then adds len to it. This can be optimized by incrementing idx by len + 1 in each iteration of the loop.
-Remove unnecessary variables: The found variable in the parseString function is unnecessary since the function always returns a boolean value. We can remove the found variable and return false directly when the string does not start with "a=0x".

2:Use a single substring function call to extract both the exponent and modulus values from the key input, rather than using separate calls.
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

The verify function in the RSASHA1Algorithm contract extracts the exponent and modulus values from the key input using two separate substring function calls:
if (exponentLen != 0) {
    exponent = key.substring(5, exponentLen);
    modulus = key.substring(
        exponentLen + 5,
        key.length - exponentLen - 5
    );
} else {
    exponentLen = key.readUint16(5);
    exponent = key.substring(7, exponentLen);
    modulus = key.substring(
        exponentLen + 7,
        key.length - exponentLen - 7
    );
}
This results in two separate byte array allocations, which can be expensive in terms of gas costs.

To optimize this code and reduce gas costs, you can use a single substring function call to extract both the exponent and modulus values from the key input. Here's how you can modify the code to achieve this:
uint16 offset = 5;
if (uint16(key.readUint8(offset)) == 0) {
    offset += 2;
}
uint16 exponentLen = uint16(key.readUint8(offset));
exponent = key.substring(offset + 1, exponentLen);
modulus = key.substring(offset + exponentLen + 1, key.length - offset - exponentLen - 1);

Here, we first initialize the offset variable to 5, which is the starting position of the exponent length field in the key input. We then check if the exponent length field is encoded as a single byte or a two-byte value, and adjust the offset accordingly.
Next, we extract the exponent value by calling substring with the starting position set to offset + 1 and the length set to exponentLen.
Finally, we extract the modulus value by calling substring with the starting position set to offset + exponentLen + 1 and the length set to key.length - offset - exponentLen - 1.
By using a single substring function call instead of two separate calls, we can reduce the number of byte array allocations and potentially save on gas costs.




