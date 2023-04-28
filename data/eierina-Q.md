(Total issues in this report: 8)

# [L-01] Valid hex string is not decoded correctly by hexStringToBytes32 and reads memory out-of-boundary

## Links
https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L11-L60

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L47-L50

## Impact
Valid hexadecimal strings are not decoded correctly.
Decoding reads out-of-bounds memory returning undefined/unpreditcable values.

At this time of writing I am not sure whether the undefined / unpredictable values may be predictably injected by an attacker and therefore and/or constitute a more serious security issue or just wrong / fail decoding.

## Proof of Concept
The [hexStringToBytes32](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L11-L60) function reads an input string and converts it to a bytes32 value.

"aaaa", "aaa", "aa", or "a" (or "AAAA", "AAA", "AA", or "A") are all valid hexadecimal values, but due to the way the [decoding loop](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/HexUtils.sol#L47-L50) is implemented, when the input string (or the selected part of it) is an odd number of characters, the decoding produces mixed, wrong, and undefined results.

Below is an example of decoding an odd number of characters out of an even number of characters and resulting in incorrect output (note that the idx and lastIdx are respectively the "cursor" start and end, therefore 0, 3 oi A to C, while 1, 4 is B to D).

```solidity

hexStringToBytes32(bytes("ABCD"), 0, 2); // produces (  0xab,  true) - correct
hexStringToBytes32(bytes("ABCD"), 1, 3); // produces (  0xbc,  true) - correct

hexStringToBytes32(bytes("ABCD"), 0, 3); // produces (0xabcd,  true) - incorrect
hexStringToBytes32(bytes("ABCD"), 1, 4); // produces (  0xbc, false) - incorrect

```

The first and second examples works as expected, but the third and fourth does not.

In the third example `hexStringToBytes32(bytes("ABCD"), 0, 3)` four characters are decoded instead of three, and the output is `0xabcd` instead of `0xabc` because each loop iteration reads two nibbles in sequence and then increment the index by two, so instead of stopping at "C" it also reads the non-expected "D" character and adds it to the result.

In the fourth example `hexStringToBytes32(bytes("ABCD"), 1, 4);` two characters are decoded instead of three, and the output is `0xbc` instead of `0xbcd` because each loop reads two nibbles in sequence per iteration and then increment the index by two, so instead of stopping at "D" it reads some extra value out-of-bounds and the value is undefined. In this case it happens to be some value that the getHex discards as non in the ascii range 0-9, A-F, a-f and therefore terminates the loop, but the result may vary.

## Tools Used
n/a

## Recommended Mitigation Steps
Correct the [hexStringToBytes32](https://github.com/code-423n4/2023-04-ens/blob/main/contracts/utils/HexUtils.sol#L11-L60) function by replacing it with the following:

```solidity
function hexStringToBytes32(bytes memory str, uint256 idx, uint256 lastIdx) internal pure returns (bytes32 r, bool valid) {
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

		for {
			let ptr := add(str, add(32, idx))
			let ptrEnd := add(str, add(32, lastIdx))
		} lt(ptr, ptrEnd) {
			ptr := add(ptr, 1)
		} {
			let nibble := getHex(byte(0, mload(ptr)))                
			if eq(nibble, 0xff) {
				valid := false
				break
			}
			r := or(shl(4, r), nibble)                
		}
	}
}
```

I would also add a stricter check to the indexes so that idx cannot be greater than lastIdx and also so that idx cannot be equal to lastIdx if we consider an empty string an invalid string.

```solidity
if or(gt(lastIdx, mload(str)), or(gt(idx, lastIdx), eq(idx, lastIdx))) {
	revert(0, 0)
}
```




# [L-02] readKeyValue fails to detect end of input

## Links

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L14-L40

## Impact

readKeyValue does not detect the end of the input string and offers an out-of-bonds `nextOffset`.

## Proof of Concept

If the function reads the one and only keyvalue in the input or reached the last keyvalue, the next `find` function will return type(uint256).max and therefore the [terminator](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L28) variable will be set to [input.length](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L34) and later [incremented by 1 in the assignment to nextOffset], effectively setting the nextOffset out-of-bonds.

## Tools Used
n/a

## Recommended Mitigation Steps

Signal the end of key-value(s) input parameter by returining type(uint256).max similarly to the find function.

```solidity
nextOffset = terminator >= input.length ? type(uint256).max : terminator + 1;
```




# [L-03] Redundant and dangerous `len` parameter in readKeyValue

## Links

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L14-L40

## Impact
If the `len` is not set to input.length minus the offset, there may be unpredictable results due how the algorithm works.

## Proof of Concept

Let's consider the following three inputs that will be parsed by the readKeyValue with an offset of zero:
1. bytes("aaa=bbb")
2. bytes("aaa=bbb ")
3. bytes("aaa=bbb ccc=ddd")

The first input will be correctly and integrally parsed for any len that goes past the equal sign (len >= 4) because terminator is first set to type(uint256).max, then to input.length.

The second input will be correctly parsed only if the len is equal to the input.length (8), because otherwise the terminator is not found, and the [value](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnsregistrar/RecordParser.sol#L38) is retrieved by substring(input, offset:3 + 1, len:8 - 3 - 1) causing the value to be 0x62626220 instead of 0x626262.

The third input suffers the same problems as the first and second.

## Tools Used
n/a

## Recommended Mitigation Steps

Either remove the len parameter and calculate it internally as len = input.length - offset, or update the algorithm to be more robust.




# [L-04] dnsEncodeName can have node hash collisions

## Links

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L9-L51

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L36

## Impact

The [dnsEncodeName](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L9-L51) may encode multiple different names that have the same/colliding node hash.
Moreover the label length of a non-zero length label may be set to zero.

## Proof of Concept

The [dnsEncodeName](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L9-L51) function has an encoding loop that runs inside an unchecked block. The labelLength is [incremented](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L36) at each step, and [reset](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L34) when a "." is encountered. Since there is no label length check and the labelLength increment is in an unchecked block, passing a 256 long label will set the labelLength to zero.

Let's take for example two names where the label lengts are 256.3 like the following two.

```
abcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijxxxxxx.eth
```

```
abcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijabcdefghijyyyyyy.eth
```

Only the first part is different because the first one ends with x, the second one ends with y.

When looping backwards, first "eth" will be parsed and we'll get 

➜ bytes32 node
➜ node = keccak256(abi.encodePacked(bytes32(0), keccak256(bytes("eth"))))
Type: bytes32
└ Data: 0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4ae

then the 256 bytes long label will be parsed with labelLength starting at 0, and overflowing back to 0 at the 256th increment, since labelLength is uint8. We then have:

➜ node = keccak256(abi.encodePacked(node, keccak256(bytes(""))))
Type: bytes32
└ Data: 0x8cc9f31a5e7af6381efc751d98d289e3f3589f1b6f19b9b989ace1788b939cf7

Therefore the [dnsEncodeName](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/utils/NameEncoder.sol#L9-L51) function will return a node hash of 0x8cc9f31a5e7af6381efc751d98d289e3f3589f1b6f19b9b989ace1788b939cf7 for both the two names in the example.

Result {
  'dnsName': '0x006162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a7878787878780365746800',
  'node': '0x8cc9f31a5e7af6381efc751d98d289e3f3589f1b6f19b9b989ace1788b939cf7'
}

Result {
  'dnsName': '0x006162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a6162636465666768696a7979797979790365746800',
  'node': '0x8cc9f31a5e7af6381efc751d98d289e3f3589f1b6f19b9b989ace1788b939cf7'
}

In the two examples above the 'node' has is the same: 0x8cc9f31a5e7af6381efc751d98d289e3f3589f1b6f19b9b989ace1788b939cf7

The label length of the two 256 characters long labels in dnsName(s) is set to 0.

Still progressing through contracts in scope, therefore I cannot tell yet if there are any other related or major security concerns.

## Tools Used
n/a

## Recommended Mitigation Steps

Add a length limit to each label and/or update the labelLength type if necessary.




# [L-05] Valid base32Hex input can crash base32hex decoder

## Links
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L332-L377

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L322-L323

https://datatracker.ietf.org/doc/html/rfc4648#page-10

## Impact
Valid base32Hex input is not decoded as expected and instead crashes the decoder.

## Proof of Concept
The [RFC-4648](https://datatracker.ietf.org/doc/html/rfc4648#page-10) specifies *Base 32 Encoding with Extended Hex Alphabet* and includes some [Test Vectors](https://datatracker.ietf.org/doc/html/rfc4648#page-13). The Test Vectors in the RFC crash the [base32HexDecodeWord](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L332-L377) function. The reason is that the encoded padding is not decoded, and instead maps to 0xFF in  the [base32HexTable](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L322-L323).

While the [base32HexDecodeWord](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L332-L377) function correctly add paddign based on the remainder of the modulo 8 of the input length, valid padding characters at the end of the input should not crash the decoder.

Eventually, if the padding is a concern in sorting encoded strings, the concern should be either handled in the sorting function or by input sanitization.

Reference table for *Base 32 Encoding with Extended Hex Alphabet* Value/Encoding from [RFC-4648](https://datatracker.ietf.org/doc/html/rfc4648#page-10) 
|Value|Encoding|Value|Encoding|Value|Encoding|Value|Encoding|
|----:|:-------|----:|:-------|----:|:-------|----:|:-------|
|0    |0       | 9   |9       |18   |I       | 27  |R       |
|1    |1       |10   |A       |19   |J       | 28  |S       |
|2    |2       |11   |B       |20   |K       | 29  |T       |
|3    |3       |12   |C       |21   |L       | 30  |U       |
|4    |4       |13   |D       |22   |M       | 31  |V       |
|5    |5       |14   |E       |23   |N       |     |        |
|6    |6       |15   |F       |24   |O       |(pad)|=       |
|7    |7       |16   |G       |25   |P       |     |        |
|8    |8       |17   |H       |26   |Q       |     |        |


## Tools Used
n/a

## Recommended Mitigation Steps

Modify  the [base32HexTable](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L322-L323) so that it returns 0x00 when the input characted is '=' ( 0x3D) and add a flag in the decode loop that prevents non-padding valid characters after the first padding character is encountered.

```
-- hex"00010203040506070809FFFFFFFFFFFFFF0A..."
++ hex"00010203040506070809FFFFFF00FFFFFF0A...";
##                               ^^
```




# [L-06] Base32hex decoder is case insensitive on input potentially allowing "sorting attacks" and similar

## Links
https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L332-L377

https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L322-L323

https://datatracker.ietf.org/doc/html/rfc4648#page-10

## Impact
The base32Hex implementation of the decoder is case insensitive which can be used to leak information or make string equality comparisons fail or implement sort attacks. If any of these is a concern, then the issue should be fixed.

## Proof of Concept
The [RFC-4648](https://datatracker.ietf.org/doc/html/rfc4648#page-10) specifies *Base 32 Encoding* and *Base 32 Encoding with Extended Hex Alphabet*. This encoding is designed with a sortability property, which means that the encoded values can be sorted alphabetically and still maintain the correct order of the original data. Since the specification defines the encoding output 0-9, A-V with A-V UPPERCASE only, the assumption "encoded values can be sorted alphabetically and still maintain the correct order of the original data" does not hold true.

From [section 12 of the RFC](https://datatracker.ietf.org/doc/html/rfc4648#section-12):

>Similarly, when the base 16 and base 32 alphabets are handled case
>insensitively, alteration of case can be used to leak information or
>make string equality comparisons fail.

In section 6 *Base 32 Encoding* the encoding table is introduced by the following text where it is stated that  the encoding output US-ASCII digits and **UPPERCASE** letters.

>Each 5-bit group is used as an index into an array of 32 printable
>characters.  The character referenced by the index is placed in the
>output string.  These characters, identified in Table 3, below, are
>selected from US-ASCII digits and uppercase letters.
>
>Table 3: The Base 32 Alphabet

In section 7 *Base 32 Encoding with Extended Hex Alphabet* the extended table is introduced by saying that the encoding is identical except for the alphabet.
   
>This encoding is identical to the previous one, except for the
>alphabet.  The new alphabet is found in Table 4.
>Table 4: The "Extended Hex" Base 32 Alphabet

|Value|Encoding|Value|Encoding|Value|Encoding|Value|Encoding|
|----:|:-------|----:|:-------|----:|:-------|----:|:-------|
|0    |0       | 9   |9       |18   |I       | 27  |R       |
|1    |1       |10   |A       |19   |J       | 28  |S       |
|2    |2       |11   |B       |20   |K       | 29  |T       |
|3    |3       |12   |C       |21   |L       | 30  |U       |
|4    |4       |13   |D       |22   |M       | 31  |V       |
|5    |5       |14   |E       |23   |N       |     |        |
|6    |6       |15   |F       |24   |O       |(pad)|=       |
|7    |7       |16   |G       |25   |P       |     |        |
|8    |8       |17   |H       |26   |Q       |     |        |


## Tools Used
n/a

## Recommended Mitigation Steps

Either modify  the [base32HexTable](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L322-L323) so that it returns 0xFF when lowercase a-v characters are detected, or restrict the valid input range in [require(char >= 0x30 && char <= 0x7A);](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L343) to require(char >= 0x30 && char <= 0x56);

The testBase32HexDecodeWord test in TestBytesUtils.sol will have to be updated to use uppercase characters.

# [NC-01] Incorrect bounds for decoded value

In BytesUtils' base32HexDecodeWord, the decode table max value is 0x1F, therefore the [require(decoded <= 0x20)](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L345) should be replaced with require(decoded < 0x20)

# [NC-02] Out-of-bounds input range for decode table

In BytesUtils' base32HexDecodeWord, the decode table [base32HexTable](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L322-L323) contains 71 indexable values.

➜ bytes(hex"00010203040506070809FFFFFFFFFFFFFF0A0B0C0D0E0F101112131415161718191A1B1C1D1E1FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF").length
Type: uint
├ Hex: 0x47
└ Decimal: 71

[Bounds](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L343-L344) are checked before accessing the table, but the math is wrong.

```solidity
            require(char >= 0x30 && char <= 0x7A);
            decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);
```

if char is 0x7A or 'z', then the table is accessed at the index 0x7A - 0x30 which is 0x4A or 74 while the maximum index should be 0x46 or 70.

Replace the bounds check with require(char >= 0x30 && char <= 0x46);


max value is 0x1F, therefore the [require(decoded <= 0x20)](https://github.com/code-423n4/2023-04-ens/blob/45ea10bacb2a398e14d711fe28d1738271cd7640/contracts/dnssec-oracle/BytesUtils.sol#L345) should be replaced with require(decoded < 0x20)
