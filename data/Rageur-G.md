## GAS-1: <X> * 2 costs more gas then <X> << 1

### Description

Using bitwize operator instead of '*' or '/' saves gas.

### Affected file

* RRUtils.sol (Line: 390)

## GAS-2: <X> <= <Y> costs more gas than <X> < <Y> + 1

### Description

In Solididy, the opcode 'less or equal' doesn't exist. So the EVM will translate this by two distinct operation, first the inferior, and then the equal which cost more gas then a strict less.

### Affected file

* BytesUtils.sol (Line: 18, 87, 196, 212, 228, 244, 265, 266, 275, 305, 337, 343, 345)
* EllipticCurve.sol (Line: 392)
* NameEncoder.sol (Line: 25)
* RRUtils.sol (Line: 151, 160, 337, 381)

## GAS-3: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

### Affected file

* BytesUtils.sol (Line: 179, 192, 208, 224, 240, 260, 300, 332, 387)
* DNSClaimChecker.sol (Line: 19)
* EllipticCurve.sol (Line: 316, 377, 386)
* HexUtils.sol (Line: 68)
* ModexpPrecompile.sol (Line: 7)
* NameEncoder.sol (Line: 9)
* RRUtils.sol (Line: 94, 111, 150, 187, 200, 222, 248, 259, 275, 332, 353)
* RSAVerify.sol (Line: 14)
* RecordParser.sol (Line: 14)
* SHA1.sol (Line: 6)

## GAS-4: Not using the named return variables when a function returns, wastes deployment gas

### Description

It is not necessary to have both a named return and a return statement.

### Affected file

* BytesUtils.sol (Line: 182)
* DNSRegistrar.sol (Line: 166, 177, 177)
* DNSSECImpl.sol (Line: 95, 116, 144)
* EllipticCurve.sol (Line: 110, 117, 127, 127, 163, 215, 215, 215, 215, 339, 339, 339, 339)
* NameEncoder.sol (Line: 11, 11)
* RRUtils.sol (Line: 44)
* RecordParser.sol (Line: 22)

## GAS-5: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* DNSRegistrar.sol (Line: 73)

## GAS-6: Should use arguments instead of state variable

### Description

Using function's parameter cost less gas then a state variable.

### Affected file

* DNSRegistrar.sol (Line: 66, 82)

## GAS-7: The result of a function call should be cached rather than re-calling the function

### Description

External calls are expensive. Consider caching.

### Affected file

* DNSRegistrar.sol (Line: 180, 188)
* NameEncoder.sol (Line: 27, 49)
* RRUtils.sol (Line: 168, 170, 176)

## GAS-8: Use ```assembly``` to write address storage values

### Affected file

* DNSRegistrar.sol (Line: 62, 63, 64, 65, 67, 81)
* OffchainDNSResolver.sol (Line: 44, 45, 46)

## GAS-9: Use constants instead of type(uintx).max

### Description

It uses more gas in the distribution process and also for each transaction than constant usage.

### Affected file

* BytesUtils.sol (Line: 88, 398)
* RecordParser.sol (Line: 24, 25, 33)

## GAS-10: Use custom errors rather than revert()/require() strings to save gas

### Description

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

### Affected file

* P256SHA256Algorithm.sol (Line: 33, 40)
* RRUtils.sol (Line: 381)
* SHA1Digest.sol (Line: 17)
* SHA256Digest.sol (Line: 16)

## GAS-11: Use nested if and avoid multiple check combinations

### Description

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

### Affected file

* EllipticCurve.sol (Line: 128)
* OffchainDNSResolver.sol (Line: 178)

## GAS-12: Using immutable on variables that are only set in the constructor and never after (2.1k gas per var)

### Description

Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas).

### Affected file

* DNSRegistrar.sol (Line: 26, 27, 29)
* OffchainDNSResolver.sol (Line: 37, 38, 39)

## GAS-13: Using private rather than public for constants saves gas

### Description

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

### Affected file

* BytesUtils.sol (Line: 322)
* DNSClaimChecker.sol (Line: 16, 17)
* DNSSECImpl.sol (Line: 27, 29, 30, 32)
* EllipticCurve.sol (Line: 21, 23, 25, 27, 29, 31, 34)
* RRUtils.sol (Line: 72, 73, 74, 75, 76, 77, 78, 79, 210, 211, 212, 213, 236, 237, 238, 239)

## GAS-14: With assembly, ```.call (bool success)``` transfer can be done gas-optimized

### Description

```return``` data ```(bool success,)``` has to be stored due to EVM architecture, but in a usage in assembly, 'out' and 'outsize' values are given (0,0), this storage disappears and gas optimization is provided.

### Affected file

* OffchainDNSResolver.sol (Line: 122)