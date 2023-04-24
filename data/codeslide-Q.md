### Low Risk Issues List

| Number | Issue                   | Instances |
| :----: | :---------------------- | :-------: |
| [L-01] | Unsafe casting of uints |     3     |

#### [L-01] Unsafe casting of uints

Downcasting from uint256 in Solidity does not revert on overflow. This can result in undesired exploitation or bugs, since developers usually assume that overflows raise errors. [OpenZeppelin's SafeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) restores this intuition by reverting the transaction when such an operation overflows. Using this library instead of the unchecked operations eliminates an entire class of bugs, so it’s recommended to always use it.

For example:

```solidity
// Before
return (address(uint160(uint256(r))), valid);

// After
return (address(toUint160(uint256(r))), valid);
```

```solidity
File: contracts/utils/HexUtils.sol

75:    return (address(uint160(uint256(r))), valid);
```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

166:    if (!RRUtils.serialNumberGte(uint32(now), rrset.inception)) {
167:         revert SignatureNotValidYet(rrset.inception, uint32(now));
```

### Non Critical Issues

| Number  | Issue                            | Instances |
| :-----: | :------------------------------- | :-------: |
| [NC-01] | Event is missing indexed fields  |     3     |
| [NC-02] | Magic numbers                    |    50+    |
| [NC-03] | Naming conventions for constants |     7     |
| [NC-04] | Missing check for address(0)     |     2     |

#### [NC-01] Event is missing indexed fields

Indexing event fields make the fields more quickly accessible to off-chain tools that parse events. Each event should use three indexed fields if there are three or more fields. If there are fewer than three fields, all of the fields should be indexed.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

47:    event Claim(
48:        bytes32 indexed node,
49:        address indexed owner,
50:        bytes dnsname,
51:        uint32 inception
52:    );

53:    event NewPublicSuffixList(address suffixes);
```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

4:    event Debug(bytes32 x);
```

#### [NC-02] Magic numbers

Constants should be defined rather than using "magic" numbers.

```solidity
File: contracts/dnsregistrar/DNSClaimChecker.sol

72:    if (str.readUint32(idx) != 0x613d3078) return (address(0x0), false);
```

```solidity
File: contracts/dnssec-oracle/BytesUtils.sol

244:    require(idx + 20 <= self.length);

248:    0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF000000000000000000000000

337:    require(len <= 52);

343:    require(char >= 0x30 && char <= 0x7A);
344:    decoded = uint8(base32HexTable[uint256(uint8(char)) - 0x30]);
345:    require(decoded <= 0x20);

349:    ret = (ret << 5) | decoded;

352:    uint256 bitlen = len * 5;
        if (len % 8 == 0) {
            // Multiple of 8 characters, no padding
            ret = (ret << 5) | decoded;
        } else if (len % 8 == 2) {
            // Two extra characters - 1 byte
            ret = (ret << 3) | (decoded >> 2);
            bitlen -= 2;
        } else if (len % 8 == 4) {
            // Four extra characters - 2 bytes
            ret = (ret << 1) | (decoded >> 4);
            bitlen -= 4;
        } else if (len % 8 == 5) {
            // Five extra characters - 3 bytes
            ret = (ret << 4) | (decoded >> 1);
            bitlen -= 1;
        } else if (len % 8 == 7) {
            // Seven extra characters - 4 bytes
            ret = (ret << 2) | (decoded >> 3);
            bitlen -= 3;
```

```solidity
File: contracts/dnssec-oracle/RRUtils.sol

169:    off += 2;

171:    off += 2;

173:    off += 4;

177:    off += 2;

381:    require(data.length <= 8192, "Long keys not permitted");
```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA1Algorithm.sol

26:    exponentLen + 5,
27:    key.length - exponentLen - 5

30:    exponentLen = key.readUint16(5);
31:    exponent = key.substring(7, exponentLen);

33:    exponentLen + 7,
34:    key.length - exponentLen - 7
```

```solidity
File: contracts/dnssec-oracle/algorithms/ModexpPrecompile.sol

26:    5,
```

```solidity
File: contracts/dnssec-oracle/algorithms/RSASHA256Algorithm.sol

25:    exponentLen + 5,
26:    key.length - exponentLen - 5

29:    exponentLen = key.readUint16(5);
30:    exponent = key.substring(7, exponentLen);

32:    exponentLen + 7,
33:    key.length - exponentLen - 7
```

```solidity
File: contracts/dnssec-oracle/DNSSECImpl.sol

294:    if (dnskey.protocol != 3) {
```

```solidity
File: contracts/dnssec-oracle/SHA1.sol

9:    let scratch := mload(0x40)

17:   switch lt(sub(totallen, len), 9)

22:   let h := 0x6745230100EFCDAB890098BADCFE001032547600C3D2E1F0

47:   mstore8(add(scratch, sub(len, i)), 0x80)

63:   j := add(j, 12)

67:   mload(add(scratch, sub(j, 12))),

71:   mload(add(scratch, sub(j, 56))),

81:   div(temp, 0x80000000),

89:   } lt(j, 320) {
90:       j := add(j, 24)

94:   mload(add(scratch, sub(j, 24))),

98:   mload(add(scratch, sub(j, 112))),

108:  div(temp, 0x40000000),

120:  } lt(j, 80) {

123:  switch div(j, 20)

132:  k := 0x5A827999

141:  k := 0x6ED9EBA1

157:  k := 0x8F1BBCDC

166:  k := 0xCA62C1D6
```

```solidity
File: contracts/dnsregistrar/OffchainDNSResolver.sol

144:    if (txt.length < 5 || !txt.equals(0, "ENS1 ", 0, 5)) {

149:    uint256 lastTxtIdx = txt.find(5, txt.length - 5, " ");

151:    address dnsResolver = parseAndResolve(txt, 5, txt.length);

154:    address dnsResolver = parseAndResolve(txt, 5, lastTxtIdx);
```

#### [NC-03] Naming conventions for constants

Per [the Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html#constants), constants "should be named with all capital letters with underscores separating words."

```solidity
File: contracts/dnssec-oracle/algorithms/EllipticCurve.sol

21:    uint256 constant a =

23:    uint256 constant b =

25:    uint256 constant gx =

27:    uint256 constant gy =

29:    uint256 constant p =

31:    uint256 constant n =

34:    uint256 constant lowSmax =
```

#### [NC-04] Missing check for address(0)

Missing check for `address(0)` when assigning values to address state variables.

```solidity
File: contracts/dnsregistrar/DNSRegistrar.sol

62:    previousRegistrar = _previousRegistrar;
63:    resolver = _resolver;
```
