1: Three ways to reduce gas in DNSClaimChecker.sol

https://github.com/code-423n4/2023-04-ens/blob/main/contracts/dnsregistrar/DNSClaimChecker.sol

-Avoid unnecessary storage operations: The Buffer library creates a new buffer for each call to getOwnerAddress. Since the Buffer is only used to construct the buf variable, which is a memory variable, we can replace the Buffer with a memory array.
-Optimize loop variables: The parseRR function contains a loop that increments the idx variable by 1 and then adds len to it. This can be optimized by incrementing idx by len + 1 in each iteration of the loop.
-Remove unnecessary variables: The found variable in the parseString function is unnecessary since the function always returns a boolean value. We can remove the found variable and return false directly when the string does not start with "a=0x".